---
id: alice-methodology-M-decide-proactive-board-monitoring
created: 2026-08-11T23:55:00Z
updated: 2026-08-12T00:30:00Z
title: "Methodology M — Decide proactive board monitoring (the durable rule for unblocking stuck tickets without operator prompting)"
type: methodology
status: draft
version: 0.1.1
amended_by: ["t_de5e2168 — auto-discovery scope (all boards, not 9)"]
source: alice-framework
tags: [kind:methodology, kind:proactive-monitoring, kind:stuck-ticket, kind:auto-act, kind:gate, kind:durable-fix, kind:auto-discovery, project:alice]
confidence: 0.9
alice-ticket: t_593d077e
companion: ["[[methodology/04b-decide-board-routing.md]]", "[[methodology/06b-decide-blocked-ticket-recovery.md]]", "[[methodology/06-iteration-loop.md]]", "[[methodology/05-op-guards.md]]"]
links: ["[[methodology/00-decide-ticket-naming.md]]", "[[methodology/M-decide-instance-vs-framework.md]]"]
---

# Methodology M — Decide proactive board monitoring (v0.1.1)

> **A multi-board system MUST monitor every active board; the chat-side agent MUST surface and act on stuck tickets without operator prompting.** This methodology codifies the durable rule for proactive monitoring across a multi-board workstream topology: (1) the auto-discovered board allow-list (no fixed count), (2) the chat-side preflight scan, (3) the auto-act policy by block-kind, and (4) the operator-action surface. Without a canonical rule + an automated check, boards slip through monitoring (verified case: 2026-08-11, `t_593d077e`) and the operator catches blocked tickets by accident rather than by design.

## Part 1: Why this methodology exists

A multi-board workstream topology (the canonical pattern in `methodology/04b-decide-board-routing.md`) means work lives across N boards (hermes, patchwork, alice-framework, etc.), each with its own dispatcher loop and worker pool. The operator + chat-side agent + cron-side monitors must answer "is this board healthy?" for every active board on a continuous cadence.

The pattern hit on 2026-08-11 (`t_593d077e`): operator flagged that the chat-side agent was NOT proactively monitoring the `alice-framework` board — 4 tickets sat in `blocked` for 30+ minutes before the operator prompted the orchestrator. Root cause: the canonical board-allow-list in the shared scan helper (`_stuck_detect_common.py::BOARDS`) was missing `alice-framework`. The cron's most-recent tick reported 8 boards; the operator's actual work was on the 9th, where the silent gap was. The v0.1.0 fix added `alice-framework` to the list; the v0.1.1 fix (this version, `t_de5e2168`) replaces the fixed-count allow-list with auto-discovery so the silent gap cannot recur when the next board is added.

This is the canonical surface for the rule: the board-allow-list is the entry point; without it, no scan covers the board. The chat-side preflight hook is the second surface; without it, the operator's chat turns don't surface stuck tickets. The auto-act policy is the third; without it, the surface reports tickets the chat-side agent cannot unblock.

**Three failure modes this methodology prevents:**

1. **Silent board-coverage gap.** A new board is added but not included in the canonical allow-list. The cron tick reports all "active" boards; the new board is missing. Operators discover blocked tickets by accident. The v0.1.0 durable fix was: every board added to the topology MUST be added to the allow-list in the same change set (paired-wiki per op-guard-5). The v0.1.1 durable fix is stronger: the allow-list is auto-discovered from `~/.hermes/kanban/boards/` at runtime, so the gap cannot recur regardless of which board is added. The `9 → all-boards` scope is enforced by code, not by a checklist that drifts.
2. **Surface without action.** A scanner detects a stuck ticket, writes to a bridge file or a Discord DM, and stops. The recipient (operator or chat-side agent) cannot move the work forward without a follow-up tool call. The durable fix is: the auto-act policy classifies block-kind by what the agent can resolve vs what requires operator input.
3. **Cron-only cadence.** A scanner fires every 5 minutes. Blocked tickets sit for 5+ minutes before the operator sees them. The durable fix is: the chat-side preflight hook runs on every chat turn (cheap, <2s scan) so the surface catches up to the operator's pace.

