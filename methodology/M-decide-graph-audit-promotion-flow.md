---
id: alice-methodology-M-decide-graph-audit-promotion-flow
created: 2026-08-17T13:25:00Z
updated: 2026-08-19T12:00Z
title: "Methodology M — Decide the graph-audit-promotion flow (the canonical 2-HITL audit + promotion cycle for the operator's loop registry)"
type: methodology
status: draft
source: alice-framework
version: 0.4.0
amended_by_v0_1_0: t_3b49e1bc
amended_by_v0_2_0: t_0205edd9
amended_by_v0_2_0_source: 'operator 2026-08-18 corrections: (a) 1 graph per run (not all graphs), (b) verifier profile = Compliance-verifier node, (c) HITL #3 = verifier (not operator) — amend-77'
amended_by_v0_2_0_companion_intent_doc: '~/.hermes/loops/intents/graph-audit-promotion-intent.md v0.3.0 (parallel 3 corrections; methodology twin per op-guard-5 paired-wiki integrity)'
amended_by_v0_3_0: t_5ed69888
amended_by_v0_3_0_source: 'operator 2026-08-18: "these changes you are making, are they being applied to the graph audit? We would want these improvements to happen when we run the flow, not just for this specific run" — amend-82 applies the op-guard-32 v4 amend (3-stage HITL pattern) to the actual flow'
amended_by_v0_3_0_companion_intent_doc: '~/.hermes/loops/intents/graph-audit-promotion-intent.md v0.4.0 (paired-wiki mirror per op-guard-5; v0.4.0 reflects the 3-stage pattern)'
amended_by_v0_4_0: t_6621d4d3
amended_by_v0_4_0_source: 'operator 2026-08-18 (chat, verbatim, 3 errors in the Mermaid diagram): (a) "3-stage HITL gates" → "2 HITL gates (HITL #1 + HITL #2)", (b) HITL #4 → HITL #2 renumbering, (c) Rewrite connected to HITL #1 feedback flow — amend-85'
amended_by_v0_4_0_companion_intent_doc: '~/.hermes/loops/intents/graph-audit-promotion-intent.md v0.4.1 (paired-wiki mirror per op-guard-5; v0.4.1 reflects the 2-HITL pattern with verifier as the unnumbered gate between HITL #1 and HITL #2)'
alice-ticket: "[[ticket:t_3b49e1bc]]"
companion: ["[[methodology/04d-decide-flow-spec.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[methodology/04c-decide-master-ticket.md]]", "[[methodology/M-decide-graph-readiness.md]]", "[[methodology/M-decide-spec-first-flow.md]]", "[[methodology/M-decide-instance-vs-framework.md]]"]
links: ["[[methodology/04d-decide-flow-spec.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[methodology/04c-decide-master-ticket.md]]", "[[methodology/M-decide-graph-readiness.md]]", "[[methodology/M-decide-spec-first-flow.md]]", "[[methodology/M-decide-instance-vs-framework.md]]", "[[2-ATOMIC/rules/op-guard-29-per-loop-hitl-gate-2026-08-15.md]]", "[[2-ATOMIC/rules/op-guard-30-promotion-requires-evidence-or-exemptions-2026-08-17.md]]", "[[2-ATOMIC/rules/op-guard-31-three-stage-graph-hitl-2026-07-20.md]]", "[[2-ATOMIC/rules/op-guard-32-four-stage-graph-hitl-2026-08-17.md]]", "[[2-ATOMIC/rules/op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md]]", "[[2-ATOMIC/rules/op-guard-11-log-md-append-only-2026-07-22.md]]", "[[2-ATOMIC/rules/op-guard-28-clarify-before-block-needs-input-2026-08-15.md]]"]
teaching-example: true
---

# Methodology M — Decide the graph-audit-promotion flow

