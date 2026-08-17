---
id: alice-methodology-M-decide-wiki-lint
created: 2026-08-17T13:58:00Z
updated: 2026-08-17T13:58:00Z
title: "Methodology M — Decide wiki-lint (the audit-only detection the loop preserves)"
type: methodology
status: draft
source: alice-framework
version: 0.1.0
tags: [kind:methodology, kind:loop-methodology, kind:wiki-drift, kind:board-drift, kind:daily-cron, kind:audit-line-only, project:alice]
confidence: 0.9
alice-ticket: t_7419d73e
teaching-example: true
companion: ["[[methodology/02-decide-skills.md]]", "[[methodology/M-decide-graph-readiness.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[methodology/M-decide-spec-first-flow.md]]", "[[methodology/M-decide-skill-curator.md]]"]
links: ["[[methodology/M-decide-graph-readiness.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[methodology/M-decide-skill-curator.md]]", "[[methodology/M-decide-spec-first-flow.md]]", "[[methodology/05-op-guards.md]]", "[[2-ATOMIC/rules/op-guard-11-log-md-append-only-2026-07-22.md]]", "[[2-ATOMIC/rules/op-guard-9-cron-loop-completeness-2026-07-21.md]]", "[[2-ATOMIC/rules/op-guard-15-must-call-terminal-2026-07-29.md]]", "[[2-ATOMIC/rules/op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md]]", "[[2-ATOMIC/concepts/kanban-board-topology-2026-07.md]]"]
source-ticket: t_7419d73e
parent-meta: t_ebc60e10
amended_by: []
---

# Methodology M — Decide wiki-lint (v0.1.0)

> **A daily 05:00 CDT cron MUST audit `~/Documents/HermesVault/2-ATOMIC/concepts/` for frontmatter-stale concept notes (superseded by a newer decision or rule) and detect `BOARDS = [...]` literals in `~/.hermes/scripts/*.py` that drift from the canonical `kanban-board-topology-2026-07.md` inventory; the loop MUST NOT edit notes, MUST NOT create boards, and MUST NOT send Discord DMs.** This methodology is the friend-portable contract for the `wiki-lint` loop. Without it, knowledge drift accumulates silently — a "canonical" concept note becomes a fossil of older decisions — and the operator catches the drift by accident. With it, the drift is detected on cadence, surfaced through the audit trail (vault log + `0-INBOX/` markdown + script board-drift report), and corrected under explicit constraints.

## Part 1: Why this methodology exists

A concept note (in `2-ATOMIC/concepts/`), a decision note (`2-ATOMIC/decisions/`), and a rule (`2-ATOMIC/rules/`) are three artifacts of the same domain. They are not parallel; they are layered: a concept notes the pattern, a decision records the verdict, and a rule codifies the durable directive. The wiki is supposed to be **self-healing**: when a decision supersedes the concept, the concept's `updated:` field is bumped; when a rule supersedes a decision, the decision's `updated:` field is bumped. Without periodic linting, three failure modes recur:

1. **Concept-stale drift.** A concept note carries an `updated:` field older than a related decision's `updated:` field. Agents that load the concept for context see an older verdict than the operator's actual current view. The drift is invisible to routine scans; it shows up only when an agent confidently asserts an obsolete answer.
2. **Board-topology drift.** A new board is added to `~/.hermes/kanban/boards/<slug>/board.json` (and to `BOARDS = [...]` literals in production scanners) without updating `kanban-board-topology-2026-07.md`. Operators reading the topology get one answer; production scanners get another. The drift accumulates every time a board is added or removed without paired-wiki.
3. **Broken frontmatter drift.** A concept note's `id`, `updated`, `type`, `status`, or `tags` field is empty or malformed. Downstream readers (wiki_lint, vault_lint, OBR indexing) either silently skip the note or error out; the operator sees the symptom but not the cause.

The `wiki-lint` loop closes the failure-mode triangle. It runs daily at 05:00 (one hour after `skill-curator` at 04:00, so curator-driven skill reorganizations land before wiki lint reads them), scans every concept note + every production scanner, and produces a durable audit trail. The contract — *what the loop must and must not do* — is what this methodology pins down so a friend reading Alice can rebuild the loop from the spec.