## Part 2: The auto-discovered board allow-list (v0.1.1)

**Every active board in the workstream topology MUST be auto-discovered by every scanner.** The list is computed at runtime from `~/.hermes/kanban/boards/<slug>/`; no scanner maintains a parallel literal. A new board added to the topology is covered by every scanner on the next tick without code changes.

### Discovery contract (canonical, op-guard-17)

A board is "active" if and only if its directory under `~/.hermes/kanban/boards/<slug>/` has EITHER:

- A `board.json` manifest (the canonical registered form), OR
- A `kanban.db` SQLite file (the runtime form; the dispatcher creates this on first task).

A directory with neither is ignored. A directory with both is included once (the manifest is the canonical form, but presence of either is the discovery gate). The discovery function sorts results alphabetically so the order is deterministic; the actual order is not load-bearing (the scanner iterates all entries).

### Why auto-discovery (not a literal)

The v0.1.0 design was a literal allow-list in `~/.hermes/scripts/_stuck_detect_common.py::BOARDS`. That design failed: `alice-framework` was added to the topology in `t_593d077e` but not to the literal until the operator caught the gap. v0.1.1 removes the literal entirely; the literal is replaced by a function call. The drift detector (see below) is now an audit-only safety net rather than the primary enforcement.

The single-source-of-truth for "which boards are active" is the filesystem: `~/.hermes/kanban/boards/`. The human-readable topology doc (per-instance: `~/Documents/HermesVault/2-ATOMIC/concepts/kanban-board-topology-2026-07.md`) describes the discovery pattern + the per-board purpose; the doc has NO fixed count. The current count is a runtime property, not a document property.

### The paired-wiki integrity requirement (op-guard-5)

When a board is added to the topology (via `methodology/04b-decide-board-routing.md` + the per-instance topology concept note), the same change set MUST update:

