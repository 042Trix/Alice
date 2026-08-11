---
id: alice-methodology-M-decide-spec-first-flow
created: 2026-08-05T15:55:00Z
updated: 2026-08-11T23:30:00Z
title: "Methodology M — Decide spec-first flow (doc → V → validate doc → V → update environment → V → validate update)"
type: methodology
status: draft
source: alice-framework
tags: [kind:methodology, kind:spec-first, kind:meta, kind:verifier-step, project:alice]
confidence: 0.0
links: ["[[methodology/M-decide-parity-check.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[methodology/04c-decide-master-ticket.md]]", "[[templates/spec-first-flow.md.template]]"]
promoted_from: _inbox/M-decide-spec-first-flow.md
teaching-example: true
---

# Methodology M — Decide spec-first flow

> The doc is the source of truth; the environment is a follower. The doc-writer writes the spec; a non-doc-writer agent (operator, coder, planner, business-analyst) updates the environment to match the doc. Every transition between phases is **verifier-gated** — the verifier step (V) is an explicit, mandatory node in the graph node order, not an optional review.

## Part 1: Why a 4-phase flow with verifier steps

Alice methodology docs describe real working patterns. Without a spec-first flow, two failure modes recur:

- **Doc-writer drift**: the doc-writer writes the doc, then quietly makes the environment match (adds crons, skills, boards). The doc is no longer spec; it is record-of-implementation. The friend reading Alice later can't tell what was decided first.
- **Environment drift**: a non-doc-writer agent updates the environment without a spec. The doc-writer later writes a doc that follows the implementation. The doc becomes a snapshot of whatever happened, not the canonical reference.

The 4-phase flow fixes both: **doc → V → validate doc → V → update environment → V → validate update**. The doc is written first and validated first. The environment follows the doc, not the other way around. Every `→` arrow is a verifier-gated transition: the next phase cannot begin until the previous phase's output passes the verifier's acceptance review.

### The canonical graph node order

The canonical graph node order for every spec-first ticket is:

```
(doc) ─V─▶ (validate doc) ─V─▶ (update environment) ─V─▶ (validate update)
```

Read left-to-right: each `V` is a verifier node that owns the transition review. The 4 phases are the work-product nodes; the 3 verifier steps are the gating nodes between them. A graph without these 3 verifier nodes is a graph whose transitions are un-audited — and un-audited transitions are the bug class that motivated the retro recommendation `Add a verifier step to the graph node order` (filed from the `t_9f1df904` retro, 2026-08-05).

**Why the verifier step is mandatory, not optional.** Without an explicit V node, the transition is either silent (no review) or implicit (the worker reviews its own output, which is the same as no review). The verifier is an **independent reviewer**: the agent that wrote the doc does not validate the doc; the agent that updated the environment does not validate the update. Independence is what makes the verifier step useful.

**When can the verifier step be elided?** Almost never. The only legitimate elision is when the phase's output is a single-line audit-trail entry with no reviewable surface (e.g., a `kanban_comment` that names the next step). For any output that will become the canonical reference (a methodology doc, an environment change, a release artifact), the verifier step is mandatory.

## Part 2: The 4 phases

**Phase 1 — doc (write the spec).** The doc-writer writes the methodology doc that describes the pattern. The doc includes: what the pattern is, why it exists, how it works, the parity check (see M-decide-parity-check.md), and an explicit "aspirational" flag if no operator-environment evidence exists. **No environment changes in this phase.**

**Phase 2 — validate doc.** The verifier (or operator, on operator-gated items) reviews the doc for: internal consistency, compliance with Alice's documentation convention, the parity-check verdict, and the aspirational-flag honesty. **No environment changes in this phase.** The verifier's output is an acceptance comment on the doc; on pass, phase 3 begins.

**Phase 3 — update environment.** A non-doc-writer agent (operator, coder, planner, business-analyst) updates the operator's environment to match the doc. This is a **separate ticket**, filed by the non-doc-writer agent, not by the doc-writer. The ticket body cites the doc as the canonical reference. The doc-writer is **not** on this ticket's assignee list. Examples: adding a cron, adding a skill, adding a board, modifying AGENTS.md / vault structure.

