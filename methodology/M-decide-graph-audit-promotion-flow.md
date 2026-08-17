---
id: alice-methodology-M-decide-graph-audit-promotion-flow
created: 2026-08-17T13:25:00Z
updated: 2026-08-17T13:25:00Z
title: "Methodology M — Decide the graph-audit-promotion flow (the canonical 4-stage HITL audit + promotion cycle for the operator's loop registry)"
type: methodology
status: draft
source: alice-framework
version: 0.1.0
tags: [kind:methodology, kind:flow-spec, kind:loop-promotion, kind:hitl, kind:audit, project:alice]
confidence: 0.0
alice-ticket: "[[ticket:t_3b49e1bc]]"
companion: ["[[methodology/04d-decide-flow-spec.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[methodology/04c-decide-master-ticket.md]]", "[[methodology/M-decide-graph-readiness.md]]", "[[methodology/M-decide-spec-first-flow.md]]", "[[methodology/M-decide-instance-vs-framework.md]]"]
links: ["[[methodology/04d-decide-flow-spec.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[methodology/04c-decide-master-ticket.md]]", "[[methodology/M-decide-graph-readiness.md]]", "[[methodology/M-decide-spec-first-flow.md]]", "[[methodology/M-decide-instance-vs-framework.md]]", "[[2-ATOMIC/rules/op-guard-29-per-loop-hitl-gate-2026-08-15.md]]", "[[2-ATOMIC/rules/op-guard-30-promotion-requires-evidence-or-exemptions-2026-08-17.md]]", "[[2-ATOMIC/rules/op-guard-31-three-stage-graph-hitl-2026-08-17.md]]", "[[2-ATOMIC/rules/op-guard-32-four-stage-graph-hitl-2026-08-17.md]]", "[[2-ATOMIC/rules/op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md]]", "[[2-ATOMIC/rules/op-guard-11-log-md-append-only-2026-07-22.md]]"]
teaching-example: true
---

# Methodology M — Decide the graph-audit-promotion flow

> The `graph-audit-promotion` flow runs the per-loop audit (per `M-decide-graph-readiness.md` v0.1.1 9-item rubric) on every registered loop in `~/.hermes/loops/hermes.yaml` and fires the canonical 4-stage operator HITL gates (per op-guard-32) — scope/intent → intent-achievement → compliance → disposition — taking per-loop action (PROMOTE / DEFER / ARCHIVE) per the operator's disposition. The flow is the canonical surface for the operator's loop-promotion cycle. Without this flow, the audit is ad-hoc (the proactive board unblocker cron surfaces scores but no operator-decision path handles the per-loop action), and the `live` vs `live-pending-test` distinction is a heuristic rather than a tracked promotion cycle.

## Source

Operator 2026-08-17 (chat, verbatim): *"we have a loop builder board. Let's follow our own spec and create a flow on the board which will do the audits and promotion"*

The operator's environment has a `loop-builder` board (canonical for loop registration per `M-decide-loop-catalog.md`) but no flow that does the per-loop audit + promotion work on it. The proactive board unblocker cron (`t_7f1cf929`) surfaces per-loop scores but the operator disposition (HITL #1/#2/#3/#4) and per-loop action (filing the disposition ticket, flipping yaml, bumping intent) is not wired. The flow codifies that wiring.

## Part 1: Why this flow

The 9-item rubric in `M-decide-graph-readiness.md` v0.1.1 Part 3 is the substance; the per-loop disposition is the operator's decision; the per-loop action is the environment mutation. The flow wires the three together:

1. **Substance** — the rubric (machine-verifiable items 1-6 + 9 + operator-verified items 7-8) is captured by `python3 ~/.hermes/tools/loop_live_audit.py`.
2. **Decision** — the operator fires the 4-stage HITL gates (per op-guard-32) and disposes each loop.
3. **Action** — the per-loop disposition ticket fires (PROMOTE → flip yaml + bump intent + audit-line; DEFER → deferral ticket; ARCHIVE → archive ticket).

Without this flow, the loop registry is a static file: every loop ships at `live-pending-test`, the audit is run manually, the operator dispositions ad-hoc, and the audit-line is missed. The flow makes the promotion cycle a first-class, auditable work graph on the `loop-builder` board.

### What this flow is not