**Why this is a methodology, not just a yaml entry.** The `wiki-lint` loop is the canonical example of an *audit-only, deterministic, daily cron with operator-policy gates*. The same shape recurs across the operator's environment (skill-curator, wiki-lint, multi-day-status-cron, project-state-snapshot, kanban-rescue-watcher). The methodology captures the pattern so each instance does not reinvent the gates. The companion `M-decide-skill-curator.md` is the v0.1.0 precedent for this same shape.

## Part 2: The contract — what the loop must preserve

The `wiki-lint` loop preserves one invariant: **every concept note in `~/Documents/HermesVault/2-ATOMIC/concepts/` is current relative to its newer decisions and rules, every scanner literal in `~/.hermes/scripts/*.py` matches the canonical board inventory, and every concept note's frontmatter is parseable**. The invariant is broken into three sub-invariants the loop enforces each tick:

### Invariant A — concept-staleness detection

For every concept note in `2-ATOMIC/concepts/*.md`, the loop MUST compare the note's `updated:` field against the latest `updated:` field of any decision or rule on the same topic (substring + greedy topic heuristic). A note is **stale** when at least one related decision/rule has a newer parseable `updated:` date. The loop MUST emit one row per stale note in the `## Stale-by-decision` table with: concept note path, note's `updated:`, the superseding decision/rule path, the superseder's `updated:`, and the days-stale delta.

### Invariant B — board-topology drift detection

For every `BOARDS = [...]` literal in `~/.hermes/scripts/*.py`, the loop MUST cross-check against the canonical inventory in `~/Documents/HermesVault/2-ATOMIC/concepts/kanban-board-topology-2026-07.md` (the `## The seven boards` bullet list + `default`). A scanner literal is **drifted** when the literal's list differs from the canonical inventory: an extra board, a missing board, or a typo. The loop MUST emit one row per drifted scanner with: script path, scanner literal variable, drift description (missing/extra boards). The check supports both legacy `BOARDS = [...]` literals and migrated `BOARDS = CANONICAL_BOARDS` assignments; shared assignments are resolved from `_kanban_boards.py` so a visible clean/drift entry survives consolidation.

### Invariant C — broken-frontmatter detection

For every note in `2-ATOMIC/concepts/*.md`, the loop MUST parse the frontmatter and confirm `id`, `updated`, `type`, `status`, and `tags` are present and non-empty. A note is **broken** when one of those five fields is missing, empty, or unparseable. The loop MUST emit one row per broken note in `## Broken-frontmatter` with: note path, the missing/malformed field, the byte-offset where the frontmatter error was detected.

## Part 3: Inputs (what the linter reads)

| Surface | What the linter reads |
|---|---|
| **Concept notes** | Every `~/Documents/HermesVault/2-ATOMIC/concepts/*.md`. Parses YAML frontmatter; treats the rest of the note as opaque text (the topic heuristic uses title + `tags:` substring matching only). |
| **Decision + rule notes** | Every `~/Documents/HermesVault/2-ATOMIC/decisions/*.md` and `2-ATOMIC/rules/*.md` for the staleness cross-reference. Decision/rule parseable `updated:` is the comparator; the older concept is the loser. |
| **Production scanner literals** | Every `BOARDS = [...]` literal in `~/.hermes/scripts/*.py` that resolves to a concrete list (i.e. not migrated to `_kanban_boards.py::CANONICAL_BOARDS`). The loop cross-checks each list against the canonical inventory. |
| **Canonical inventory** | `~/Documents/HermesVault/2-ATOMIC/concepts/kanban-board-topology-2026-07.md` — the `## The seven boards` bullet list + the `default` board (8 boards total). Parsed live; no caching across ticks. |
| **Canonical boards module** | `~/.hermes/scripts/_kanban_boards.py` — the resolved canonical list when a scanner migrated to `BOARDS = CANONICAL_BOARDS`. The linter resolves the assignment symbolically, not by literal substitution. |
| **Yesterday's audit summary** | `~/Documents/HermesVault/0-INBOX/wiki-lint-<date-1>.md` for idempotency. The linter reuses yesterday's per-staleness drift record and only re-scans notes that changed (frontmatter `updated:` bumped). |
| **Vault log state** | `~/Documents/HermesVault/log.md` for the audit-line append target (per op-guard-11 — append-only via `vault_log.append_audit_line`). |

