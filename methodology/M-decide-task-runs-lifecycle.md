---
id: alice-methodology-M-decide-task-runs-lifecycle
created: 2026-09-06T17:45:00Z
updated: 2026-09-06T17:45:00Z
title: "Methodology M — Decide task_runs lifecycle (the durable rule for closing run rows on task state transition)"
type: methodology
status: draft
version: 0.1.0
amended_by: []
source: alice-framework
tags: [kind:methodology, kind:task-runs, kind:lifecycle, kind:orphan, kind:reaper, kind:state-transition, kind:durable-fix, project:alice]
confidence: 0.9
alice-ticket: t_60854c77
companion: ["[[methodology/M-decide-proactive-board-monitoring.md]]", "[[methodology/04b-decide-board-routing.md]]", "[[methodology/05-op-guards.md]]"]
links: ["[[methodology/00-decide-ticket-naming.md]]", "[[methodology/M-decide-instance-vs-framework.md]]"]
---

# Methodology M — Decide task_runs lifecycle (v0.1.0)

> **A kanban task state transition (running|ready|blocked → done|archived) MUST close every associated `task_runs` row with `ended_at=now` and a semantic `outcome`.** This methodology codifies the durable rule for the run-row lifecycle in `kanban_db.py`: (1) the post-transition reaper hook fires for every state transition, (2) the orphan-reaper detects and closes runs whose `current_run_id` is already NULL but `ended_at IS NULL`, (3) the 4-field reaper schema for any retroactive reap (Scope / Owner / Timeline / Rationale, per op-guard-17). Without a canonical rule + an automated check, run rows outlive their parent tasks (verified case: 2026-09-06, `t_60854c77`) and board-status tooling reports misleading "N workers running" signals that don't reflect reality.

## Part 1: Why this methodology exists

A kanban task has two parallel state machines: the `tasks.status` column (the operator-visible state — `todo`, `ready`, `running`, `blocked`, `review`, `done`, `archived`, `triage`) and the `task_runs` table (the run-row history — each attempt by a worker to complete the task). These two state machines MUST stay in sync: when a task transitions to a terminal state, every run row that was active for that task must be closed.

The pattern hit on 2026-09-06 (`t_60854c77`): the `board-stale-check` cron ran at 2026-09-06 17:21 UTC against all 8 active kanban DBs and found 24 orphan `task_runs` rows across 4 boards (hermes: 10, loop-builder: 2, agent-resources: 2, patchwork: 10) — all with `status IN ('running', 'claimed') OR ended_at IS NULL`, all with dead PIDs (verified via `os.kill(pid, 0)`), and all with parent tasks that had already transitioned to `done` or `archived`. The board-status tooling (cron-output scrapes, dashboard WS consumers, third-party health checks) saw these stale `running` rows and reported misleading "N workers running" signals.

Root cause: the dispatcher's `_end_run` helper (per `hermes_cli/kanban_db.py::complete_task` and `archive_task`) only operates on the run row currently pointed at by `tasks.current_run_id`. When the parent task transitions to a terminal state via a path that does NOT call `_end_run` (e.g., the canonical-terminal auto-complete path at `kanban_db.py:14612`, the canonical-blocked backstop at `kanban_db.py:14667`, or any future terminal-state path that doesn't go through `_end_run`), the run row is left orphaned: `tasks.current_run_id` gets set to NULL without the corresponding `task_runs.ended_at` getting set. The run row then sits in the table forever, contributing to the misleading "N running" count.

This is the canonical surface for the rule: the state-transition path is the entry point; without it, every future terminal-state path inherits the orphan bug. The post-transition reaper is the second surface; without it, existing orphans accumulate without bound. The migration helper is the third; without it, the existing 24 orphans (and any that accumulate before the fix lands) require manual SQL cleanup.

**Three failure modes this methodology prevents:**