1. **The topology doc** — add the board to the canonical board list (as a description, not a count), bump `updated:` frontmatter.
2. **The drift detector** — verify the literal-vs-discovered diff stays clean (the literal no longer exists, so the diff is always clean; the drift detector's role is now to confirm the discovery function returns the expected set).
3. **The chat-side preflight hook** — the hook reads from the shared scan helper, so this is implicit. Verify the hook uses the imported allow-list, not a literal copy.
4. **The cron-side wrapper** — same; verify the wrapper imports from the shared helper.

### Drift detector (audit-only safety net)

`wiki_lint.py --check board-drift` runs as part of the daily 04:00 cron and confirms the discovered board set matches the canonical `## All boards` enumeration in the topology doc. The check is audit-only; it emits a `## Board-drift` section in the daily report with one line per scanner (`L<line> — DRIFT (m/n) · missing=... · extra=...`). The drift detector is the safety net for cases where the discovery function itself drifted (e.g., env var override, glob pattern bug) — not for cases where the literal drifted, because the literal no longer exists.

## Part 3: The chat-side preflight hook

Every chat turn (before the LLM produces a reply) MUST run a read-only preflight scan across the active boards. The scan is cheap (<2s per turn on the current N-board topology, where N is auto-discovered) and surfaces stuck tickets to the chat-side agent before the operator has to ask.

### Scan shape

For each active board, the preflight queries `ready + running + blocked + triage` counts via the shared scan helper. The classifier applies the eligibility filter:

1. **Status counts** — single grouped query per board.
2. **Stuck-eligibility classifier** — `blocked(kind=dependency)` with no live parent, NOT matching a human-gate keyword, with the assignee-profile-existence guard (auto-unblock never routes to a nonexistent profile).
3. **Stale-claim-queue classifier** — `ready/todo` items with assignee != '' older than 24h (the canonical "boards look like a mess" surface).
4. **Blocked-needs-input inventory** — `blocked(kind=needs_input)` items, capped at 25 ids per board.

### Surface rules

| Condition | Surface |
|---|---|
| `ready + running == 0 AND blocked > N` (default N=5) for any board | Emit a warning to the chat turn (operator-visible) |
| Same ticket stuck > 3x in a row | File a `[HUMAN ACTION]` ticket with bucket = `REAL_ASK_BUCKETS` |
| Blocked ticket > N min (default N=15) and eligible for auto-unblock | Auto-act per Part 4 |
| Idle scan (no stuck, no stale) | Silent — no DM, no warning (operator chat is signal-only) |

The chat-side hook is read-only by default; the auto-act path is opt-in via the auto-act policy (Part 4).

## Part 4: The auto-act policy

The auto-act policy classifies a blocked ticket by `block_kind` and decides: auto-unblock, surface to operator, or skip. The policy is the canonical resolution rule; the per-instance implementation lives in the cron-side wrapper.

### Dependency-kind blocked tickets

**Eligibility criteria (all must hold):**

1. `status='blocked'` (NOT `triage` — triage is left alone)
2. `block_kind='dependency'`
3. No live parent (`parent_id IS NULL OR parent.status NOT IN ('done','archived')`)
4. Does NOT match a human-gate keyword in title/body/reason
5. Assignee-profile guard: if assignee != 'default', verify the profile dir exists

**Action: auto-unblock.** The dispatcher's parent-promotion edge (`kanban_db.py::promote_parents_after_child_done`) handles the inverse case (child-done promotes parent), but dependency-blocked tickets with dead parents need an explicit release call. The cron-side wrapper calls `kanban_block(kind=dependency, reason="parent dead; auto-unblocking")` to release the gate, OR `kanban_edit` to clear the block. Audit-line per op-guard-11 records the action.

### Operator-decision blocked tickets

**Eligibility criteria:** `block_kind='needs_input'` AND title contains a real operator-decision keyword (`LGTM`, `approve`, `pick`, `decide`, `bucket`, `path A/B/C`, etc.) AND assignee is jarvis or default.

**Action: surface to operator-DM, do NOT auto-unblock.** The DM carries the ticket id, the operator-decision prompt, and a 1-line summary of what auto-unblocked elsewhere. The recipient is the operator; the chat-side agent does not assume.

### Human-action blocked tickets

**Eligibility criteria:** title starts with literal `[HUMAN ACTION]` prefix (per op-guard-3 + `concepts/human-action-child-pattern.md`).

**Action: skip entirely.** The operator gates these; auto-act would subvert the structural surface. The preflight still reports the count so the operator sees the queue, but no auto-act fires.

### Audit-line shape (op-guard-11)

```
## [<isots>] jarvis-stuck-preflight — board=<X> blocked=<N> ready=<N> running=<N> action=<unblock_N|surface_to_operator|no_action> — source=<parent-ticket-id>
```

The audit-line is the durable record. The `kanban_comment` on the ticket is the local record; the audit-line is the operator-visible record.

## Part 5: The operator-action surface (op-guard-13)

The operator-DM surface is signal-only by contract. Every DM must contain something the operator can act on, OR a hard-failure signal that the operator must see. The preflight surface applies the same gate.

### When the preflight fires a DM

1. **Block-count threshold exceeded.** When `blocked > N` (default N=5) on any board, the chat-side preflight DM's the operator with the ticket IDs + their blocked reasons + a 1-line summary of what was auto-unblocked.
2. **Same-ticket-stuck escalation.** When the preflight detects the same ticket stuck > 3x in a row, file a `[HUMAN ACTION]` ticket to the operator-DM cron with bucket = `REAL_ASK_BUCKETS` (operator decision: archive, re-dispatch, or override).
3. **Dispatch liveness failure.** When the dispatcher is down or the scan fails entirely, the preflight surfaces a hard-failure signal (NOT actionable from chat; the operator must inspect the host).

### When the preflight does NOT fire a DM

1. **Idle scan.** No stuck, no stale, no liveness failure → silent. Operator chat is signal-only.
2. **Worker-profile tickets with reviewer-action vocabulary.** Coder / verifier / planner tickets whose body mentions "approve", "LGTM", "review" are NOT operator-action per op-guard-13. They are rescuable by other workers.
3. **Tickets with `block_kind=needs_input` but no operator-decision keyword.** These are stuck-tickets the rescue-watcher should handle, not the operator-DM.

## Part 6: Cadence and ownership

The chat-side agent owns proactive monitoring. The cadence is every chat turn (cheap, <2s scan per turn). The cron-side wrapper owns the 5-min cadence (`jarvis-stuck-board-monitor`) as a backstop for when the chat-side is idle. The doc-writer owns the canonical rule (this document). The operator owns disposition (auto-act vs surface-vs-skip is per-board, configurable via the wrapper).

| Surface | Cadence | Owner | What it does |
|---|---|---|---|
| Chat-side preflight hook | every chat turn | chat-side agent (jarvis) | Surface stuck tickets to the operator's chat |
| `jarvis-stuck-board-monitor` cron | every 5 min | cron-side wrapper | Bridge-file write; chat-side hook reads on next turn |
| `[HUMAN ACTION]` escalation | per ticket, > 3x stuck | chat-side agent | File `[HUMAN ACTION]` ticket to operator-DM cron |
| `kanban_rescue_watcher` cron | per the cron registry | cron-side wrapper | Re-dispatch rescuable stuck tickets |

## Part 7: Backwards compatibility

Existing crons + scanners from before the methodology shipped are grandfathered. The v0.1.0 → v0.1.1 amendment (auto-discovery scope, `t_de5e2168`) lands as a code change in the same change set: every scanner that had a `BOARDS = [...]` literal replaces it with `BOARDS = _discover_boards()` (or the per-instance equivalent). The drift detector's role is reduced from "primary enforcement" to "audit-only safety net" because the literal no longer exists. The audit-line shape from v0.1.0 (Part 4) is preserved verbatim.

## Part 8: Done-gate

A proactive-monitoring fix is **durable-shipped** when ALL of:

1. ✅ The auto-discovered board allow-list covers every active board in the topology. The discovery function lives at `_stuck_detect_common.py::_discover_boards()` (or the per-instance equivalent); every scanner (cron-side, chat-side, project-state-snapshot) imports from the shared helper so the discovery cannot drift between surfaces.
2. ✅ The chat-side preflight hook fires on every chat turn and surfaces stuck tickets without operator prompting.
3. ✅ The auto-act policy is documented + implemented (dependency-unblock, operator-decision surface, human-action skip).
4. ✅ The audit-line shape is canonical (`jarvis-stuck-preflight — board=<X> ... action=<...>`).
5. ✅ The compliance-verifier PASS: the next blocked ticket on a newly-added board (one that did not exist when the last compliance run was issued) is auto-unblocked (or surface-vs-skip per policy) within 15 min without operator prompting. The "newly-added" qualifier is the proof that auto-discovery works, not just a hardcoded set.

A fix that fails any check is **rework**, not shipped.

## Part 9: Operator gate

None at the methodology level. Verifier-gated for the per-instance implementation. The operator owns the per-board `block_kind` policy decisions (Part 4) and the threshold N (default 15 min) via the operator-action-dm cron.

## Part 10: How this maps to a future operational guard (if the pattern recurs)

If the chat-side agent runs Part 3's preflight scan 3 times and the same root cause produces a stuck ticket each time (e.g., "agent doesn't know which boards are active" hits 3 different sessions), this methodology MAY be promoted to a operational guard (one-paragraph constraint). The pattern would be: "When a new board is added to the topology, the chat-side agent MUST verify the canonical board-allow-list includes it." Until 3 observed failures of the same shape, this rule stays as a methodology.

## Part 11: Cross-references

- **Methodology 04b** (`methodology/04b-decide-board-routing.md`) — the work-type routing system; the board-allow-list is the substrate.
- **Methodology 06b** (`methodology/06b-decide-blocked-ticket-recovery.md`) — the post-failure recovery procedure (this methodology is the proactive surface; 06b is the reactive surface).
- **Methodology 06** (`methodology/06-iteration-loop.md`) — the iteration loop primitive (detect → surface → act); this methodology is the multi-board specialization.
- **Methodology 05** (`methodology/05-op-guards.md`) — operational guards; this methodology is NOT a operational guard (see Part 10).
- **Methodology M-instance-vs-framework** (`methodology/M-decide-instance-vs-framework.md`) — the framework-vs-instance distinction rule; this methodology is friend-portable (Alice framework).
- **Per-instance implementation:** `~/.hermes/scripts/_stuck_detect_common.py` (canonical board-allow-list + scan helper), `~/.hermes/tools/jarvis_stuck_board_monitor.py` (cron-side wrapper), `~/.hermes/scripts/kanban_stuck_detect.py` (chat-side hook).
- **Per-instance concept note:** `~/Documents/HermesVault/2-ATOMIC/concepts/kanban-board-topology-2026-07.md` (the topology doc).
- **Canonical op-guard rules:**
  - `op-guard-5-wiki-update-2026-07-21.md` (paired-wiki integrity; the foundation of Part 2).
  - `op-guard-9-cron-loop-completeness-2026-07-21.md` (cron-loop completeness; the chain that the surface must honor).
  - `op-guard-11-log-md-append-only-2026-07-22.md` (audit-line append-only; Part 4's audit-line contract).
  - `op-guard-13-verify-operator-action-2026-07-27.md` (operator-action bucket classifier; Part 5's surface gate).
  - `op-guard-15-must-call-terminal-2026-07-29.md` (must-call-terminal; the cron-side wrapper MUST call the terminal API after auto-act).
  - `op-guard-16-spec-first-flow-2026-08-05.md` (spec-first flow; doc-writer ships the methodology first, then files the instance child tickets).
  - `op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md` (alice-first, instance-second, compliance-gate; the per-instance implementation is the coder child ticket).
  - `op-guard-19-pre-verify-artifact-state-2026-08-08.md` (pre-verify before filing commit/tag/release/push tickets; the verifier PASS is op-guard-19-grounded).
- **Source ticket:** `t_593d077e` (the verified case: 4 alice-framework tickets blocked 30+ min; cron never flagged; the v0.1.0 fix added `alice-framework` to the literal `BOARDS` allow-list).
- **Amendment ticket:** `t_de5e2168` (the v0.1.1 fix: replaces the literal `BOARDS` allow-list with auto-discovery from `~/.hermes/kanban/boards/` so the silent-gap class of bug cannot recur when the next board is added). Operator quote 2026-08-11: *"why are we scoping to 9 boards instead of just saying all boards?"*
- **Sibling ticket:** `t_7be253bd` (worker-stall-detection; this methodology is the blocked-ticket surface; t_7be253bd is the running-ticket surface).
- **Related skill:** `~/.hermes/skills/hermes-host-ops/hermes-dispatch-liveness` (dispatch liveness is about the dispatcher's own health; this methodology is about ticket health).

## Audit-line

```
## [2026-08-12T00:30Z] doc-writer-ship-v011-amendment — methodology/M-decide-proactive-board-monitoring.md v0.1.0 → v0.1.1 (auto-discovery scope: BOARDS list iterates ~/.hermes/kanban/boards/ at runtime; the fixed 9-board count is gone from the canonical rule). Source: t_de5e2168 + operator quote 2026-08-11 "why are we scoping to 9 boards instead of just saying all boards?" Companion files: paired-wiki on kanban-board-topology-2026-07.md (drops "9 active boards" subtitle + "The nine boards" heading; describes auto-discovery pattern) + CHANGELOG.md [Unreleased] entry. Instance-conformance children filed for coder (the _stuck_detect_common.py + _kanban_boards.py patches) and verifier (op-guard-17 done-gate).
```