- **Not a methodology.** The 9-item rubric lives in `M-decide-graph-readiness.md` v0.1.1 Part 3. This doc is the flow spec (the 6-field template + topology + process), not the rubric itself.
- **Not a single-ticket audit.** The flow processes ALL registered loops in one cycle; per-loop tickets are the children of the master ticket.
- **Not autonomous.** The 4-stage HITL gates are operator decisions; the flow does not auto-PROMOTE / auto-ARCHIVE without operator disposition.

## Part 2: Flow spec (per `04d-decide-flow-spec.md` — the 6-field template)

### Field 1 — Goal

Audit every loop in `~/.hermes/loops/hermes.yaml` against the 9-item rubric (`M-decide-graph-readiness.md` v0.1.1 Part 3), fire the 4-stage operator HITL gates (per op-guard-32) for each loop, and take per-loop action per the operator's disposition. One canonical flow per audit cycle; loops can re-enter the flow on a cron schedule (Phase 2, per the loop-updater trigger pattern).

### Field 2 — Inputs

- `~/.hermes/loops/hermes.yaml` — the loop registry (the canonical loop list).
- `python3 ~/.hermes/tools/loop_live_audit.py` — the audit tool (9-item rubric).
- `python3 ~/.hermes/loops/intents/<loop-id>-intent.md` — the per-loop intent doc.
- `~/Documents/alice-framework/methodology/M-decide-graph-readiness.md` v0.1.1 — the 9-item rubric.
- `~/.hermes/state/skill_registry.json` — the skill registry (per op-guard-17).
- Operator's `clarify` responses (per op-guard-24 + op-guard-28) — the 4-stage HITL dispositions.

### Field 3 — Outputs

- **Audit run output** — the 9-item score per loop (JSON, default); surfaced to chat-side via the operator-DM cron.
- **Per-loop audit ticket** — `[M] [hermes] [loop-audit] <loop-id>` on the `loop-builder` board. Body carries the 4-stage HITL dispositions + `clarify-<timestamp>` decision_refs.
- **Per-loop disposition ticket** — `[M] [hermes] [loop-promotion|deferral|archive] <loop-id>` per the operator's HITL #4 choice.
- **Follow-up tickets** for missing rubric items (per op-guard-30) — one ticket per missing item per loop.
- **Updated intent doc + methodology doc** if HITL #1 REVISE — the canonical amendment path.
- **Updated yaml `status:`** if HITL #4 PROMOTE — flips after compliance-verifier passes (per op-guard-30).
- **Audit-line** to `~/Documents/HermesVault/log.md` per op-guard-11 (`vault_log.append_audit_line`).

### Field 4 — Success criteria

- All N loops in `~/.hermes/loops/hermes.yaml` audited per the 9-item rubric.
- All 4-stage HITL gates fired for each loop (unless skip conditions per op-guard-32 — live-status grandfather, 24h-disposition-cache, etc.).
- Per-loop disposition ticket filed with operator's `decision_ref` (per op-guard-10 v2 §1.2 `closed_per_decision`).
- Compliance-verifier child ticket (per op-guard-17) fires after each missing item is addressed.
- YAML `status:` flips to `live` after compliance-verifier passes (per op-guard-30 evidence + compliance-verifier chain).
- Audit-line appended per op-guard-11; the canonical 6-field record includes operator disposition timestamp, compliance-verifier ticket id, evidence files, exemption list, audit score, and flip timestamp.

### Field 5 — Retry parameters

- **Transient failures** (audit tool timeout, sqlite readonly, etc.) — retry with backoff (3 attempts, 5s/15s/45s).
- **Hard failures** (yaml unparseable, audit tool missing, methodology doc missing) — route to operator-action-dm cron (per op-guard-13).
- **Operator-decision failures** (clarify timeout, disposition rejected) — re-invoke `clarify` per op-guard-28 (with 24h skip rule per op-guard-29).
- **Compliance-verifier failures** (verifier child FAIL) — re-fire the verifier child ticket (3 attempts, 30s/2m/5m); on third FAIL, route to operator-action-dm cron.

### Field 6 — Escalation process