1. **Orphan run rows from non-`_end_run` terminal paths.** A terminal-state transition path that doesn't go through `_end_run` leaves the run row in `ended_at IS NULL` + `current_run_id IS NULL` state. The dispatcher sees it on the next scan, reports it as a "running" worker, and never reaps it. The durable fix is: the post-transition reaper hook fires on EVERY state transition, not just the ones that go through `_end_run`. The hook reaps any `task_runs` row for the task that still has `ended_at IS NULL`, regardless of how it got that way.

2. **Backlog of historical orphans.** Run rows that became orphans before the fix landed (the canonical case: 24 rows across 4 boards) accumulate without bound unless explicitly reaped. The durable fix is: a one-shot migration script that reaps all historical orphans in a single transaction, with the 4-field reaper schema (Scope / Owner / Timeline / Rationale, per op-guard-17) for the audit trail.

3. **Misleading board-status reports.** A cron-output scraper or dashboard WS consumer that reads `task_runs WHERE ended_at IS NULL` sees N "in-flight" workers when the real number is 0 (all PIDs are dead, all parents are terminal). The operator's mental model of board health is wrong; they chase ghosts. The durable fix is: the post-transition reaper + the one-shot migration both close the orphan rows, restoring the truthful state.

## Part 2: The post-transition reaper hook (canonical, op-guard-17)

**Every state transition in `kanban_db.py` MUST close every associated `task_runs` row.** The post-transition reaper hook is a single helper function (`_reap_orphan_runs(conn, task_id)`) that fires for every state transition (not just the ones that go through `_end_run`) and reaps any `task_runs` row for the task that still has `ended_at IS NULL`. The hook is invoked at the END of every state-transition path, AFTER the main transition UPDATE + the `_end_run` call (if any) + the event append.

### Hook contract (canonical)

```python
def _reap_orphan_runs(conn: sqlite3.Connection, task_id: str) -> int:
    """Close any task_runs rows for ``task_id`` whose ended_at is still NULL.

    Fires on EVERY state transition (not just the ones that go through
    ``_end_run``). Catches orphans that accumulate via non-``_end_run``
    terminal paths (canonical-terminal auto-complete, canonical-blocked
    backstop, future state-transition paths). Returns the count of
    reaped rows; emits a ``run_row_orphan_reaped`` event for each.

    Per ``methodology/M-decide-task-runs-lifecycle.md`` v0.1.0.
    """
    now = int(time.time())
    rows = conn.execute(
        "SELECT id, status, outcome FROM task_runs "
        "WHERE task_id = ? AND ended_at IS NULL",
        (task_id,),
    ).fetchall()
    if not rows:
        return 0
    for row in rows:
        conn.execute(
            "UPDATE task_runs SET "
            "  status = 'reaped_orphan', "
            "  outcome = COALESCE(outcome, 'reaped_orphan'), "
            "  ended_at = ?, "
            "  summary = COALESCE(summary, 'orphan run row reaped on state transition'), "
            "  error = COALESCE(error, 'orphan: parent task transitioned to terminal state'), "
            "  claim_lock = NULL, "
            "  claim_expires = NULL, "
            "  worker_pid = NULL "
            "WHERE id = ? AND ended_at IS NULL",
            (now, row["id"]),
        )
        _append_event(
            conn, task_id, "run_row_orphan_reaped",
            {"run_id": row["id"], "previous_status": row["status"],
             "previous_outcome": row["outcome"]},
        )
    return len(rows)
```

### Invariants

- **Idempotent**: the hook's WHERE clause `ended_at IS NULL` ensures it's a no-op on already-reaped rows. Re-running the hook after a state transition is safe.
- **Auditable**: every reaped row emits a `run_row_orphan_reaped` event with the previous status + outcome, so the audit trail is complete.
- **Cooperative with `_end_run`**: the hook fires AFTER `_end_run` for state transitions that go through `_end_run`. The hook is a no-op for those rows because `_end_run` already set `ended_at`. The hook is a safety net for state transitions that DON'T go through `_end_run`.
- **In-transaction**: the hook is called inside the same `write_txn` as the main state transition, so the reap is atomic with the transition. A crashed transition means the reap is also rolled back.