**Phase 4 — validate update.** The verifier (or operator) reviews the implementation ticket's work: does the environment change match the doc? Is the doc's parity-check section now accurate (does the operator's environment now support the pattern)? Are there side effects? On pass, the doc's parity-check section moves from "aspirational" to "confirmed." On fail, the implementation is rolled back; the doc is preserved (the spec is still correct; the implementation is wrong).

### The verifier step (V) — explicit node between every phase

The V step is its own node, with the same 6 required metadata fields as any other node (per `methodology/04a-decide-work-graph.md` Part 5):

| # | Field | Verifier-step default |
|---|---|---|
| 1 | `goal` | Confirm the prior phase's output satisfies its acceptance criteria. |
| 2 | `original_ask` / `context` | The parent ticket's body; the doc being validated; the env change being validated. |
| 3 | `task_specific_info` | The specific artifact path / commit / board / cron id to inspect. |
| 4 | `process` / `flow` | Read the artifact → cross-check against the prior phase's acceptance criteria → write an acceptance comment. |
| 5 | `skill_version` | `verifier` profile version pinned at ticket creation. |
| 6 | `acceptance_criteria` | Yes/no: "does the artifact match the prior phase's spec?" plus 3-7 evidence checks. |

The V step is an **Agent node** (per `04a-decide-work-graph.md` Part 4 Type 3) with `assignee=verifier`. It is not a Human node (operator LGTM is a separate gate, applied only when the operator wants it). It is not a Scripts/Code node (the review requires judgment, not deterministic comparison).

The V step is **bounded**: it cannot re-run the prior phase, expand scope, or rewrite the artifact. If the verifier's review surfaces a real defect, the verifier writes a `kanban_comment` on the parent ticket with the defect + evidence and transitions the V node to `done` with a "review-failed" comment. The parent ticket then re-enters the prior phase for re-work; the prior-phase assignee (e.g., doc-writer) re-files when ready.

## Part 3: Why the doc-writer is spec-only

The doc-writer's tools (file, kanban, vision, image-gen, TTS) do not include terminal / cron-create-by-default / direct environment writes. This is by design: the doc-writer's role is to write what the pattern should be, not to make it so. The operator (or another agent) owns environment changes; the verifier owns validation. This separation prevents the "doc-writer drift" failure mode (Part 1) and aligns with the parity-check rule's verdict structure (M-decide-parity-check.md Part 3 — "aspirational patterns get a follow-up infrastructure ticket").

## Part 4: How to apply the rule

When filing a new ticket on `alice-framework`:

1. **Phase 1**: draft the doc; include the parity-check section (per `templates/parity-check.md.template`); do not modify the environment.
2. **Phase 1 → V**: spawn the V-1 verifier step (assignee=verifier, parent=phase-1 ticket). The verifier reviews the doc against phase-1's acceptance criteria (internal consistency, Alice convention compliance, parity-check evidence, aspirational-flag honesty).
3. **Phase 2**: on V-1 pass, file a verifier ticket (or get operator LGTM on operator-gated items) on the doc; wait for review. **V-1 must transition to `done` before phase 2's body work begins.**
4. **Phase 2 → V**: spawn the V-2 verifier step (assignee=verifier, parent=phase-2 ticket). V-2 confirms the doc is fit for phase-3 environment updates.
5. **Phase 3** (if the doc introduces new environment infrastructure): file a separate ticket on the appropriate (non-alice-framework) board for the environment change; cite the doc as the canonical reference; do NOT assign the doc-writer.
6. **Phase 3 → V**: spawn the V-3 verifier step (assignee=verifier, parent=phase-3 ticket). V-3 confirms the environment change matches the doc's spec.
7. **Phase 4**: verifier (or operator) reviews the environment change; updates the doc's parity-check section from "aspirational" to "confirmed" on pass.

**Important:** a single ticket can span multiple phases, but only if the doc-writer stays out of phase 3. The doc-writer files phases 1+2+4 (doc, validate doc, validate update); a non-doc-writer agent files phase 3 (update environment). The V-1, V-2, V-3 verifier nodes are children of their respective phase tickets — the verifier role is the only one that owns the V nodes.

**The 7-step expansion of the 4-phase flow** (1 phase-1, 2 V-1, 3 phase-2, 4 V-2, 5 phase-3, 6 V-3, 7 phase-4) is the canonical application order. Skipping a V step is the same bug class as skipping a phase: an un-audited transition that the operator cannot reconstruct.

## Part 5: Anti-patterns

- **"Doc-writer modifies the environment to make the doc parity-check pass."** The doc-writer writes the doc, runs the parity check, finds no evidence, and adds the evidence themselves (registers a cron, creates a skill). Fix: file a phase-3 ticket for the non-doc-writer agent; do not write the environment yourself.
- **"Doc silently follows a drifted implementation."** A non-doc-writer agent changes the environment without a spec; the doc-writer later writes a doc that describes whatever happened. Fix: require phase 1 first; the doc is the reference.
- **"Phases 3 and 4 are skipped because 'it's obvious.'"** The doc is approved; the environment is updated by whoever has time; nobody validates. Fix: every phase is a step; skipping a step is the failure mode.
- **"The doc-writer is added as a watcher on phase-3."** The doc-writer is not a watcher, not an implementer, not a reviewer of phase-3. The doc-writer's job is done at the end of phase 2.
- **"The verifier step is skipped because the worker already reviewed their own output."** Self-review is not review. The V node must be an independent reviewer with `assignee=verifier`, not the worker who produced the prior phase's output. Fix: file the V node as a child of the prior-phase ticket; assignee must be `verifier`, not the prior phase's assignee.
- **"A V node re-runs the prior phase."** The V node is bounded: it reads the artifact, compares to acceptance criteria, and emits an acceptance comment. It does not rewrite the doc, does not re-run phase-3, does not change scope. Fix: if the V node expands scope, the V node is filed as a defect comment on the parent ticket and the prior phase re-enters for re-work.

## Part 6: Cross-references

- `methodology/M-decide-parity-check.md` — the parity-check rule that phase 1 includes; the verdicts (parity-checked / partial / aspirational) flow into phase 4.
- `methodology/04a-decide-work-graph.md` — the V node is an Agent node (Type 3) with the 6 required metadata fields per Part 5.
- `methodology/04c-decide-master-ticket.md` Part 6 — the done-gate pattern (auto-done vs operator-LGTM-done) is the master-level analog of the V node; the V node is the per-transition analog.
- `templates/spec-first-flow.md.template` — the fillable ticket template with phase markers (now extended to include V markers).
- `templates/parity-check.md.template` — the parity-check section embedded in phase-1 tickets.
- Source: operator's 2026-08-05 direction; retro recommendation `Add a verifier step to the graph node order` from `t_9f1df904` fixture (2026-08-05).
- Supersedes: `t_75e1bfa7` (the original parity-check rule's "2-ticket handoff" anti-pattern).

## Parity check

**Pattern this ticket describes:** Every spec-first graph execution has an explicit verifier step (V) between every pair of phases; the V step is a child ticket with `assignee=verifier` and the 6 required metadata fields.

**Evidence in operator's environment:**
- `~/.hermes/profiles/verifier/config.yaml` exists; `hermes profile list` shows `verifier` profile (grok-4.5).
- `methodology/04a-decide-work-graph.md` Part 4 defines the 3-node-type taxonomy (Human / Scripts/Code / Agents) with the verifier as an Agent subtype.
- `methodology/04c-decide-master-ticket.md` Part 6 already names "verifier completion is included among the required children" as the default done-gate.
- `methodology/06a-decide-retro.md` Part 6 worked example shows 1 verifier agent node as a child of the master ticket; the retro recommendation `Add a verifier step to the graph node order` (filed 2026-08-05 from `t_9f1df904`) confirms this pattern is now being codified into the canonical graph node order.
- Operator's loop YAML (`~/.hermes/loops/hermes.yaml`) shows multiple loop entries already using the verifier step (`verifier:` block per loop entry).

**Verdict:** parity-checked (with V steps already used at the loop-entry level; this ticket formalizes them at the per-transition level).

**Follow-up tickets:** none for the doc change itself. The verifier step is now the canonical pattern for every spec-first ticket; new tickets apply the 7-step order automatically.