- **24h blocked on any stage** — escalate via operator-action-dm cron + chat-side-board-escalator (per the META `t_71b5d8df`).
- **Compliance-verifier FAIL** — route to the worker + re-dispatch (per the canonical close-gate pattern).
- **Scope/intent revision that triggers rewrites** — file rewrite tickets + re-run intent-achievement check (HITL #2) after the rewrites.
- **Per-loop disposition ticket conflict** (operator disposition + 4-stage HITL mismatch) — surface to operator-DM cron with the conflict + suggested resolution.

## Part 3: Flow topology (per `04a-decide-work-graph.md` — the 3-node-type taxonomy)

The flow has these nodes, each tagged with its type (per `04a-decide-work-graph.md` Part 4) + rationale:

| Node | Type | Rationale |
|------|------|-----------|
| **Audit** | Scripts/Code | `loop_live_audit.py` is deterministic (stdlib + sqlite + subprocess); no LLM in the loop. Same input → same output. |
| **HITL #1 (Scope/intent)** | Human | The operator owns the loop's purpose; the agent cannot decide what the loop is for. |
| **Rewrite (intent + methodology)** | Scripts/Code | Doc edits are deterministic: read intent.md + methodology doc, write the diff. The verifier (Agent node) checks the diff matches the operator's REVISE choice. |
| **HITL #2 (Intent-achievement)** | Human | The operator confirms the graph CURRENTLY achieves the intent (per op-guard-32 hitl #2). The agent cannot observe its own behavior. |
| **Compliance-verifier** | Agent | Per op-guard-17, the verifier is an Agent node: it requires judgment (does the instance conform to the doc?). The verifier is NOT the same agent that produced the loop; it's an independent profile (verifier). |
| **HITL #3 (Compliance)** | Human | The operator confirms the 9-item rubric score + per-item evidence is accurate. (Some bullets may be CONFIRMED by an Agent; the final CONFIRM is the operator's.) |
| **HITL #4 (Disposition)** | Human | The operator chooses PROMOTE / DEFER / ARCHIVE. The agent cannot make the disposition; it surfaces the choice surface. |
| **Action (flip yaml + bump intent + audit-line)** | Scripts/Code | YAML flip + intent version bump + audit-line append are deterministic. The audit-line's `disposition` field carries the operator's HITL #4 choice. |

### Flow graph

```
[Audit] → [HITL #1] → (REVISE?) → [Rewrite] → [HITL #2]
                         ↓ (CONFIRM)
                       [HITL #2]
[HITL #2] → (ACHIEVES) → [HITL #3]
         → (PARTIALLY/DOES-NOT) → [Rewrite] → [HITL #2]
[HITL #3] → (CONFIRM) → [Compliance-verifier] → [HITL #4]
         → (DISPUTE) → [Audit re-run] → [HITL #3]
         → (EXEMPTION) → [Action: document exemptions] → [HITL #4]
[HITL #4] → (PROMOTE) → [Compliance-verifier] → [Action: flip yaml to live]
         → (DEFER) → [Action: file deferral ticket]
         → (ARCHIVE) → [Action: file archive ticket]
```

### Discipline gate

Per `M-decide-graph-readiness.md` v0.1.1 Part 4 §D + Part 5 check 8, every node uses its proper type:

- **Scripts/Code nodes** (Audit, Rewrite, Action) fail loudly with deterministic error messages; no LLM in the loop.
- **Agent nodes** (Compliance-verifier) produce bounded output that the next validator enforces; the verifier is independent from the loop's producer.
- **Human nodes** (HITL #1/#2/#3/#4) gate decisions the agent cannot make on the operator's behalf.

A loop that has an Agent (LLM-driven) node doing work a script could do deterministically is not `live`-ready; the item-9 violation is a release-blocker that supersedes items 1–8. The graph-audit-promotion flow itself must conform to this discipline (per the verifier child ticket that confirms the instance conforms per op-guard-17).

## Part 4: Process (step-by-step)

1. **Master ticket** — `[MASTER] Graph audit + promotion cycle v<N>` filed on `loop-builder` board (the canonical board for loops that operate on other loops per `M-decide-loop-catalog.md`).
2. **Audit node** — fires `python3 ~/.hermes/tools/loop_live_audit.py --format json` for all N loops. Output is committed to the master ticket as a `## Audit run` section.
3. **Per-loop HITL gates** — for each loop in the audit, fire the 4 stages in order (skip conditions per op-guard-32):
   - **HITL #1 (Scope/intent)** — `clarify` with the loop's intent summary + observed behavior summary. Choices: CONFIRM / REVISE / ABANDON.
   - **HITL #2 (Intent-achievement)** — `clarify` with the actual behavior per recent runs vs HITL #1's intent. Choices: ACHIEVES / PARTIALLY-ACHIEVES / DOES-NOT-ACHIEVE.
   - **HITL #3 (Compliance)** — `clarify` with the 9-item rubric score + per-item evidence + missing items. Choices: CONFIRM / DISPUTE / EXEMPTION.
   - **HITL #4 (Disposition)** — `clarify` with the prior gate decisions + the canonical PROMOTE / DEFER / ARCHIVE.
4. **Rewrite nodes** — fire when scope/intent or intent-achievement requires graph changes (intent doc + methodology doc edits, child rewrite tickets for the graph itself).
5. **Compliance-verifier child** — per op-guard-17, fires per loop after HITL #3 passes (CONFIRM or EXEMPTION) and confirms the instance conforms to the methodology doc + intent + yaml + tests.
6. **Action node** — flips YAML `status:` + bumps intent doc `version:` + appends audit-line per the operator's HITL #4 disposition. Per op-guard-30 evidence + compliance-verifier chain.

## Part 5: Bridging the 9-item rubric to the flow

The 9-item rubric (`M-decide-graph-readiness.md` v0.1.1 Part 3) maps to the flow as follows:

| Rubric item | Audit node | Compliance-verifier |
|---|---|---|
| 1. Methodology doc | ✅ machine | Agent re-checks |
| 2. Intent doc | ✅ machine | Agent re-checks |
| 3. Yaml status | ✅ machine | Agent re-checks |
| 4. Regression suite | ✅ machine (count) | Agent runs pytest |
| 5. Compliance-verifier child | (this is what fires) | N/A |
| 6. Paired-wiki integrity | ✅ machine (git log) | Agent re-checks |
| 7. Operator review | ⚠ operator-verified | Operator survey |
| 8. End-to-end run | ✅ machine (log.md) | Agent re-checks |
| 9. Node-type discipline | ✅ machine (regex) | Agent walks the design doc |

Items 1–6 + 9 are machine-verifiable; the Audit node captures them. Item 5 is the compliance-verifier child itself (the per-loop child that fires after HITL #3). Items 7–8 are operator-verified (HITL #3 + the operator's review of past runs). The Compliance-verifier Agent re-confirms the machine-verifiable items and walks the design doc for item 9.

## Part 6: Per-loop action (the 3 dispositions)

### PROMOTE

Per op-guard-30:

1. **Evidence filing** — file follow-up tickets for any missing items (the 9-item rubric's `❌` items).
2. **Compliance-verifier child** — fires per op-guard-17; transitions to `done` with `--result "compliance: pass"` after the verifier confirms the instance conforms.
3. **YAML flip** — `status: live-pending-test → live` + intent doc `version:` bumped per semver + audit-line appended per op-guard-11.
4. **Audit-line** — captures the 6 fields: operator disposition timestamp, compliance-verifier ticket id, evidence files, exemption list, audit score, flip timestamp.

### DEFER

1. **Per-loop deferral ticket** — `[M] [hermes] [loop-deferral] <loop-id>` filed on `loop-builder` board with `closed_per_decision` metadata (per op-guard-10 v2 §1.2) + the operator's `clarify-<timestamp>` decision_ref.
2. **Re-disposition** — the loop re-enters the flow on a later cycle (24h re-prompt rule per op-guard-29).
3. **Audit-line** — captures the deferral event + the next-cycle planned date.

### ARCHIVE

1. **Per-loop archive ticket** — `[M] [hermes] [loop-archive] <loop-id>` filed on `loop-builder` board with `closed_per_decision` metadata + the operator's `clarify-<timestamp>` decision_ref.
2. **YAML change** — `status: archived` (new status) OR loop entry removed from `~/.hermes/loops/hermes.yaml` per op-guard-10 v2 §1.3 (`closed_in_error`).
3. **Intent doc + methodology doc** — retained for forensic trace (the "no auto-delete" rule per the loop-updater's `no auto-merge or auto-delete` constraint).
4. **Audit-line** — captures the archive event + the operator's rationale.

## Part 7: Application procedure

To set up the flow on the operator's instance:

1. **File the methodology doc** (this doc) on `alice-framework` board at `~/Documents/alice-framework/methodology/M-decide-graph-audit-promotion-flow.md` v0.1.0. (Doc-writer owns this.)
2. **File the loop entry** in `~/.hermes/loops/hermes.yaml` with `status: live-pending-test`, `register_with_profile: planner`, `run_with_profile: doc-writer`, `target_board: loop-builder`, `path: graph-audit-promotion`. (Coder owns this.)
3. **File the intent doc** at `~/.hermes/loops/intents/graph-audit-promotion-intent.md` v0.1.0. (Coder owns this.)
4. **File the regression suite** at `~/.hermes/tests/test_graph_audit_promotion_loop.py` v0.1.0 with 10 cases. (Coder owns this.)
5. **File the compliance-verifier child ticket** on `loop-builder` board that confirms the instance conforms to this doc. (Verifier owns this.)
6. **Trigger the master ticket** — the operator (or jarvis on the operator's behalf) files a master ticket on `loop-builder` for the first audit cycle v1. The master ticket fires the flow per Part 4.

The instance-side wiring of the flow follows the spec-first-flow rule (per op-guard-16 + op-guard-17): this doc is the alice-first; the loop yaml + intent + tests are the instance-second; the compliance-verifier is the done-gate child.

## Part 8: Cross-references

- **`methodology/04d-decide-flow-spec.md`** — the 6-field flow spec template that this doc adapts.
- **`methodology/04a-decide-work-graph.md`** — the work-graph state machine + the 3-node-type taxonomy (Part 4) that this flow conforms to.
- **`methodology/04c-decide-master-ticket.md`** — the master-ticket pattern (the master ticket is the orchestration entry; per-loop tickets are the children).
- **`methodology/M-decide-graph-readiness.md` v0.1.1** — the 9-item rubric + the 4-stage HITL process (this flow's audit + per-loop action).
- **`methodology/M-decide-spec-first-flow.md`** — the 4-phase flow + verifier steps. Part 2's phase-3 ("update environment") is where this flow ships.
- **`methodology/M-decide-instance-vs-framework.md`** — the framework-vs-instance distinction. This doc is framework-side; the loop entry + intent + tests are instance-side.
- **`2-ATOMIC/rules/op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md`** — alice-first / instance-second. This doc is the alice-first; the loop yaml + intent + tests are the instance-second.
- **`2-ATOMIC/rules/op-guard-29-per-loop-hitl-gate-2026-08-15.md`** — the per-loop HITL gate (PROMOTE / DEFER / ARCHIVE). This flow codifies the gate within the audit cycle.
- **`2-ATOMIC/rules/op-guard-30-promotion-requires-evidence-or-exemptions-2026-08-17.md`** — promotion requires evidence + 4-field exemption schema. The flow's PROMOTE branch follows this rule.
- **`2-ATOMIC/rules/op-guard-31-three-stage-graph-hitl-2026-08-17.md`** — superseded by op-guard-32.
- **`2-ATOMIC/rules/op-guard-32-four-stage-graph-hitl-2026-08-17.md`** — the canonical 4-stage HITL pattern (scope/intent → intent-achievement → compliance → disposition). This flow is the operational surface for op-guard-32.
- **`2-ATOMIC/rules/op-guard-11-log-md-append-only-2026-07-22.md`** — audit-line discipline. Every flow transition appends an audit-line via `vault_log.append_audit_line`.
- **`2-ATOMIC/rules/op-guard-10-no-close-on-age-2026-07-27.md`** — closure evidence. The per-loop disposition ticket's `closed_per_decision` metadata carries the `decision_ref` (the operator's `clarify-<timestamp>`).

## Part 9: Audit-line

`## [2026-08-17T13:25Z] doc-writer-shipped — methodology/M-decide-graph-audit-promotion-flow.md v0.1.0 written. Codifies the graph-audit-promotion flow per the 6-field template (04d-decide-flow-spec.md) + the 4-stage HITL gates (op-guard-32) + the 3-node-type topology (Scripts/Code + Agents + Human, per 04a-decide-work-graph.md Part 4). Per-loop action: PROMOTE (evidence + compliance-verifier chain per op-guard-30); DEFER (per-loop deferral ticket with closed_per_decision); ARCHIVE (per-loop archive ticket). Audit-line discipline per op-guard-11. Source: t_3b49e1bc (operator 2026-08-17: "we have a loop builder board. Let's follow our own spec and create a flow on the board which will do the audits and promotion").`