## Part 4: Outputs (what the linter writes)

The linter writes exactly two artifacts per tick, in this order:

### Output 1 — `0-INBOX/wiki-lint-<ISO-date>.md`

The audit markdown for human reading. The file MUST include:

- **Counts line.** `checked: N; stale: N; new-boards-without-doc: N; broken-frontmatter: N; needs-manual: N; scripts-drifted: N/N` where each count is the per-tick totals.
- **`## Stale-by-decision` table.** Concept note path, concept `updated:`, superseding decision/rule path, superseder `updated:`, days-stale delta. One row per stale note.
- **`## New-boards-without-topology-update`.** One row per `board.json` discovery that is not yet documented in `kanban-board-topology-2026-07.md`.
- **`## Broken-frontmatter`.** One row per note whose frontmatter is missing/empty/malformed on `id`, `updated`, `type`, `status`, or `tags`.
- **`## Needs-manual`.** Date/topic matches that cannot be determined automatically — agent's heuristic asked, can't answer; operator resolves.
- **`## Board-drift`.** Per-scanner table: scanner script path, literal variable, drift description (missing/extra boards vs canonical).
- **NO fabricated note names.** The linter MUST NOT invent concept notes that did not appear in the scan; any per-row reference is a real, observed file.

The file MUST be ≥ 1 KB of markdown (a tick that scans zero concepts is a tick that didn't fire; the file size gate catches that). File path template: `0-INBOX/wiki-lint-<ISO-date>.md` (per the loop yaml entry).

### Output 2 — vault log audit-line (per op-guard-11)

Every tick appends exactly one line to `~/Documents/HermesVault/log.md` via `vault_log.append_audit_line` (the canonical append-only helper; **never** `write_text` or `open("a")` directly — the 2026-07-22 truncation incident (`t_682219d8`) was a curator's read-modify-write on this file). The schema, per the loop yaml:

```
## [ISO8601-UTC] wiki-lint — checked=N stale=N new-boards-without-doc=N broken-frontmatter=N scripts-drifted=N/N
```

All five counts MUST be present in the audit-line; a missing field is an audit-line schema violation and the linter transitions to `result=failure` per op-guard-9 + op-guard-15.

### Output 3 — NO Discord DM

The loop MUST NOT call `send_dm` / `hermes-send` / any Discord delivery path. This is operator policy, captured in the yaml entry's `worker_prompt` block, and the linter's stop_when condition fails-closed if a Discord delivery is attempted. The operator reads the vault log + the `0-INBOX/` file when they want a picture; they do not want a daily 05:00 DM. (The output the linter produces is `DM: suppressed (operator policy — audit-line only)` — printed to stdout, not delivered.)

## Part 5: Cadence + ownership

| Surface | Cadence | Owner | What it does |
|---|---|---|---|
| `wiki-lint` cron | daily 05:00 CDT (`0 5 * * *`) | Scripts/Code (`~/.hermes/tools/wiki_lint.py`) | Scan + drift report + audit file + audit-line. |
| Loop yaml entry | registered at `~/.hermes/loops/hermes.yaml` `id: wiki-lint` | Scripts/Code (yaml) | Names the cron schedule, the worker prompt, the audit-line schema, the file path template, the script-invocation contract. |
| Loop intent doc | registered at `~/.hermes/loops/intents/wiki-lint-intent.md` | Scripts/Code (markdown) | Worker-prompt version pin, dispatcher registration contract, node-type discipline phrase (per Part 6). |
| Compliance-verifier child | per `M-decide-spec-first-flow.md` Part 2 — a verifier ticket that runs the 8-check discipline from `M-decide-graph-readiness.md` Part 5 on the loop's current state. | Agents (verifier profile) | Confirms the loop's contract still holds after every env change. |
| Operator review | ad-hoc | Human (operator) | Reads the daily `0-INBOX/wiki-lint-<date>.md` + vault log; decides what to fix (a stale concept note, a new board without topology update, a broken frontmatter field). |

**Cadence rationale.** Daily 05:00 catches knowledge drift within one sleep cycle, and runs ONE HOUR AFTER `skill-curator` at 04:00 so any skill-driven concept reorganizations (e.g. a new concept note filed by curator) are visible to linter before linter's read. The cron is the canonical detect leg (per op-guard-9); the audit-line + `0-INBOX/` markdown are the canonical surface (the loop is auditable-but-silent on Discord per operator policy); the operator review is the canonical act leg (the linter cannot decide which stale notes to fix without operator judgment on topic matches). The 04:00 → 05:00 ordering also avoids a class of frontmatter race where the curator writes a new concept note whose `updated:` is `now` but the linter's read happens before the write completes.

## Part 6: Node-type discipline (item 9 of the 9-item promotion rubric)

Per `methodology/M-decide-graph-readiness.md` v0.1.1 Part 3 item 9 + `methodology/04a-decide-work-graph.md` Part 4, every node in the work graph MUST use its proper type. The `wiki-lint` loop has four nodes, one of each canonical type plus the canonical verifier:

- **Scripts/Code node — `~/.hermes/tools/wiki_lint.py`.** The linter's scan + staleness comparator + board-drift comparator + frontmatter-validity check is deterministic and idempotent. It walks the vault + scanner files, parses frontmatter, runs bounded regex matches against `BOARDS = [...]` literals, and writes the audit file + audit-line. The Scripts/Code node is the canonical place for the work because the loop's contract is *deterministic checks against a known state* — exactly what scripts are for. An Agent (LLM-driven) interpretation would risk non-determinism (different stale-detection verdicts on different runs), which violates the staleness-drift + idempotency invariants.
- **Agents node — `doc-writer` profile (the loop's `run_with_profile`).** The dispatcher registers the linter's tick as a job assigned to the `doc-writer` profile. The Agent node is responsible for the loop-level orchestration: claiming the cron-triggered job, running the worker prompt, and routing the result through `kanban_complete` (per op-guard-15 must-call-terminal). The Agent node does NOT do the scan itself; the scan is delegated to the Scripts/Code node. The Agent's job is the *interpretation of the cron schedule + the dispatcher contract*, which is exactly what Agents are for (judgment + bounded output, deterministic validation in the next node).
- **Human node — operator review.** The operator reviews the daily `0-INBOX/` file + the vault log. The Human node owns `## Needs-manual` topic-match decisions (the heuristic surfaced a match the agent can't decide; the operator picks whether to update the concept note or amend the heuristic), frontmatter-fix authority (the linter reports broken frontmatter; the operator decides whether the fix is in-place or archived-and-replaced), and the cadence policy itself (daily vs weekly, 05:00 vs other hour). The Human gate is also the failure-recovery surface: when the linter fails a tick, the operator's eyes on the vault log are what decide whether the loop needs a manual recovery or the next scheduled tick will retry naturally.
- **(Implied) Verifier node — `M-decide-graph-readiness.md` Part 5 check 8 (item 9 compliance-verifier child).** When the loop is being promoted from `live-pending-test` to `live`, a verifier profile (assigned via the per-loop META's compliance-verifier child, per op-guard-17) walks the loop's design doc + intent + yaml + script and confirms the node-type mapping here is honest. The verifier is itself an Agents node per Part 4 (`04a-decide-work-graph.md`) — an independent reviewer that does NOT re-run the scan.

The canonical phrase per `04a-decide-work-graph.md` Part 4 (Scripts/Code / Agents / Human) is satisfied: each step in the linter's contract maps to exactly one of the three node types, with no Agent node doing deterministic work and no Scripts/Code node doing interpretation.

## Part 7: Failure modes + recovery

| Failure mode | Detect | Act | Verify |
|---|---|---|---|
| **Concept directory missing** (`2-ATOMIC/concepts/*.md` returns empty) | The scan raises `FileNotFoundError` at startup. | Linter transitions to `result=failure` per op-guard-15 + op-guard-9; the next cron tick retries. Operator sees the failure via the audit-line. | Next tick succeeds, OR operator manually restores the directory. |
| **Audit file < 1 KB (zero-concept scan)** | File size gate catches the missing counts. | Linter rewrites the file with `checked: 0; stale: 0; new-boards-without-doc: 0; broken-frontmatter: 0; needs-manual: 0; scripts-drifted: 0/0` + a `## Zero-concept scan warning` section. Audit-line records `result=success` (the gate is "file exists + ≥ 1 KB"; the warning is in the body). | Operator reads the file + decides whether the missing `2-ATOMIC/concepts/` is a host-level issue. |
| **Audit-line append fails** (e.g., disk full, perms) | `vault_log.append_audit_line` raises. | Linter transitions to `result=failure` per op-guard-11; the loop does not retry the audit-line write until the next tick (audit-line is idempotent on retry — a second `## [...] wiki-lint` line for the same ISO date is harmless). | Next tick's audit-line includes the prior failure summary. |
| **`kanban-board-topology-2026-07.md` missing or unparseable** | YAML parser raises on the topology file. | Linter fails the `Board-drift` check explicitly (the section reports `Topology: <error>`); audit-line records `scripts-drifted=0/0` (the literal comparison is gated on the topology being parseable). | Operator edits or restores the topology file; next tick's Board-drift section is restored. |
| **`_kanban_boards.py` missing** | `import` resolution fails at the canonical-board fallback. | Linter falls back to literal-list comparison only; the migrated scanners are reported as `drifted: cannot-evaluate` (a non-failure state); audit-line records the import error inline. | Operator edits or restores `_kanban_boards.py`; next tick's migrated scanners resolve cleanly. |
| **Cross-cutting rule edits to concept notes** (Invariant violation — loop is audit-only) | The verifier profile (item-5 compliance-verifier child) catches the violation on the next `M-decide-graph-readiness.md` Part 5 check 8 walk. | Linter rollback (the verifier's evidence proves the loop touched a note; the rollback re-reads the note from git history or the operator's last-approved snapshot). Audit-line records the rollback. Verifier fails the compliance-verifier child. | Compliance-verifier re-runs; the loop is back at `live-pending-test` after the rollback. |
| **Cron never fires** | `jobs.json` shows `last_run=NULL` for `wiki-lint` past 25h. | `kanban_rescue_watcher` + `cron-loop-completeness` chain (op-guard-9) detects the missed tick + files a `[HUMAN ACTION]` ticket to operator-DM. | Operator inspects cron registration; next tick fires. |

The failure-mode table is the canonical surface for retro-action. A new failure mode that recurs 3 times across 3 ticks becomes a new row in the table + a paired-wiki update to the methodology (per op-guard-5).

## Part 8: How to apply this methodology

When onboarding the `wiki-lint` loop in a new operator environment:

1. **Copy the script.** Place `~/.hermes/tools/wiki_lint.py` from the canonical source (this methodology + the alice-framework repo's `examples/` if available, else hand-port the spec from Part 3 + Part 4).
2. **Register the cron.** Add `0 5 * * *` to the user's crontab (or the equivalent on non-cron platforms) — ONE HOUR AFTER `skill-curator` at 04:00 so curator changes are visible. Point the cron at the script's venv python.
3. **Register the loop entry.** Add `wiki-lint` to `~/.hermes/loops/hermes.yaml` with the canonical schema (worker_prompt + audit_line_schema + file_path_template + crontab_entry). Initial status: `live-pending-test`.
4. **Register the intent doc.** Add `~/.hermes/loops/intents/wiki-lint-intent.md` (the version-pinned dispatcher contract) per the loop-updater loop's `worker_prompt`. The intent doc MUST carry the node-type discipline phrase (Scripts/Code / Agents / Human) per Part 6 + op-guard-29.
5. **Copy the topology reference.** Place `~/Documents/HermesVault/2-ATOMIC/concepts/kanban-board-topology-2026-07.md` from the canonical source. Without it, Part 2 Invariant B fails-closed (Board-drift reports `Topology: missing`).
6. **Run the first tick manually.** `python3 ~/.hermes/tools/wiki_lint.py` from the operator shell; verify the audit file + audit-line both land; verify no Discord delivery fires; verify `--dry-run` renders the full report to stdout without writing.
7. **Verify the cron.** Wait one day, or trigger the cron manually (`cron <crontab>` after editing the crontab). Verify the 05:00 tick fires AFTER the 04:00 skill-curator tick.
8. **File the compliance-verifier child.** Per `M-decide-spec-first-flow.md` Part 2 + `M-decide-graph-readiness.md` Part 5, the META ticket that registers the loop has a verifier child that runs the 8-check discipline. The verifier child confirms: scripts/agents/human node types map correctly (Part 6); staleness comparator is conservative (`## Needs-manual` not auto-resolved); board-drift check supports both legacy literals and migrated modules; frontmatter validity checks all five required fields; audit-line schema matches; file size gate catches zero-concept scans; no Discord delivery path is invoked.
9. **Promote to `live`.** Once all 9 items of the rubric pass (per `M-decide-graph-readiness.md` Part 3), flip the yaml `status:` field + bump the intent doc `version:` field + append an audit-line per op-guard-11.

A loop that fails any check is `rework`, not shipped (per `M-decide-graph-readiness.md` Part 5 check 8 — node-type discipline is a release-blocker that supersedes items 1–8).

## Part 9: Backwards compatibility

This methodology applies forward from 2026-08-17 (initial ship). Pre-existing wiki-lint instances in operator environments are grandfathered: their existing cron schedule + audit file schema + topology reference continue to apply. The methodology is the *canonical reference* for what the loop must look like in any Alice-compliant environment; existing instances that already satisfy Part 2 + Part 4 + Part 6 require no changes.

The 2026-07-22 `t_682219d8` incident (the vault log truncation caused by a read-modify-write) is the canonical evidence for Output 2's `vault_log.append_audit_line` requirement. Pre-2026-07-22 linters that wrote directly via `open("a")` are grandfathered for forensic traceability, but new code MUST use the helper (per op-guard-11).

The 04:00 → 05:00 scheduling ordering (skill-curator → wiki-lint) is operator-policy, not a methodology invariant. Different operator environments may order cron runs differently; the canonical reference for the operator's environment is the yaml entry's `worker_prompt` block (Part 5 surface) + the `crontab_entry` field. The methodology names the *shape* of the ordering (linter runs after curator so curator changes land in time); the operator names the *exact offsets*.

The board-drift check's tolerance for `_kanban_boards.py` migrations is also operator-policy. The canonical reference is `~/.hermes/scripts/_kanban_boards.py::CANONICAL_BOARDS`; the methodology names the *shape* of the support (migrated scanners resolve symbolically, legacy scanners resolve literally); the operator names which migration waves landed.

## Part 10: Cross-references

- **`methodology/02-decide-skills.md`** — the canonical skills methodology. The wiki-lint loop audits concept notes; skills discipline is a sister concern.
- **`methodology/04a-decide-work-graph.md`** — Part 4 (the 3-node-type taxonomy: Human / Scripts/Code / Agents) is the design-time discipline that Part 6 codifies at promotion-time per `M-decide-graph-readiness.md` Part 3 item 9.
- **`methodology/M-decide-graph-readiness.md`** v0.1.1 — the 9-item promotion rubric that includes the loop's `live`-readiness bar. Part 3 item 1 (methodology doc) + item 9 (node-type discipline) are the two items this doc flips; items 2, 4, 5, 6 are the per-loop promotion path; items 3, 7, 8 are the runtime + operator review gates.
- **`methodology/M-decide-skill-curator.md`** v0.1.0 — the sister methodology for `skill-curator`. Both loops share the audit-only + daily-cron + operator-policy-gates shape; this doc mirrors Part 2 (invariant), Part 3 (inputs), Part 4 (outputs), Part 5 (cadence), Part 6 (node-type), and Part 7 (failure modes) by design.
- **`methodology/M-decide-spec-first-flow.md`** — the 4-phase flow + V-1/V-2/V-3 verifier steps. The compliance-verifier child (Part 5 of `M-decide-graph-readiness.md`) is the per-loop application of the V-2 step.
- **`methodology/05-op-guards.md`** — the operational-guard umbrella. Op-guards 9, 11, 15, 17 are the load-bearing rules for this loop; op-guard-5 is the paired-wiki rule that makes the methodology + intent + yaml + tests atomic.
- **`~/.hermes/tools/wiki_lint.py`** — the canonical Scripts/Code node. The script's behavior is the spec in code form; the methodology is the spec in prose form.
- **`~/.hermes/loops/hermes.yaml` `id: wiki-lint`** — the canonical yaml binding. The yaml's `worker_prompt` block + `audit_line_schema` are the runtime contract; the methodology is the friend-portable contract.
- **`~/.hermes/loops/intents/wiki-lint-intent.md`** — the canonical intent doc (per the loop-updater loop's discipline). The intent doc's `version:` field is bumped per `loop-updater` invocation; the methodology's `version:` field is bumped per amendment.
- **`~/Documents/HermesVault/2-ATOMIC/concepts/kanban-board-topology-2026-07.md`** — the canonical board inventory. Part 2 Invariant B parses this file live; without it, the Board-drift check reports `Topology: missing`.
- **`~/Documents/HermesVault/log.md`** — the canonical vault log. Every linter tick appends exactly one line via `vault_log.append_audit_line` per op-guard-11.
- **`2-ATOMIC/rules/op-guard-9-cron-loop-completeness-2026-07-21.md`** — the detect → surface → act → verify chain that the linter's cron + audit-line + audit-file + operator-review chain honors.
- **`2-ATOMIC/rules/op-guard-11-log-md-append-only-2026-07-22.md`** — the audit-line append-only discipline. The 2026-07-22 truncation incident is the canonical evidence for the helper's existence.
- **`2-ATOMIC/rules/op-guard-15-must-call-terminal-2026-07-29.md`** — the must-call-terminal rule. The linter's tick MUST call `kanban_complete` (or transition to `failure` via `kanban_block`) before the cron returns; exit-without-terminal-call is a protocol violation per the rule.
- **`2-ATOMIC/rules/op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md`** — the alice-first / instance-second / compliance-gate discipline. The compliance-verifier child that Part 5 names IS the op-guard-17 done-gate child for the loop's META.
- **Source tickets:** `t_7419d73e` (this methodology doc's ship ticket; doc-writer), `t_ebc60e10` (the per-loop audit + DEFER disposition + 5-children filing META), `t_981e0950` (the board-drift detector that shipped v0.2.0 of the intent doc), `t_aa4fca2d` (the original wiki-lint loop build ticket).
- **Companion children (per `t_ebc60e10`):** `t_6c3acd3a` (coder, item 4 regression suite), `t_dff45f02` (verifier, item 5 compliance-verifier child), `t_2ad14235` (jarvis, op-guard-29 HITL gate for operator disposition), `t_e1b60776` (verifier META done-gate per op-guard-17).

## Audit-line

```
## [2026-08-17T13:58Z] doc-writer-shipped — methodology/M-decide-wiki-lint.md v0.1.0 written. Codifies: the wiki-lint loop's contract (Part 2 — concept-staleness detection + board-topology drift + broken-frontmatter detection), inputs (Part 3 — concept notes + decisions/rules + scanner literals + canonical inventory + topology + yesterday's summary + vault log state), outputs (Part 4 — 0-INBOX audit markdown + vault log audit-line + NO Discord DM), cadence (Part 5 — daily 05:00 CDT cron AFTER skill-curator 04:00 + compliance-verifier child + operator review), node-type discipline (Part 6 — Scripts/Code for the scan/comparator/validity, Agents for the doc-writer dispatcher, Human for the operator review, Verifier for the item-5 compliance-verifier child), failure modes (Part 7 — 7-row recovery table), onboarding (Part 8 — 9-step apply procedure), backwards compat (Part 9 — pre-existing instances grandfathered; cron ordering is operator-policy not methodology invariant), cross-references (Part 10). Flips items 1 + 9 of the 9-item promotion rubric (M-decide-graph-readiness.md v0.1.1 Part 3); the post-write audit-score is ≥ 4/9 (items 1+2+9 PASS, items 4+5+6+8 still open as sibling children). Companion: paired-wiki on 04a-decide-work-graph.md (one-line cross-reference to M-decide-wiki-lint.md) + paired-wiki intent doc update (version 0.2.0 → 0.3.0 + node-type discipline phrase per Part 6). Source: t_7419d73e + parent t_ebc60e10 (DEFER disposition recommended; the loop is `live-pending-test` v0.1.0 and remains so until items 3, 4, 5 + 6-8 also pass).
```