## Part 3: The 4-field reaper schema (retroactive, op-guard-17)

The post-transition reaper handles future orphans automatically. For historical orphans (the 24 rows that existed on 2026-09-06), the retroactive reaper requires a one-shot migration. The migration MUST document the 4-field reaper schema per op-guard-17:

| Field | Value |
|---|---|
| **Scope** | The 24 task_runs rows with `status IN ('running', 'claimed') OR ended_at IS NULL` across 4 boards (hermes: 10, loop-builder: 2, agent-resources: 2, patchwork: 10) |
| **Owner** | The dispatcher (the post-transition reaper hook reaps future orphans; the one-shot migration reaps historical orphans) |
| **Timeline** | The one-shot migration runs ONCE at 2026-09-06 17:45 UTC; the post-transition reaper fires on every future state transition |
| **Rationale** | The 24 rows are orphans because (a) the parent task transitioned to a terminal state via a path that didn't call `_end_run` (canonical-terminal auto-complete, canonical-blocked backstop) AND (b) the PIDs are dead (verified via `os.kill(pid, 0)`). The reaper sets `status='reaped_orphan', ended_at=now, outcome='reaped_orphan'`. |

### Migration command (canonical)

The one-shot migration is a single SQL statement per board (run inside a transaction for atomicity):

```sql
BEGIN;
UPDATE task_runs
   SET status = 'reaped_orphan',
       outcome = COALESCE(outcome, 'reaped_orphan'),
       ended_at = CAST(strftime('%s','now') AS INTEGER),
       summary = COALESCE(summary, 'orphan run row reaped by one-shot migration at 2026-09-06 17:45 UTC'),
       error = COALESCE(error, 'orphan: parent task transitioned to terminal state via non-_end_run path'),
       claim_lock = NULL,
       claim_expires = NULL,
       worker_pid = NULL
 WHERE task_id IN (
     SELECT id FROM tasks WHERE status IN ('done', 'archived')
 )
   AND ended_at IS NULL;
-- For tasks whose parent no longer exists (rare; e.g. the row's
-- task_id references a task that was hard-deleted), reap those too:
UPDATE task_runs
   SET status = 'reaped_orphan',
       outcome = 'reaped_orphan',
       ended_at = CAST(strftime('%s','now') AS INTEGER),
       summary = 'orphan run row reaped by one-shot migration (parent task missing)',
       error = 'orphan: parent task no longer exists',
       claim_lock = NULL,
       claim_expires = NULL,
       worker_pid = NULL
 WHERE task_id NOT IN (SELECT id FROM tasks)
   AND ended_at IS NULL;
COMMIT;
```

The migration MUST be run via the canonical helper script (`~/.hermes/scripts/reap_orphan_runs.py`) that:
1. Iterates the auto-discovered board allow-list (per `M-decide-proactive-board-monitoring.md`).
2. Runs the SQL above against each board's `kanban.db`.
3. Emits an audit-line per op-guard-11 for each board's reap count.
4. Returns the total count + per-board breakdown for the dispatcher-notifier surface.

## Part 4: Acceptance criteria (compliance-verifier child, per op-guard-17)

The methodology is shipped when:

1. **Alice doc lands** at `methodology/M-decide-task-runs-lifecycle.md` v0.1.0 (this file) with the 4-field reaper schema in Part 3.
2. **Instance fix lands** at `hermes_cli/kanban_db.py` adding `_reap_orphan_runs()` helper + invoking it from `complete_task`, `_complete_task_for`, `archive_task`, `release_from_review`, `block_task`, `recompute_ready`, and the canonical-terminal auto-complete path.
3. **Regression test** at `tests/hermes_cli/test_kanban_orphan_run_reaper.py` v0.1.0 with 5+ cases: (a) task→done transition via `_end_run` + post-transition reaper is a no-op, (b) task→done transition via canonical-terminal path reaps the orphan, (c) task→archived transition reaps the orphan, (d) cross-task close via `_complete_task_for` reaps the orphan, (e) historical orphan reaped via the one-shot migration script.
4. **One-shot migration** runs at 2026-09-06 17:45 UTC via `~/.hermes/scripts/reap_orphan_runs.py` and reaps all 24 historical orphans across 4 boards.
5. **Audit-line per op-guard-11** appended to `~/Documents/HermesVault/log.md` with the per-board reap count + the total.
6. **Compliance-verifier child** ticket (per op-guard-17) verifies after the fix that a fresh task→done transition via the canonical-terminal path closes the run row within 1 second.

## Cross-references

- `methodology/04b-decide-board-routing.md` — the multi-board workstream topology that creates the orphan-across-boards problem.
- `methodology/M-decide-proactive-board-monitoring.md` — the auto-discovered board allow-list that the migration script uses.
- `methodology/05-op-guards.md` — op-guard-15 (must-call-terminal) + op-guard-21 (chat-side orchestrator-only) are the durable-fix disciplines this methodology depends on.
- `hermes_cli/kanban_db.py::_end_run` — the original run-row closer; the post-transition reaper is the safety net for paths that don't go through `_end_run`.
- `hermes_cli/kanban_db.py::complete_task` — the in-scope `running|ready|blocked → done` transition; calls `_end_run` and (after the fix) `_reap_orphan_runs`.
- `hermes_cli/kanban_db.py::_complete_task_for` — the cross-task close path; calls `_end_run` on the target and (after the fix) `_reap_orphan_runs`.
- `hermes_cli/kanban_db.py::archive_task` — the `running|ready|blocked → archived` transition; calls `_end_run` and (after the fix) `_reap_orphan_runs`.
- `hermes_cli/kanban_db.py::release_from_review` — the verifier PASS path; calls `_end_run` and (after the fix) `_reap_orphan_runs`.
- `hermes_cli/kanban_db.py::block_task` — the `running|ready|blocked → blocked` transition; calls `_end_run` and (after the fix) `_reap_orphan_runs`.
- `hermes_cli/kanban_db.py:14612` — the canonical-terminal auto-complete path; this is the path that creates orphans (it does NOT call `_end_run`). The fix adds `_reap_orphan_runs` here.
- `hermes_cli/kanban_db.py:14667` — the canonical-blocked backstop; same shape, same fix.
- `~/.hermes/scripts/reap_orphan_runs.py` — the one-shot migration helper (new, ships with the fix).
- `t_60854c77` — the source ticket for this methodology; verified state at 2026-09-06 17:21 UTC; 24 orphans across 4 boards; all PIDs dead; all parents terminal.
- `2-ATOMIC/rules/op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md` — alice-first / instance-second / compliance-gate; this methodology ships before the instance fix.
- `2-ATOMIC/rules/op-guard-19-pre-verify-artifact-state-2026-08-08.md` — pre-verify artifact state; the 24 orphans were verified via `os.kill(pid, 0)` + `SELECT tr.id, tr.task_id, ... FROM task_runs tr LEFT JOIN tasks t ON t.id = tr.task_id WHERE tr.status IN ('running', 'claimed') OR tr.ended_at IS NULL`.
- `2-ATOMIC/rules/op-guard-11-log-md-append-only-2026-07-22.md` — the audit-line protocol for the migration + reaper events.

## Audit-line

`## [2026-09-06T17:45Z] M-decide-task-runs-lifecycle-v0.1.0-shipped — methodology file lands at alice-framework/methodology/M-decide-task-runs-lifecycle.md v0.1.0; codifies post-transition reaper hook (`_reap_orphan_runs`) + 4-field retroactive reaper schema (Scope / Owner / Timeline / Rationale) per op-guard-17. Source: t_60854c77 (24 orphan task_runs across 4 boards detected 2026-09-06 17:21 UTC).`