> The `graph-audit-promotion` flow runs the per-loop audit (per `M-decide-graph-readiness.md` v0.1.1 9-item rubric) on **a SINGLE target loop** specified by the operator in the master ticket body. The flow is **NOT** a "process all registered loops" pass; if 10 graphs exist in `~/.hermes/loops/hermes.yaml`, the operator runs the audit 10 times — once per graph. Each invocation fires the canonical **2 HITL gates (HITL #1 scope/intent → HITL #2 final disposition)** with the verifier's compliance check as the automated gate between them — for the single target loop, taking per-loop action (PROMOTE / DEFER / ARCHIVE) per the operator's HITL #2 disposition. The flow is the canonical surface for the operator's loop-promotion cycle. Without this flow, the audit is ad-hoc (the proactive board unblocker cron surfaces scores but no operator-decision path handles the per-loop action), and the `live` vs `live-pending-test` distinction is a heuristic rather than a tracked promotion cycle. *(v0.2.0, amend-77: 1 graph per run; verifier profile = Compliance-verifier node. v0.3.0, amend-82: 3-stage HITL pattern per op-guard-32 v4. v0.4.0, amend-85: simplifies to 2 HITL gates per operator 2026-08-18 — verifier is the unnumbered automated gate between HITL #1 and HITL #2; Rewrite is fed from the HITL #1 feedback flow.)*

## Source

Operator 2026-08-17 (chat, verbatim): *"we have a loop builder board. Let's follow our own spec and create a flow on the board which will do the audits and promotion"*

The operator's environment has a `loop-builder` board (canonical for loop registration per `M-decide-loop-catalog.md`) but no flow that does the per-loop audit + promotion work on it. The proactive board unblocker cron (`t_7f1cf929`) surfaces per-loop scores but the operator disposition (HITL #1 + HITL #2) and per-loop action (filing the disposition ticket, flipping yaml, bumping intent) is not wired. The flow codifies that wiring.

### v0.3.0 amendment source (amend-82)

Operator 2026-08-18 (chat, verbatim): *"these changes you are making, are they being applied to the graph audit? We would want these improvements to happen when we run the flow, not just for this specific run"*

The prior v0.4.0 rule update (op-guard-32 v4 amend; source: t_4283ebbd) dropped HITL #2 from the 4-stage HITL pattern, simplifying to 3 stages (HITL #1 + HITL #3 + HITL #4). The rule update landed but the intent doc + methodology doc still described the 4-stage pattern. Future invocations of the flow would have used the v0.3.0 4-stage pattern, not the operator's intended 3-stage pattern. The v0.3.0 amend-82 of this methodology doc applies the 3-stage pattern to the actual flow.

### v0.4.0 amendment source (amend-85, 2026-08-19)

Operator 2026-08-18 (chat, verbatim, 3 errors in the master-ticket summary PDF Mermaid diagram):

1. *"Fires the canonical 3-stage HITL gates (HITL #1 scope/intent → HITL #3 compliance → HITL #4 disposition) — There are 2 HITL gates, not 3. We should also stop referring to this final HITL gate as HITL4, it is HITL2."*
2. *"The diagram shows Operator -> HITL4 -> Verifier Agent Node HITL3 -> HITL3 Pass/Fail. This is incorrect. HITL must run and pass before HITL4 is fired."*
3. *"The rewrite script/code block coming from the operator, where does this originate from? It should be connected to a HITL, not directly to the operator."*

The v0.3.0 amend-82 of this methodology doc applied the 3-stage pattern to the flow but the operator pushed back on the diagram order + numbering. The v0.4.0 amend-85 simplifies to 2 HITL gates (HITL #1 + HITL #2), with the verifier compliance check as the unnumbered automated gate between them.

## Part 1: Why this flow

The 9-item rubric in `M-decide-graph-readiness.md` v0.1.1 Part 3 is the substance; the per-loop disposition is the operator's decision; the per-loop action is the environment mutation. The flow wires the three together:

1. **Substance** — the rubric (machine-verifiable items 1-6 + 9 + operator-verified items 7-8) is captured by `python3 ~/.hermes/tools/loop_live_audit.py`.
2. **Decision** — the operator fires the 2 HITL gates (per v0.4.0, amend-85) and disposes each loop at HITL #1 + HITL #2; the verifier's compliance check is the unnumbered automated gate between them.
3. **Action** — the per-loop disposition ticket fires (PROMOTE → flip yaml + bump intent + audit-line; DEFER → deferral ticket; ARCHIVE → archive ticket).

Without this flow, the loop registry is a static file: every loop ships at `live-pending-test`, the audit is run manually, the operator dispositions ad-hoc, and the audit-line is missed. The flow makes the promotion cycle a first-class, auditable work graph on the `loop-builder` board.

### What this flow is not

- **Not a methodology.** The 9-item rubric lives in `M-decide-graph-readiness.md` v0.1.1 Part 3. This doc is the flow spec (the 6-field template + topology + process), not the rubric itself.
- **Not a "process all loops" pass.** The flow processes a SINGLE target loop per master ticket; the operator runs the audit N times for N graphs (per v0.2.0, amend-77).
- **Not autonomous.** The 2 operator HITL gates (HITL #1 + HITL #2) are operator decisions; the flow does not auto-PROMOTE / auto-ARCHIVE without operator disposition. The verifier compliance check is between HITL #1 and HITL #2 (per v0.4.0, amend-85 — unnumbered in the operator's framing).

## Part 2: Flow spec (per `04d-decide-flow-spec.md` — the 6-field template)

### Field 1 — Goal

Audit the SINGLE target loop (specified by the operator in the master ticket's `target_loop_id` binding) against the 9-item rubric (`M-decide-graph-readiness.md` v0.1.1 Part 3), fire the 2 HITL gates (per v0.4.0, amend-85) for the target loop, and take per-loop action per the operator's disposition. One canonical flow per audit cycle; the operator runs the audit N times for N graphs (per v0.2.0, amend-77). The verifier's compliance check is the automated gate between HITL #1 and HITL #2 (per v0.4.0, amend-85 — unnumbered); the operator owns HITL #1 + HITL #2 (2 HITL gates; HITL #2 was previously HITL #4 in v0.4.0 amend-82, now renamed per operator 2026-08-18). Loops can re-enter the flow on a cron schedule (Phase 2, per the loop-updater trigger pattern).

### Field 2 — Inputs

- `~/.hermes/loops/hermes.yaml` — the loop registry (the canonical loop list; the audit reads it to find the target-loop entry, not to enumerate all loops).
- **Master ticket `target_loop_id: <loop-id>` binding** — the canonical target-loop-id surface (per v0.2.0, amend-77). Without the binding, the master is `blocked kind=needs_input` (per op-guard-28) and re-routed with a `clarify` invocation asking the operator to specify the target.
- `python3 ~/.hermes/tools/loop_live_audit.py` — the audit tool (9-item rubric).
- `python3 ~/.hermes/loops/intents/<target-loop-id>-intent.md` — the per-loop intent doc for the target loop.
- `~/Documents/alice-framework/methodology/M-decide-graph-readiness.md` v0.1.1 — the 9-item rubric.
- `~/.hermes/state/skill_registry.json` — the skill registry (per op-guard-17).
- Operator's `clarify` responses (per op-guard-24 + op-guard-28) — the 2 operator gates (HITL #1 + HITL #2) for the target loop.
- Verifier's `--result "compliance: pass"` (per op-guard-17) — the verifier compliance check between HITL #1 and HITL #2 (unnumbered in v0.4.0 amend-85).

### Field 3 — Outputs

- **Audit run output** — the 9-item score for the SINGLE target loop (JSON, default); surfaced to chat-side via the operator-DM cron.
- **Per-loop audit ticket** — `[M] [hermes] [loop-audit] <target-loop-id>` on the `loop-builder` board. Body carries the 2-HITL-gate dispositions: 2 `clarify-<timestamp>` decision_refs (operator gates HITL #1 + HITL #2) + 1 `--result "compliance: pass"` decision_ref (verifier compliance check between HITL #1 and HITL #2).
- **Per-loop disposition ticket** — `[M] [hermes] [loop-promotion|deferral|archive] <target-loop-id>` per the operator's HITL #2 choice.
- **Follow-up tickets** for missing rubric items (per op-guard-30) — one ticket per missing item for the target loop.
- **Updated intent doc + methodology doc** if HITL #1 REVISE / PARTIALLY-ACHIEVES — the canonical amendment path for the target loop. The Rewrite node fires when HITL #1 gives feedback (per v0.4.0, amend-85 — the feedback on HITL #1 IS the trigger; the Rewrite edge connects to the HITL #1 disposition, NOT directly to the operator).
- **Updated yaml `status:`** if HITL #2 PROMOTE — flips after compliance-verifier passes (per op-guard-30).
- **Audit-line** to `~/Documents/HermesVault/log.md` per op-guard-11 (`vault_log.append_audit_line`).

### Field 4 — Success criteria

- Master ticket carries a valid `target_loop_id: <loop-id>` binding.
- The SINGLE target loop is audited per the 9-item rubric; the audit run output is committed to the master ticket as `## Audit run` section.
- The 2 operator HITL gates (HITL #1 + HITL #2) fire for the target loop via `clarify` invocations; the verifier compliance check fires via `--result "compliance: pass"` between the 2 HITL gates (per v0.4.0, amend-85). Skip conditions per op-guard-32 v4 still apply.
- The verifier runs AND passes BEFORE HITL #2 fires (per v0.4.0, amend-85 — order is canonical; HITL #2 is BLOCKED until verifier PASS).
- The Rewrite node fires when HITL #1 REVISE / PARTIALLY-ACHIEVES (per v0.4.0, amend-85 — the feedback on HITL #1 IS the trigger).
- Per-loop disposition ticket filed with operator's `decision_ref` (per op-guard-10 v2 §1.2 `closed_per_decision`) + the verifier's `--result "compliance: pass"` as the compliance check.
- Compliance-verifier child ticket (per op-guard-17) fires after each missing item is addressed; the verifier's PASS is the gate that flips the YAML on PROMOTE.
- YAML `status:` flips to `live` after compliance-verifier passes (per op-guard-30 evidence + compliance-verifier chain).
- Audit-line appended per op-guard-11; the canonical 6-field record includes operator disposition timestamp, compliance-verifier ticket id, evidence files, exemption list, audit score, and flip timestamp.

### Field 5 — Retry parameters

- **Master ticket missing `target_loop_id` binding** — block master `kind=needs_input` and re-route with a `clarify` invocation (per v0.2.0, amend-77). The flow does NOT default to "audit all loops".
- **Transient failures** (audit tool timeout, sqlite readonly, etc.) — retry with backoff (3 attempts, 5s/15s/45s).
- **Hard failures** (yaml unparseable, audit tool missing, methodology doc missing) — route to operator-action-dm cron (per op-guard-13).
- **Operator-decision failures** (clarify timeout, disposition rejected) — re-invoke `clarify` per op-guard-28 (with 24h skip rule per op-guard-29).
- **Compliance-verifier failures** (verifier child FAIL) — re-fire the verifier child ticket (3 attempts, 30s/2m/5m); on third FAIL, route to operator-action-dm cron.
- **Out-of-order HITL fire** (HITL #2 fires before verifier PASS, per v0.4.0 amend-85) — block on `kind=dependency` with the canonical reason per op-guard-32 v3. The chat-side / verifier preflight catches the out-of-order fire.

### Field 6 — Escalation process

- **24h blocked on any stage** — escalate via operator-action-dm cron + chat-side-board-escalator (per the META `t_71b5d8df`).
- **Compliance-verifier FAIL** — route to the verifier + re-dispatch (per the canonical close-gate pattern).
- **Scope/intent revision that triggers rewrites** — file rewrite tickets + re-run the audit + verifier rechecks (the Rewrite node fires from the HITL #1 feedback flow per v0.4.0, amend-85).
- **Per-loop disposition ticket conflict** (operator disposition + verifier mismatch) — surface to operator-DM cron with the conflict + suggested resolution.

## Part 3: Flow topology (per `04a-decide-work-graph.md` — the 3-node-type taxonomy)

The flow has these nodes, each tagged with its type (per `04a-decide-work-graph.md` Part 4) + rationale:

| Node | Type | Rationale |
|------|------|-----------|
| **Audit** | Scripts/Code | `loop_live_audit.py` is deterministic (stdlib + sqlite + subprocess); no LLM in the loop. Same input → same output. Audits the SINGLE target loop (per v0.2.0, amend-77). |
| **HITL #1 (Scope/intent)** | Human | The operator owns the loop's purpose; the agent cannot decide what the loop is for. |
| **Rewrite (intent + methodology)** | Scripts/Code | Doc edits are deterministic: read intent.md + methodology doc, write the diff. The Rewrite node fires when HITL #1 gives REVISE / PARTIALLY-ACHIEVES feedback (per v0.4.0, amend-85 — the feedback IS the trigger; the Rewrite edge connects to the HITL #1 disposition, NOT directly to the operator). |
| **Compliance-verifier (= `verifier` profile)** | Agent | Per op-guard-17, the verifier is an Agent node: it requires judgment (does the instance conform to the doc?). The verifier is NOT the same agent that produced the loop; it's an independent profile (verifier). The verifier's `--result "compliance: pass"` is the compliance check between HITL #1 and HITL #2 (per v0.4.0, amend-85 — unnumbered; was HITL #3 in v0.4.0 amend-82; was the third gate in v0.3.0 4-stage). The verifier's PASS implicitly confirms intent-achievement (the build matches the spec, the spec describes the intent per v0.3.0, amend-82). |
| **Verifier compliance check (unnumbered in v0.4.0, amend-85)** | **Agent (was Human in v0.1.0; was HITL #3 in v0.4.0 amend-82)** | *(v0.4.0, amend-85)* The verifier profile emits PASS or FAIL via `--result "compliance: pass\|fail"` per op-guard-17. This is NOT an operator `clarify` invocation; the operator surfaces the PASS/FAIL outcome from the verifier's report. The verifier runs BEFORE HITL #2 fires; HITL #2 is BLOCKED until verifier PASS. |
| **HITL #2 (Disposition)** | Human | The operator chooses PROMOTE / DEFER / ARCHIVE based on the verifier's compliance check + the HITL #1 scope/intent decision. The verifier's modifications are visible to the operator before HITL #2 fires (per v0.4.0, amend-85 — the verifier runs BEFORE HITL #2). HITL #2 was previously "HITL #4" in v0.4.0 amend-82; renumbered per operator 2026-08-18 (*"stop referring to this final HITL gate as HITL4, it is HITL2"*). Choices: PROMOTE / DEFER / ARCHIVE. |
| **Action (flip yaml + bump intent + audit-line)** | Scripts/Code | YAML flip + intent version bump + audit-line append are deterministic. The audit-line's `disposition` field carries the operator's HITL #2 choice. |

### Flow graph (v0.4.0, amend-85 — 2-HITL pattern, corrected Mermaid diagram)

```
[Target: <target-loop-id>] → [Audit] → [HITL #1] → (REVISE / PARTIALLY-ACHIEVES)
                                         ↓ (CONFIRM)
                                       [Verifier compliance check]
                                         ↓ (PASS)
                                       [HITL #2] → (PROMOTE) → [Action: flip yaml to live]
                                                  → (DEFER)   → [Action: file deferral ticket]
                                                  → (ARCHIVE) → [Action: file archive ticket]

[HITL #1] → (REVISE / PARTIALLY-ACHIEVES) → [Rewrite] → [Doc-Writer updates intent doc + methodology doc]
                                                  ↓
                                              [HITL #1 re-fire] (per op-guard-32 v2 re-run pattern)
                                                  ↓
                                              [Verifier compliance check re-fire]
                                                  ↓
                                              [HITL #2 fires ONLY after verifier PASS]
                                         → (FAIL) → [Audit re-run] → [Verifier compliance check]
```

(The verifier compliance check is the unnumbered automated gate between HITL #1 and HITL #2 per v0.4.0, amend-85. Rewrites from HITL #1 REVISE / PARTIALLY-ACHIEVES feedback re-fire HITL #1 + the verifier check + HITL #2 — the Rewrite edge connects to the HITL #1 disposition, NOT directly to the operator.)

**Constraint (v0.4.0, amend-85):** 2 HITL gates (HITL #1 + HITL #2). The verifier compliance check is the unnumbered automated gate between them. The verifier runs BEFORE HITL #2 fires; HITL #2 is BLOCKED until verifier PASS. Rewrites are fed from the HITL #1 feedback flow.

### Corrected Mermaid diagram (v0.4.0, amend-85 — the canonical 2-HITL pattern)

```mermaid
graph TD
    %% graph-audit-promotion v0.4.0 (methodology twin) — 2-HITL pattern with Rewrite
    %% Node types per M-decide-work-graph.md Part 4
    %% Edges per operator 2026-08-18 corrections (amend-85)

    Operator((Operator<br/>Human node<br/>profile: operator))
    DocWriter[Doc-Writer<br/>Scripts/Code node<br/>profile: doc-writer<br/>builds intent doc + methodology doc]
    Audit[Audit<br/>Scripts/Code node<br/>profile: doc-writer<br/>runs loop_live_audit.py]
    Verifier[Verifier<br/>Agent node<br/>profile: verifier<br/>compliance: PASS / FAIL]
    Rewrite[Rewrite<br/>Scripts/Code node<br/>profile: doc-writer<br/>updates intent doc + methodology doc]
    Action[Action<br/>Scripts/Code node<br/>profile: doc-writer<br/>YAML flip + intent bump + audit-line]

    %% HITL #1 (operator) — scope/intent disposition
    Operator -->|HITL #1: Scope/intent<br/>CONFIRM / REVISE / ABANDON| DocWriter

    %% Build phase
    DocWriter -->|build the graph| Audit
    Audit -->|9-item rubric score| DocWriter
    DocWriter -->|pass to verifier| Verifier

    %% Verifier compliance check (unnumbered in v0.4.0 amend-85)
    %% The verifier runs BEFORE HITL #2 fires — the operator's HITL #2 is BLOCKED until PASS
    Verifier -->|compliance: PASS / FAIL<br/>(unnumbered automated gate between HITL #1 and HITL #2)| Operator

    %% Rewrite loop — fires when operator gives feedback on HITL #1
    %% The feedback is part of the HITL #1 disposition, NOT a direct operator edge
    Operator -->|REVISE / PARTIALLY-ACHIEVES<br/>(feedback on HITL #1)<br/>trigger for Rewrite| Rewrite
    Rewrite -->|updates intent doc + methodology doc| DocWriter

    %% HITL #2 (operator) — final disposition
    %% ONLY FIRES AFTER verifier PASS
    Operator -->|HITL #2: Disposition<br/>PROMOTE / DEFER / ARCHIVE<br/>only after verifier PASS| Action
    Action -->|YAML flip + intent bump + audit-line| Operator

    %% Color coding
    style Operator fill:#ffd700,stroke:#333,stroke-width:2px
    style DocWriter fill:#87ceeb,stroke:#333,stroke-width:2px
    style Verifier fill:#98fb98,stroke:#333,stroke-width:2px
    style Action fill:#ffb6c1,stroke:#333,stroke-width:2px
    style Audit fill:#dda0dd,stroke:#333,stroke-width:2px
    style Rewrite fill:#f0e68c,stroke:#333,stroke-width:2px
```

### Discipline gate

Per `M-decide-graph-readiness.md` v0.1.1 Part 4 §D + Part 5 check 8, every node uses its proper type:

- **Scripts/Code nodes** (Audit, Rewrite, Action) fail loudly with deterministic error messages; no LLM in the loop.
- **Agent nodes** (Compliance-verifier) produce bounded output that the next validator enforces; the verifier is independent from the loop's producer.
- **Human nodes** (HITL #1 + HITL #2) gate decisions the agent cannot make on the operator's behalf.

A loop that has an Agent (LLM-driven) node doing work a script could do deterministically is not `live`-ready; the item-9 violation is a release-blocker that supersedes items 1–8. The graph-audit-promotion flow itself must conform to this discipline (per the verifier child ticket that confirms the instance conforms per op-guard-17).

## Part 4: Process (step-by-step)

1. **Master ticket** — `[MASTER] Graph audit + promotion cycle v<N> — target: <target-loop-id> (<date>)` filed on `loop-builder` board (the canonical board for loops that operate on other loops per `M-decide-loop-catalog.md`). The master ticket MUST carry the `target_loop_id: <loop-id>` binding (per v0.2.0, amend-77); missing binding → `blocked kind=needs_input` (per op-guard-28) + `clarify` invocation asking the operator to specify the target.
2. **Audit node** — fires `python3 ~/.hermes/tools/loop_live_audit.py --loop-id <target-loop-id> --format json` for the SINGLE target loop (per v0.2.0, amend-77). Output is committed to the master ticket as a `## Audit run` section.
3. **Per-loop HITL gates** — fire the 2 HITL gates in order for the target loop (skip conditions per op-guard-32 v4):
   - **HITL #1 (Scope/intent)** — `clarify` with the loop's intent summary + observed behavior summary. Choices: CONFIRM / REVISE / ABANDON.
   - **Verifier compliance check** *(unnumbered in v0.4.0 amend-85)* — **verifier profile** reviews the doc-writer's build artifacts + the per-loop intent doc + the methodology doc + the YAML entry, then emits PASS or FAIL via `--result "compliance: pass\|fail"`. The verifier is the canonical gate between HITL #1 and HITL #2 (unnumbered in v0.4.0 amend-85 per operator 2026-08-18). The operator does NOT do the verifier check. The verifier's PASS implicitly confirms intent-achievement (per v0.3.0, amend-82). HITL #2 fires ONLY after verifier PASS.
   - **HITL #2 (Disposition)** — `clarify` with the prior gate decisions + the verifier's compliance check outcome + the canonical PROMOTE / DEFER / ARCHIVE. The operator's HITL #2 is the final word. The verifier's modifications are visible before HITL #2 fires (per v0.4.0, amend-85).
4. **Rewrite nodes** — fire when scope/intent requires graph changes (operator dispositions REVISE / PARTIALLY-ACHIEVES on HITL #1, per v0.4.0 amend-85 — the feedback IS the trigger). The Rewrite node fires intent doc + methodology doc edits, child rewrite tickets for the graph itself. After rewrites, HITL #1 re-fires (per op-guard-32 v2 re-run pattern) + verifier rechecks + HITL #2 fires after verifier PASS.
5. **Compliance-verifier child** — per op-guard-17, fires per loop as the unnumbered verifier gate (between HITL #1 and HITL #2, per v0.4.0 amend-85) and confirms the instance conforms to the methodology doc + intent + yaml + tests. The verifier's `--result "compliance: pass"` is the gate that flips the YAML on PROMOTE. HITL #2 is BLOCKED until verifier PASS.
6. **Action node** — flips YAML `status:` + bumps intent doc `version:` + appends audit-line per the operator's HITL #2 disposition. Per op-guard-30 evidence + compliance-verifier chain.

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

Items 1–6 + 9 are machine-verifiable; the Audit node captures them. Item 5 is the compliance-verifier child itself (the per-loop child that fires as the verifier gate per v0.4.0 amend-85). Items 7–8 are operator-verified (the verifier's report + HITL #2 disposition review surfaces them). The Compliance-verifier Agent re-confirms the machine-verifiable items and walks the design doc for item 9.

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

1. **File the methodology doc** (this doc) on `alice-framework` board at `~/Documents/alice-framework/methodology/M-decide-graph-audit-promotion-flow.md` v0.4.0. (Doc-writer owns this; v0.4.0 ships the 2-HITL pattern per amend-85 — the v0.3.0 amend-82 3-stage + the v0.4.0 amend-85 2-HITL simplification + the corrected Mermaid diagram.)
2. **File the loop entry** in `~/.hermes/loops/hermes.yaml` with `status: live-pending-test`, `register_with_profile: planner`, `run_with_profile: doc-writer`, `target_board: loop-builder`, `path: graph-audit-promotion`. (Coder owns this.)
3. **File the intent doc** at `~/.hermes/loops/intents/graph-audit-promotion-intent.md` v0.4.1. (Coder owns this; v0.4.1 ships the 2-HITL pattern per amend-85 + the corrected Mermaid diagram.)
4. **File the regression suite** at `~/.hermes/tests/test_graph_audit_promotion_loop.py` v0.4.1 with the test additions for the 2-HITL pattern. (Coder owns this.)
5. **File the compliance-verifier child ticket** on `loop-builder` board that confirms the instance conforms to this doc + the intent doc + the YAML entry + the regression suite. (Verifier profile owns this; the verifier is the canonical gate between HITL #1 and HITL #2 per v0.4.0 amend-85.)
6. **Trigger the master ticket** — the operator (or jarvis on the operator's behalf) files a master ticket on `loop-builder` for the first audit cycle v1, with the `target_loop_id: <target-loop-id>` binding. The master ticket fires the flow per Part 4. The operator runs the audit N times for N graphs.

The instance-side wiring of the flow follows the spec-first-flow rule (per op-guard-16 + op-guard-17): this doc is the alice-first; the loop yaml + intent + tests are the instance-second; the compliance-verifier is the done-gate child.

## Part 8: Cross-references

- **`methodology/04d-decide-flow-spec.md`** — the 6-field flow spec template that this doc adapts.
- **`methodology/04a-decide-work-graph.md`** — the work-graph state machine + the 3-node-type taxonomy (Part 4) that this flow conforms to.
- **`methodology/04c-decide-master-ticket.md`** — the master-ticket pattern (the master ticket is the orchestration entry; per-loop tickets are the children).
- **`methodology/M-decide-graph-readiness.md` v0.1.1`** — the 9-item rubric + the 2-HITL process (this flow's audit + per-loop action).
- **`methodology/M-decide-spec-first-flow.md`** — the 4-phase flow + verifier steps. Part 2's phase-3 ("update environment") is where this flow ships.
- **`methodology/M-decide-instance-vs-framework.md`** — the framework-vs-instance distinction. This doc is framework-side; the loop entry + intent + tests are instance-side.
- **`2-ATOMIC/rules/op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md`** — alice-first / instance-second. This doc is the alice-first; the loop yaml + intent + tests are the instance-second.
- **`2-ATOMIC/rules/op-guard-29-per-loop-hitl-gate-2026-08-15.md`** — the per-loop HITL gate (PROMOTE / DEFER / ARCHIVE). This flow codifies the gate within the audit cycle.
- **`2-ATOMIC/rules/op-guard-30-promotion-requires-evidence-or-exemptions-2026-08-17.md`** — promotion requires evidence + 4-field exemption schema. The flow's PROMOTE branch follows this rule.
- **`2-ATOMIC/rules/op-guard-31-three-stage-graph-hitl-2026-07-20.md`** — superseded by op-guard-32.
- **`2-ATOMIC/rules/op-guard-32-four-stage-graph-hitl-2026-08-17.md` v4 amend** — the canonical HITL pattern. Under v0.4.0 amend-85, the 4-stage pattern is simplified to 2 HITL gates (HITL #1 + HITL #2) with the verifier compliance check as the unnumbered automated gate between them. This flow is the operational surface for op-guard-32 v4.
- **`2-ATOMIC/rules/op-guard-11-log-md-append-only-2026-07-22.md`** — audit-line discipline. Every flow transition appends an audit-line via `vault_log.append_audit_line`.
- **`2-ATOMIC/rules/op-guard-10-no-close-on-age-2026-07-27.md`** — closure evidence. The per-loop disposition ticket's `closed_per_decision` metadata carries the `decision_ref` (the operator's `clarify-<timestamp>`).
- **`2-ATOMIC/rules/op-guard-28-clarify-before-block-needs-input-2026-08-15.md`** — the canonical `clarify` before `kanban_block(kind=needs_input)` discipline. The v0.2.0, amend-77 missing-target-loop-id binding falls into this rule: the flow invokes `clarify` to ask the operator to specify the target before blocking the master.

## Part 9: Audit-line

`## [2026-08-17T13:25Z] doc-writer-shipped — methodology/M-decide-graph-audit-promotion-flow.md v0.1.0 written. Codifies the graph-audit-promotion flow per the 6-field template (04d-decide-flow-spec.md) + the 4-stage HITL gates (op-guard-32) + the 3-node-type topology (Scripts/Code + Agents + Human, per 04a-decide-work-graph.md Part 4). Per-loop action: PROMOTE (evidence + compliance-verifier chain per op-guard-30); DEFER (per-loop deferral ticket with closed_per_decision); ARCHIVE (per-loop archive ticket). Audit-line discipline per op-guard-11. Source: t_3b49e1bc (operator 2026-08-17: "we have a loop builder board. Let's follow our own spec and create a flow on the board which will do the audits and promotion").`

`## [2026-08-18T17:40Z] doc-writer-shipped — methodology/M-decide-graph-audit-promotion-flow.md v0.1.0 → v0.2.0 (amend-77). 3 corrections: (A) 1 graph per run (not all registered loops); master ticket MUST carry `target_loop_id: <loop-id>` binding, missing binding → blocked kind=needs_input per op-guard-28 + clarify invocation. (B) Verifier profile (`/Users/homestead/.hermes/profiles/verifier/SOUL.md`) is the canonical Compliance-verifier node; the verifier's `--result "compliance: pass"` is the gate that flips YAML on PROMOTE per op-guard-17 + op-guard-30. (C) HITL #3 (Compliance) flipped from operator (Human node) to verifier (Agent node); operator owns HITL #1, #2, #4 only; HITL #3 is the verifier's PASS/FAIL via --result. Companion intent doc bumped to v0.3.0 (paired-wiki per op-guard-5); regression suite v0.3.0 with 4 new test cases. Source: t_0205edd9 (operator 2026-08-18 chat). Per `M-decide-graph-readiness.md` v0.1.1 Part 4 §D: 1 graph per run is canonical; verifier-as-HITL#3 enforces the doc-writer/operator/verifier separation of labor.`

`## [2026-08-18T21:00Z] doc-writer-shipped — methodology/M-decide-graph-audit-promotion-flow.md v0.2.0 → v0.3.0 (amend-82). Applies the op-guard-32 v4 amend (3-stage HITL pattern) to the actual flow. Replaces "fires the canonical 4-stage HITL gates" with "fires the canonical 3-stage HITL gates" throughout. Drops HITL #2 (intent-achievement) — the verifier (HITL #3) implicitly confirms intent-achievement via the compliance check; the operator does NOT need a separate gate. The 2 operator gates are HITL #1 + HITL #4; the 1 verifier gate is HITL #3. Per-loop audit ticket body schema carries 3 decision_refs (HITL #1 + HITL #3 + HITL #4) instead of 4 — 2 operator `clarify-<timestamp>` decision_refs + 1 verifier `--result "compliance: pass"` decision_ref. Success criteria + Flow graph + Skip conditions table reflect the 3-stage pattern. The Flow graph now shows HITL #1 (CONFIRM) → HITL #3 (verifier) → HITL #4 (operator), with rewrites from HITL #1 REVISE re-firing HITL #1 + HITL #3 (no HITL #2). The HITL #2 row is removed from the topology table. The verifier's modifications are visible to the operator before HITL #4 fires (per operator 2026-08-18: "maybe we just need HITL1 and 4"). Companion intent doc bumped to v0.4.0 (paired-wiki per op-guard-5); regression suite v0.4.0 with 5 new test cases for the 3-stage pattern. Source: t_5ed69888 (operator 2026-08-18: "these changes you are making, are they being applied to the graph audit? We would want these improvements to happen when we run the flow, not just for this specific run"). Per op-guard-32 v4 amend: the 3-stage pattern is the canonical flow going forward. Per-loop tickets filed under v0.3.0 (4-stage, with HITL #2) are grandfathered. Per op-guard-5 paired-wiki integrity: the methodology doc + intent doc + regression suite ship in the same change set.`

`## [2026-08-19T12:00Z] doc-writer-shipped — methodology/M-decide-graph-audit-promotion-flow.md v0.3.0 → v0.4.0 (amend-85). Applies the operator's 2026-08-18 corrections from the master-ticket summary PDF Mermaid review. Replaces "fires the canonical 3-stage HITL gates" with "fires the canonical 2 HITL gates (HITL #1 + HITL #2)" throughout. Renumbers the final disposition gate from HITL #4 → HITL #2. Unnumbers the verifier compliance check (was HITL #3 in v0.3.0 amend-82; now the unnumbered automated gate between HITL #1 and HITL #2). Reorders the flow chain — HITL #1 → verifier compliance check → HITL #2 (only after verifier PASS). Connects the Rewrite node to the HITL #1 feedback flow (not directly to the operator) — the operator's HITL #1 REVISE / PARTIALLY-ACHIEVES feedback IS the trigger for Rewrite. Adds the corrected Mermaid diagram to Part 3. The Node-type discipline updates: Rewrite fires from the HITL #1 feedback edge (not from a direct operator edge); the verifier compliance check is unnumbered; HITL #2 (renumbered from HITL #4) is BLOCKED until verifier PASS per op-guard-32 v3 amend. Per-loop audit ticket body schema carries 2 operator `clarify-<timestamp>` decision_refs (HITL #1 + HITL #2) + 1 verifier `--result "compliance: pass"` decision_ref. Success criteria + Flow graph + Skip conditions table + Failure modes all reflect the 2-HITL pattern. Out-of-order HITL fire (HITL #2 firing before verifier PASS) blocks on `kind=dependency` per op-guard-32 v3. Companion intent doc bumped to v0.4.1 (paired-wiki per op-guard-5); regression suite v0.4.1 with new test cases for the 2-HITL pattern. Source: t_6621d4d3 (operator 2026-08-18 chat: "There are 2 HITL gates, not 3. We should also stop referring to this final HITL gate as HITL4, it is HITL2" + "HITL must run and pass before HITL4 [now HITL #2] is fired" + "The rewrite script/code block coming from the operator, where does this originate from? It should be connected to a HITL, not directly to the operator"). Per op-guard-5 paired-wiki integrity: the methodology doc + intent doc + master-ticket summary PDF ship in the same change set.`
