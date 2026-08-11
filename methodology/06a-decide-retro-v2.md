---
id: alice-methodology-06a-decide-retro-v2
created: 2026-08-06T07:40:00Z
updated: 2026-08-11T23:30:00Z
version: 0.1.5
title: "Methodology 06a (v2, Amendment 1+2+3+4+5) — Decide the retro (flow-level retro split into agent half + human half; Retro-H parent edge is [retro_a] only; Retro-A spawns post-council, parents=fan-in)"
type: methodology
status: draft
supersedes: ["[[methodology/06a-decide-retro.md]]", "[[methodology/06a-decide-retro-v2.md#initial-v2-draft]]", "[[methodology/06a-decide-retro-v2.md#amendment-1--2]]", "[[methodology/06a-decide-retro-v2.md#amendment-3--2026-08-07----the-retro-is-2-parts-agent-retro-a--human-retro-h]]"]
amended_by: ["[[ticket:t_f0cd340b]]", "[[ticket:t_323ad698]]", "[[ticket:t_33843787]]", "[[ticket:t_42e0c91c]]"]
amendment_initial_v2_draft_by: "[[ticket:t_3569c32c]]"
amendment_3_by: "[[ticket:t_323ad698]]"
amendment_4_by: "[[ticket:t_33843787]]"
amendment_5_by: "[[ticket:t_42e0c91c]]"
source: alice-framework
tags: [kind:methodology, kind:retro, kind:iteration-loop, kind:feedback, kind:self-improvement, kind:flow-level, kind:two-part, kind:retro-a, kind:retro-h, kind:parent-edge, kind:circular-gate-fix, kind:retro-spawn-position, kind:post-council, kind:done-gate, project:alice, amendment:1, amendment:2, amendment:3, amendment:4, amendment:5]
confidence: 1.0
links: ["[[methodology/06-iteration-loop.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[methodology/04b-decide-board-routing.md]]", "[[methodology/04c-decide-master-ticket.md]]", "[[methodology/04d-decide-flow-spec.md]]", "[[methodology/05-op-guards.md]]", "[[methodology/M-decide-spec-first-flow.md]]", "[[methodology/M-decide-human-digest.md]]", "[[methodology/06a-decide-retro.md]]", "[[worked-examples/01-solo-founder-skeleton/AGENTS.md]]"]
teaching-example: true
---
<!-- Note (2026-08-11, t_36795558): M-decide-x-article-review-flow.md moved to instance side at ~/.hermes/methodology/M-decide-x-article-review-flow.md per op-guard-17 methodology-canonical/instance-conforms; not a sibling of this framework doc anymore. -->

# Methodology 06a (v2, Amendment 1+2+3+4+5) — Decide the retro (flow-level retro split into agent half + human half; retro is two kanban tasks; Retro-H parent edge is [retro_a] only; Retro-A spawns post-council with parents=fan-in)

> v1 documented a 1-retro-per-node shape with 3 freeform fields ("what worked / what didn't / what to change"). v2 changes the shape, the writer, the scope, and the format. The retro is now **flow-level**, **written by a different agent than the executor**, **scoped to master-ticket flows**, **structured as observed-issue + proposed-corrective-action findings**, **filed as a kanban task on a substrate board (NOT a file in `0-INBOX/`)**, and **split into two tasks**: **Retro-A** (the agent half — verifier runs the 6-axis evaluation and produces findings) and **Retro-H** (the human half — operator reviews Retro-A's findings, adds observations, and dispositions each finding as ACCEPT / REJECT / DEFER within a fixed 24h window). ACCEPTED findings spawn child tickets. This document supersedes v1. Read this one; archive v1 to the historical notes section.

This is the sibling doc to `methodology/06-iteration-loop.md`. The 06 doc describes the iteration loop primitive (detect → surface → act) and the engine that drives crons and skills. This doc describes the **retro** — the post-execution step that sits *after* the act leg and *before* the next detect. The retro converts one execution's evidence into the next execution's improved graph, flow, skill, or methodology.

The retro is the system's mechanism for self-improvement. Without it, the same mistakes ship every run.

---

## Part 1: What changed from v1 to v2

v1 of this document captured an earlier operator direction. v2 reflects the operator's correction on 2026-08-05: the retro shape needs to be richer and the retro writer needs to be a separate role. Four changes, summarized:

| Aspect | v1 | v2 |
|---|---|---|
| **Scope** | One retro per graph node | One retro per **flow** (the master ticket's complete run) |
| **Writer** | The agent that ran the graph | A **different agent** — verifier, council, or coach |
| **Trigger eligibility** | Every graph execution | **Master-ticket flows only** (no retros for crons, ad-hoc work, or standalone tickets) |
| **Artifact format** | Freeform 3-field document, written into the master's ticket comment | **A kanban task on the appropriate substrate board** with structured findings in the body |
| **Findings shape** | 3 freeform fields (worked / didn't / change) | **Structured findings**: observed issue + proposed corrective action, evaluated on 6 axes |
| **Number of tasks** | One retro file (or one comment block) | **Two kanban tasks**: Retro-A (agent half, `assignee=verifier`) + Retro-H (human half, `assignee=operator`). Both must reach `done` before the master auto-closes (Amendment 3, 2026-08-07). |

If you have read v1, the rest of this document is new. If you have not, skip v1 entirely.

### Amendment 1 (2026-08-06) — Rule 4 retro-as-kanban-task supersedes the prior v2 file-based publish

The initial v2 draft (shipped by `t_3569c32c` on 2026-08-06) treated the retro as a file (vault note + ticket comment + master-ticket comment). The operator's clarification on 2026-08-06 (recorded in `t_f0cd340b`) corrected this: **the retro itself is a kanban task** filed on the appropriate substrate board, with the 6-axis assessment + structured findings in the task body. The prior file-based publish pattern is **superseded** by this amendment. The retro is no longer written as a `0-INBOX/retro-*.md` file; the retro's durable record IS the kanban task row + its body + its comment thread.

This adds a new canonical Rule 4 ("the retro is a kanban task") and renumbers the prior Rule 4 (structured findings shape) to Rule 5. It also resolves Open Question Q4 (where findings get filed — answer: in the retro task body, indexed by `F-N`).

### Why these four changes

The operator's reasoning (2026-08-05):

- **Flow-level scope.** Per-node retros are too granular. The same friction often shows up at three nodes; a per-node retro writes it up three times. A flow-level retro sees the friction once and proposes the corrective action that prevents it next time.
- **Different writer.** A retro the agent writes on itself is a self-evaluation. Self-evaluations tend to confirm what the writer already believed. A separate writer sees the run as evidence, not as a story the writer is invested in.
- **Master-only eligibility.** Crons, ad-hoc work, and standalone tickets generate operational metrics, but not *flow-level* learning. A flow that has no master has no defined scope to learn about. (Open question Q2 below covers planner/council flows without a master.)
- **Structured findings.** Freeform fields produced noise. Operators read retro after retro of "everything went great" without learning anything. Structured findings force the writer to cite evidence (the observed issue) and commit to a destination (the proposed corrective action).

---

## Part 2: The rules of v2 (4 original + 2 from Amendment 3)

These rules replace the v1 "three fields" pattern. They are the canonical retro contract; any retro that violates one of them is malformed.

**Rule count history.** Part 2 originally shipped with 4 rules (Rules 1–4). Amendment 1 renumbered the prior Rule 4 to Rule 5 and added Rule 4 (retro is a kanban task). Amendment 3 added Rule 5 (the retro is 2 parts) and renumbered the prior Rule 5 to Rule 6. Amendment 4 (2026-08-11) is a clarification, not a new rule: it changes the parent-edge shape (`parents=[retro_a]` only, was `[master, retro_a]`) and adds explicit parent-edge vs required-children-set terminology. Amendment 5 (2026-08-11, t_42e0c91c) is also a clarification, not a new rule: it changes Retro-A's parent-edge shape from `[master]` (Phase 0 retro spawn) to a post-council fan-in list `[master, P4, V-4, V-of-V, corrective-children-done, ship-done-when-applicable]` — Retro-A's spawn position moves from Phase 0 to the END of the flow (after council output + corrective actions + ship lane). This is the durable fix for the verified wast3 article incident (`t_092e629c`) where the v0.6.0 Phase 0 retro spawn caused the master to auto-close before Retro-H could disposition (the master auto-closed when Retro-A reached `done`, stranding Retro-H at `blocked/kind=needs_input`). Retro-H's parent edge remains `parents=[retro_a]` only (Amendment 4; unchanged). The canonical Part 2 still has **6 rules**.

### Rule 1 — A different agent runs the retro

The agent that ran the flow does **not** run the retro on itself. **As of Amendment 3 (2026-08-07), the retro is split into two tasks — Retro-A and Retro-H — each with its own "different agent" check.** Three options for Retro-A's writer, with trade-offs:

| Option | Profile | Pro | Con |
|---|---|---|---|
| **Verifier** | The agent that verified the flow's output (per `methodology/04a-decide-work-graph.md`) | Already has the context, lowest ceremony, no new profile to maintain | May be biased toward "the work is fine" since they signed off on it |
| **Council** | The multi-perspective review (per `methodology/07-council-methodology.md`, forthcoming) | Independent perspective, surfaces blind spots a single writer misses | More ceremony; council is heavier than most flows need |
| **Coach** | A new dedicated agent focused on flow-level learning | Focused scope, no conflict of interest | Yet another agent profile to build, maintain, and keep consistent with the rest of the fleet |

**Recommendation.** Start with the **verifier** as the default Retro-A writer. The verifier is already in the flow's lifecycle; routing the retro through them avoids the ceremony of a council and the overhead of a new profile. Graduate to a coach profile **only when** the volume of retros justifies it — a rough threshold is one full-time coach worth of work per week, which the operator's instance has not yet reached.

**Council is reserved for retros that find something the verifier cannot judge alone** — typically cross-flow patterns, methodology-level findings, or operator-facing decisions that need multiple perspectives. Most retro findings do not need a council; the verifier is enough.

#### What "different agent" means for each half

The "different agent" rule applies independently to Retro-A and Retro-H:

- **Retro-A** (the agent half). The Retro-A writer must not be the agent that drafted the master ticket body, wrote any of the flow's children, or was itself the executor. The default Retro-A writer is the **verifier**, satisfying this rule because the verifier did not execute any child (the verifier only signed off). If the Retro-A writer is the executor, the retro is malformed and must be re-assigned.
- **Retro-H** (the human half). The Retro-H writer is, by definition, a different agent from the executor: **the operator is human, not a runner of children**. Retro-H's "different agent" rule is structurally satisfied by the fact that the operator is not the agent that executed the flow. No additional independence check is required for Retro-H.

Both halves are distinct tickets with distinct gates. See Rule 5 for the canonical 2-part shape.

#### What "different agent" means in practice (Retro-A only, since Retro-H is structural)

The Retro-A writer must not be the agent that:

- Drafted the master ticket's body.
- Wrote any of the flow's children.
- Signed off on the verifier step (when the writer is *not* the verifier; if the verifier is the writer, this rule is satisfied because the verifier signed off independently of the executor).

The Retro-A writer may be the verifier *if* the verifier did not also execute a child. In practice this is the common case: the verifier runs at the end, after all children, and the verifier has not been the executor.

#### What the executor should NOT do

The executor must not:
- Edit the retro task body after the writer publishes it (amendment is operator-only within the window).
- Pre-emptively propose retro findings (this pollutes the writer's evidence base).
- Skip the retro by filing the master ticket `done` faster than the writer can publish.
- Write a retro file in `0-INBOX/` (Rule 4 forbids file-based publish; the retro is a kanban task).

These four anti-patterns preserve the writer's independence and the rule that the retro is a task, not a file.

### Rule 2 — One retro per flow, evaluated on 6 axes

A flow has many steps. The retro looks at the flow as a whole, not per node. **Six evaluation axes** structure the writer's assessment:

| Axis | Question the writer answers |
|---|---|
| **a. Right information at the beginning** | Did the agent have what it needed at flow start? Was the spec, the brief, the cross-references, the prior-art sufficient, redundant, or missing? |
| **b. Right result the first time** | Was the output correct on first attempt, or did it require rework (re-dispatch, verifier failure, operator correction)? |
| **c. Skills adequate** | Were the skills assigned to this flow sufficient? Did the agent have to improvise? Were there specific skills for the work, or did the agent have to derive them? |
| **d. Flow defined optimally** | Was the flow's structure (steps, branches, gates) well-designed? Could it have been simpler or done in parallel? |
| **e. Followed the flow as specd** | Did the agent execute each step as defined, or deviate? If deviated, why, and was the deviation an improvement or a bug? |
| **f. Right agents used** | Were the assignee profiles correct? Should a different agent (e.g., a specialist) have done some steps? |

Each axis gets a **1-line assessment** (pass / partial / fail) plus the observed evidence. The format is uniform across all six axes; variation breaks the operator's mental model.

#### How the axes relate to v1's 3 fields

v1 had three freeform fields. v2's six axes cover the same conceptual ground with more structure:

| v1 field | v2 axes that cover it |
|---|---|
| "What worked" | axes b, e, f (right result, followed flow, right agents) |
| "What didn't" | all six axes (anything that wasn't right is an issue) |
| "What to change" | structured findings, derived from axes a, c, d primarily |

The "what worked" field is gone as a top-level section because it is implied by the axis assessments: axes that pass are "what worked." The writer still notes positive observations, but they live inside the axis assessment as evidence, not as a freeform paragraph.

#### Why exactly six axes

Six is the smallest number that covers every angle the operator cares about without overlap:

- **Information** (a) and **result** (b) are inputs and outputs of the flow.
- **Skills** (c) and **agents** (f) are the resources the flow consumed.
- **Flow design** (d) and **conformance** (e) are the structural and behavioral properties of the run itself.

Two axes (information, result) measure outcome. Two (skills, agents) measure resource fit. Two (design, conformance) measure process. The pairing is deliberate; readers who care about outcome read top-down, readers who care about process read bottom-up.

Adding a seventh axis is allowed but requires justification in the retro itself: why is the seventh axis not covered by the six? Removing an axis is not allowed; if the operator wants fewer, they edit this doc.

### Rule 3 — Only on master-ticket flows

A retro is a **flow-level event** and it is **tied to a master ticket**. Crons, ad-hoc work, and standalone tickets do not generate retros.

#### What counts as a master-ticket flow

A flow is master-ticket-eligible for retro if and only if:

1. The flow has a master ticket (per `methodology/04c-decide-master-ticket.md`).
2. The master ticket has reached the `done` state (per `methodology/04a-decide-work-graph.md`).
3. The flow has at least one child ticket that produced evidence (a `done` child, a verifier comment, a file artifact).

If all three are true, the retro fires. If any is false, the retro does not fire.

#### What does NOT generate a retro

- **Crons** — even crons that fire and act every hour. A cron's "retro" is the iteration loop's `feedback` element (per `methodology/06-iteration-loop.md`); it is a per-tick audit-line, not a flow-level retro.
- **Ad-hoc operator requests** — "add this to the doc" with no master ticket. The retro would have no master to scope it.
- **Standalone tickets** — single tickets, no children, no flow. Same logic: no master, no retro.
- **Failed / blocked / aborted runs** — the crash/block/abort is itself the lesson; record it in the kanban event log and the post-mortem (per `methodology/05-op-guards.md`). A retro on a non-`done` run is a post-mortem, not a retro; the two are different artifacts.
- **Council verdicts that did not spawn a flow** — see Open Question Q2.

#### Why this rule exists

The retro is a **learning event** and the master is the **scope of the learning**. Without a master, the "flow" is implicit and the retro has no clear subject. A retro without a subject becomes a wish list — the writer invents scope, the operator reads prose without context, and the findings do not land where they should.

Crons are the canonical example. A cron fires every 10 minutes; if each tick were a retro, the operator would read 144 retros a day. The cron's feedback is captured in the iteration loop's `feedback` element (an audit-line + a state adjustment), not in a flow-level retro.

### Rule 4 — The retro is two kanban tasks (Retro-A + Retro-H), not files

The retro is **two tasks on the kanban board**, filed when the master ticket transitions to `done` (or, for operator-facing flows, spawned at Phase 0 per `methodology/04c-decide-master-ticket.md` Part 7.5). The two tasks share a master, share the structured findings, and have distinct writers and dispositions. **No separate `0-INBOX/retro-*.md` file is written.** The retro file pattern (per v1 and the prior v2 draft) is superseded by this rule.

**As of Amendment 3 (2026-08-07), the retro is TWO kanban tasks, not one:**

- **Retro-A** — the agent half. `assignee=verifier` (or council/coach per Rule 1). The agent writes the 6-axis assessment + structured findings. This is the technical retro record.
- **Retro-H** — the human half. `assignee=operator`. The operator reviews Retro-A's findings, adds observations, and dispositions each finding as ACCEPT / REJECT / DEFER.

**Parent edges (as of Amendment 4 + Amendment 5, 2026-08-11):**

- **Retro-A's parent edge** (Amendment 5, t_42e0c91c) — Retro-A is filed with `parents=[master, P4, V-4, V-of-V, corrective-children-done, ship-done-when-applicable]`. The list references every prior phase + corrective-action child (when applicable) + ship-done evidence (when applicable); the list grows as the flow progresses. The Phase 0 retro spawn (Retro-A with `parents=[master]` only) is the v0.6.0 shape that v0.7.0 of `~/.hermes/methodology/M-decide-x-article-review-flow.md` (instance-side per op-guard-17) supersedes — Phase 0 spawn is a v0.7.0 violation. When no Alice amendment is in scope, the list reduces to `[master, P4, V-4, V-of-V]` (no corrective-action children, no ship lane). When corrective-action children exist but no ship lane ran (Alice amendment was external-only), the list is `[master, P4, V-4, V-of-V, corrective-children-done]`. When a ship lane ran (Alice amendment in scope), the full list is `[master, P4, V-4, V-of-V, corrective-children-done, ship-done]`. The dispatcher MUST refuse the parent-edge promotion for Retro-A if any prior phase gate is missing.

- **Retro-H's parent edge** (Amendment 4, unchanged) — Retro-H is filed with `parents=[retro_a]` ONLY (NOT `[master, retro_a]`). Retro-H blocks on Retro-A being `done` via the retro_a parent edge; adding the master to Retro-H's parent list would create a circular gate (master blocks on Retro-H done, Retro-H blocks on master done) — see Amendment 4 §"Why this matters" for the verified incident.

**Retro-A spawn position (Amendment 5):** Retro-A fires AFTER every upstream gate condition is satisfied — after Phase 4 council verdict + V-4 PASS + V-of-V PASS + every corrective-action child has reached `done` + every conditional ship lane has shipped (when applicable). Retro-A does NOT fire at Phase 0 (master creation). The orchestrator files Retro-A with `initial_status=blocked/needs_input` (the parent-edge gate is the gate; the block_kind is the structural hint); the dispatcher promotes Retro-A to `ready` once every parent in the list reaches `done`.

**Master's required-children set (unchanged):** The master has TWO retro children in its required-child set under the operator-LGTM-done gate (per `methodology/04c-decide-master-ticket.md` Part 7.5): both Retro-A and Retro-H must reach `done` for the master to auto-close. The required-children set is a separate mechanism from the parent edges; it is the done-gate list (what the dispatcher checks at master close), not a parent edge. Retro-H is in the master's required-children set but NOT in Retro-H's parent list. Retro-A's parent list references the post-council fan-in (the amendment 5 shape), but the master itself is still in Retro-A's parent list — that's the structural anchor that ensures Retro-A cannot promote to `ready` until the master is also at the right state (per `methodology/04a-decide-work-graph.md` Part 5 parent-edge semantics).

See Rule 5 for the full 2-part shape. See Part 3 for the dual-path lifecycle.

#### Why two tasks, not one

The retro has two distinct jobs that v1 and the prior v2 draft collapsed into one:

1. **The agent's evaluation** (Retro-A): the 6-axis assessment, the structured findings, the technical audit trail. This is what makes the retro a *learning event* — the agent sees what the operator cannot see (the executor's behavior, the verifier's deviations, the skill gaps).
2. **The human's review** (Retro-H): the operator's observations, the operator's amendments to findings, the per-finding ACCEPT/REJECT/DEFER disposition. This is what makes the retro *operator-facing* — the operator sees what the agent cannot (the operator's perspective on the flow, the cross-flow pattern, the methodology-level decision).

Collapsing these into one ticket meant either (a) the agent wrote the operator's voice and the operator's observations were lost, or (b) the operator amended the agent's body and the audit trail was muddied. The 2-part shape keeps both voices distinct and the audit trail clean. See Amendment 3 §"Why these changes" for the full rationale.

#### Why tasks, not files

Each retro task has three durable obligations: (1) be searchable by the operator, (2) be amendable within the window, (3) produce follow-up tickets that the next iteration consumes. A file in `0-INBOX/` satisfies (1) only weakly (no first-class search index) and forces ad-hoc mechanisms for (2) and (3). A kanban task satisfies all three:

- (1) Search — the task lives on a substrate board; it has `assignee`, `block_kind`, `status`, `priority`, `parents`, `children`, `comments`, `events` — all queryable.
- (2) Amend — the operator amends the body via edits; the kanban DB records the edit events. No `git diff` ceremony.
- (3) Follow-ups — accepted findings become child tickets via `kanban_create(parents=[retro_h_task_id])`. The parent edge is recorded in the kanban DB, not inferred from prose.

#### What goes in the Retro-A task body

The Retro-A task body is a structured template. The Retro-A writer fills the fields; Retro-H reads them. The template:

```markdown
# Retro-A: <flow title> (master t_<id>)

## Context
- Master ticket: t_<id>
- Flow run window: <ISO start> — <ISO end>
- Writer profile: <verifier | council | coach>
- Writer member: <agent name>
- Writer independence: <"writer did not execute any child" | "writer executed child t_X — INDEPENDENCE VIOLATION, see Rule 1">

## 6-axis assessment
| Axis | Assessment | Evidence |
|---|---|---|
| a. Right information at the beginning | pass / partial / fail | <1-line evidence> |
| b. Right result the first time | pass / partial / fail | <1-line evidence> |
| c. Skills adequate | pass / partial / fail | <1-line evidence> |
| d. Flow defined optimally | pass / partial / fail | <1-line evidence> |
| e. Followed the flow as spec'd | pass / partial / fail | <1-line evidence> |
| f. Right agents used | pass / partial / fail | <1-line evidence> |

## Findings

### Finding F-1
- **Axis:** <a–f>
- **Observed issue:** <path / ticket id / log line / count / delta — must be verifiable>
- **Proposed corrective action:** <destination: which doc / skill / flow / profile> — <the change> — <verifier check>
- **Disposition:** PROPOSED | ACCEPTED | REJECTED | DEFERRED | DISPOSITION SET <ISO>
- **Notes:** <operator's amendment if any; required when disposition != PROPOSED>

### Finding F-2
<same shape>

## Retro-A → Retro-H handoff
- Retro-A status: drafting | open (waiting for Retro-H to open)
- Retro-H task id: t_<id> (linked)
- Operator's review window opens when Retro-A reaches `done`.
```

#### What goes in the Retro-H task body

The Retro-H task body is a structured template. Retro-A's findings are mirrored into Retro-H's body for the operator's review (so the operator does not have to leave Retro-H to read Retro-A). The operator's observations and dispositions live in Retro-H's body. The Retro-H template:

```markdown
# Retro-H: <flow title> (master t_<id>) — operator review

## Context
- Master ticket: t_<id>
- Retro-A task id: t_<id> (linked)
- Retro-A writer: <profile> / <member>
- Retro-A finished: <ISO when Retro-A reached `done`>
- Operator review window: <ISO when Retro-H opened> — <ISO + 24h>

## Findings (mirrored from Retro-A)

### Finding F-1
- **Axis:** <a–f>
- **Observed issue:** <mirrored from Retro-A>
- **Proposed corrective action:** <mirrored from Retro-A>
- **Disposition:** PROPOSED | ACCEPTED | REJECTED | DEFERRED | DISPOSITION SET <ISO>
- **Operator's observation:** <free-form; required when disposition != PROPOSED>
- **Disposition set:** <ISO>

### Finding F-2
<same shape>

## Operator's overall observations
- <free-form observations that span multiple findings or apply to the flow as a whole>

## Operator review window
- Opens: <ISO when Retro-H transitions to `open`>
- Closes: <ISO + 24h, or the operator-amended timestamp>
- Default window length: 24 hours (1 day)
- Operator actions during window: ACCEPT / REJECT / DEFER per finding
```

The operator edits Retro-H's body directly to disposition each finding. Retro-A's body remains the technical audit record; Retro-H's body is the operator-facing record. Both bodies carry the same finding ids (`F-1`, `F-2`, …) so cross-referencing is direct.

#### The accept / reject / defer flow (Retro-H dispositions)

The operator dispositions each finding **in the Retro-H body** during the Retro-H window. Three valid dispositions:

| Disposition | Operator signal | Retro-H's response | Follow-up action |
|---|---|---|---|
| **ACCEPT** | Operator edits the finding's `Disposition:` to `ACCEPTED` and adds a `Operator's observation:` line | The finding is marked accepted in the Retro-H body | A child ticket is filed via `kanban_create(parents=[retro_h_task_id], assignee=<right-profile>, body=<one-paragraph spec derived from the finding's proposed corrective action>)`. The child ticket's body references the finding id (`Finding F-N`). |
| **REJECT** | Operator edits the finding's `Disposition:` to `REJECTED` and adds a `Operator's observation:` line stating the reason | The finding is marked rejected in the Retro-H body | No child ticket is filed. The audit-line records `finding-rejected: F-N by operator at <ISO> — <reason>`. |
| **DEFER** | Operator edits the finding's `Disposition:` to `DEFERRED — <future date or condition>` | The finding is marked deferred in the Retro-H body | No child ticket is filed now. A `deferred_findings.md` index entry is created at the substrate board's inbox, with `finding_ref: F-N`, `deferred_to: <date/condition>`, `retro_h_ref: t_<id>`. |

The operator's disposition for a finding is **per-finding, not all-or-nothing**. A retro with 5 findings may have 3 accepted, 1 rejected, 1 deferred. The Retro-H ticket closes only after every finding has a disposition.

#### Closing the retros

The two tickets have distinct closure paths. **Retro-A closes first** (transitioning from `open` to `done` after the Retro-A writer publishes the technical record); **Retro-H closes second** (transitioning from `open` to `applied` to `done` after the operator dispositions all findings).

Retro-A closure conditions:

1. Every axis has a `pass / partial / fail` assessment with evidence.
2. Every proposed finding has a `Disposition:` field initialized to `PROPOSED`.
3. Retro-A's audit-line is appended: `retro-a-published: <master-id> at <ISO> — <N findings proposed>; child tickets (Retro-H): <id-list>`.

Retro-H closure conditions:

1. Every proposed finding has a disposition (ACCEPTED / REJECTED / DEFERRED).
2. For each ACCEPTED finding, a child ticket exists in the kanban DB (`kanban_create` returned an id; `kanban_show` confirms the child row).
3. Retro-H's audit-line is appended: `retro-h-applied: <master-id> at <ISO> — <N accepted, N rejected, N deferred>; child tickets: <id-list>`.

**The master auto-closes only after BOTH Retro-A and Retro-H reach `done`.** This is the done-gate the operator-LGTM-done mechanics in `methodology/04c-decide-master-ticket.md` Part 7.5 require.

The operator (or a verifier-gate, per `methodology/05-op-guards.md` op-guard-13) verifies the closure conditions before each retro task transitions to `done`. Each retro task is closed via `kanban_complete` once its closure conditions are met. The master auto-closes only after both retro tickets are `done` — the master's children-set includes both Retro-A and Retro-H, not just one.

#### What the executor MUST NOT do

- Edit either retro task body after publishing. The bodies are mutable only by the operator (within the window) or by an explicit amendment comment.
- Pre-emptively propose findings in the master ticket body or in flow comments. The Retro-A writer's evidence base is the run, not the executor's pre-narrative.
- File the master ticket `done` faster than both retro tickets can publish. The master's done-gate (per `methodology/04c-decide-master-ticket.md`) includes BOTH retro transitions to `done` (for operator-LGTM-done masters) or Retro-A's transition to `open` only (for auto-done masters — see the master's done-gate choice in Part 3 §"Three v2 adjustments" for the per-gate behavior).
- Write a retro file in `0-INBOX/` (Rule 4 forbids file-based publish; the retro is two kanban tasks).

### Rule 5 — The retro is 2 parts: agent (Retro-A) + human (Retro-H)

**As of Amendment 3 (2026-08-07), the retro is TWO parts. This rule codifies the split.** The 2-part shape is the canonical retro pattern; future flows (x-article-review or any other operator-facing flow) MUST spawn both halves.

#### The two parts

| Part | Writer | Assignee | Body content | Closure gate | Status at master close |
|---|---|---|---|---|---|
| **Retro-A** (the agent half) | Different agent from the executor (per Rule 1; default = verifier) | `assignee=verifier` (or council / coach per Rule 1) | 6-axis assessment + structured findings (per Rule 6, originally Rule 5) | Retro-A writer publishes the technical record; Retro-A transitions to `done` | MUST be `done` |
| **Retro-H** (the human half) | The operator (human) | `assignee=operator` | Mirrored findings + operator's observations + per-finding ACCEPT/REJECT/DEFER disposition | Operator dispositions every finding (24h window per Amendment 2); Retro-H transitions to `done` | MUST be `done` |

The master has **two retro children** in its required-child set under `done-gate: operator-LGTM-done` (per `methodology/04c-decide-master-ticket.md` Part 7.5). Both Retro-A and Retro-H must reach `done` for the master to auto-close.

#### Order: Retro-A → Retro-H

The two halves run in sequence, not parallel:

1. **As of Amendment 5 (2026-08-11, t_42e0c91c):** Retro-A opens AFTER every upstream gate condition fires — after Phase 4 council verdict + V-4 PASS + V-of-V PASS + every corrective-action child has reached `done` + every conditional ship lane has shipped (when applicable). Retro-A is filed with `parents=[master, P4, V-4, V-of-V, corrective-children-done, ship-done-when-applicable]`. **Retro-A does NOT fire at Phase 0** (the v0.6.0 shape that v0.7.0 supersedes). Retro-A runs through `drafting → ready → running → open → done` as the verifier publishes the 6-axis assessment + findings. **For backwards compat (Rule 4 still permits, but Amendment 5 supersedes for x-article-review):** if a non-x-article-review master-ticket flow still files Retro-A at Phase 0 per `methodology/04c-decide-master-ticket.md` Part 7.5 (the original 2-part-retro rule), Retro-A opens when the master opens; the Amendment 5 fan-in parent list is the canonical form for new flows going forward, and other flows may adopt Amendment 5 paired-wiki. x-article-review specifically mandates Amendment 5 per `~/.hermes/methodology/M-decide-x-article-review-flow.md` (instance-side per op-guard-17) v0.7.0 Part 5 + Part 7.5.
2. Retro-H opens when Retro-A reaches `done`. Retro-H is filed as a child of Retro-A (with `parents=[retro_a]` per Amendment 4); Retro-H is NOT a child of the master via parent edges (the master is in Retro-H's required-children set via the done-gate list, not via parent edges). Retro-H blocks on Retro-A being `done` via the retro_a parent edge.
3. The operator has 24h from Retro-H's `open` transition to disposition each finding.
4. Retro-H transitions through `drafting → ready → running → open → applied → done` as the operator dispositions each finding and the verifier-gate confirms closure conditions.
5. The master auto-closes after BOTH Retro-A and Retro-H reach `done`.

This order is structural: the agent's evaluation must land before the operator's review can be meaningful (the operator reviews the agent's findings, not a blank form). The 24h clock starts on Retro-H's `open` transition, not on Retro-A's.

#### Where each half lives

- **Retro-A** lives on the appropriate **substrate board** for the master's domain (alice-framework → `alice-framework`, hermes → `hermes`, etc., per Part 6 §"IS tied to a board"). Retro-A's `assignee` is the agent profile (default: verifier).
- **Retro-H** lives on the **same substrate board** as Retro-A. Retro-H's `assignee` is `operator` (a literal-human ticket; the dispatcher surfaces Retro-H to the operator's chat channel via the `operator-action-dm` cron, not via worker dispatch). Retro-H's title prefix is `[HUMAN ACTION]` per the canonical `[HUMAN ACTION]` child-ticket protocol (`methodology/05-op-guards.md` op-guard-3 + `2-ATOMIC/concepts/human-action-child-pattern.md`).

#### What each half does

**Retro-A does (the agent's job):**

- Fills the 6-axis assessment per the Retro-A template (Rule 4 §"What goes in the Retro-A task body").
- Proposes N structured findings, each with observed issue + proposed corrective action + axis + disposition `PROPOSED`.
- Does NOT pre-emptively add operator observations (the operator adds those in Retro-H).
- Publishes Retro-A by transitioning `open → done` with the audit-line `retro-a-published: <master-id> at <ISO> — <N findings proposed>`.

**Retro-H does (the operator's job):**

- Mirrors Retro-A's findings into the Retro-H body (the operator does not have to leave Retro-H to read Retro-A's findings).
- Adds `Operator's observation:` per finding (free-form; required when disposition ≠ PROPOSED).
- Adds `Operator's overall observations:` for findings that span multiple axes or apply to the flow as a whole.
- Dispositions each finding: ACCEPT, REJECT, or DEFER (per Rule 4 §"The accept / reject / defer flow").
- For ACCEPTED findings, the operator (or a coder/planner/dispatcher on Retro-H's behalf) files the follow-up child ticket via `kanban_create(parents=[retro_h_task_id], ...)`.

The two halves are coupled but distinct. The Retro-A writer is forbidden from editing Retro-H's body (Retro-H is operator-only). The operator is allowed to amend Retro-A's body during the Retro-H window (per Rule 4 §"What the executor MUST NOT do" — the executor cannot amend; the operator is not the executor). In practice the operator rarely amends Retro-A's body directly; instead, the operator adds observations in Retro-H that cross-reference Retro-A findings.

#### What the 2-part shape forbids

- **A single retro ticket.** A retro that is filed as one ticket (e.g., `assignee=verifier` with operator dispositions in the comments) is malformed as of Amendment 3. Future flows MUST file Retro-A and Retro-H as separate tickets.
- **Retro-H assigned to an agent.** Retro-H's assignee is `operator` (a literal-human ticket). Retro-H assigned to a verifier / coder / planner / jarvis / doc-writer is a structural violation — the operator's review cannot be delegated to an agent.
- **Retro-H running before Retro-A is `done`.** Retro-H is a child of Retro-A via the retro_a parent edge; Retro-H cannot open until Retro-A is `done`. Filing Retro-H at Phase 0 with `parents=[master]` only (no retro_a edge) is a violation — Retro-H would be ready immediately instead of waiting for Retro-A to publish. The correct parent list is `parents=[retro_a]` ONLY (per Amendment 4, 2026-08-11); the master is in the required-children set, not in the parent list. Filing Retro-H with `parents=[master, retro_a]` was the prior shape (Amendment 3) but creates a circular gate that prevents both retro halves from closing (see Amendment 4 §"Why this matters" for the verified incident).
- **The master closing before both retro halves are `done`.** Under `done-gate: operator-LGTM-done`, the master MUST include both Retro-A and Retro-H in its required-child set. Closing the master with only one retro child is a violation (per `methodology/04c-decide-master-ticket.md` Part 7.5 + the amendment-3 done-gate update).

### Rule 6 — Structured findings: observed issue + proposed corrective action

Every finding has **two parts**, in this order:

1. **Observed issue** — what was the data, the actual behavior, the specific evidence? This is the "what was." Cite evidence: a path, a log line, a ticket id, a count, a delta.
2. **Proposed corrective action** — what should change, **where** (which doc, which skill, which flow), and **how** (the edit, the addition, the removal). This is the "what's next." The destination must be specific — a path, a ticket id, a parent edge.

The format is structured, not freeform. One finding per axis per issue. A flow may produce N findings, where N is the number of issues the writer observed.

#### Example finding (canonical shape)

```markdown
## Finding F-1

**Axis:** a (Right information at the beginning)
**Observed issue:** Flow t_X (master ticket) started with no spec doc. The agent used the body of the master ticket as the only context. The first child task (t_X.1) had to be re-dispatched 2 times because the spec was incomplete (see events on t_X.1: 2 re-spawns within 6h).
**Proposed corrective action:** Update `methodology/04e-decide-spec-hierarchy.md` Part 2 to require a spec doc linked from the master ticket body. Verify with the verifier that the master ticket body's "Sources" section includes a spec doc path.
```

That is **one finding.** A flow may produce zero findings (a clean run with all six axes `pass`), one finding (a single issue), or N findings (N issues across the six axes).

#### What "observed issue" must contain

- **A path, ticket id, or log line.** Evidence that exists on disk or in the kanban event log. The operator must be able to verify the issue by reading the cited source.
- **A delta or a count.** "Two re-dispatches," "400 lines instead of 80," "5 minutes instead of 1." Vague "slow" or "confusing" is not enough.
- **A timeline marker.** When in the flow the issue occurred (start, middle, end, across the whole run). The retro reader wants to know where the issue fits in the flow.

#### What "proposed corrective action" must contain

- **A destination.** The artifact (doc, skill, flow, profile, op-guard file) that the corrective action will modify.
- **The change.** What will be added, removed, or edited. Prefer concrete edits ("add a 7th field to the flow spec") over aspirational goals ("make flows more rigorous").
- **A verifier check** when possible. How the operator or verifier will confirm the corrective action took effect. "Verify with the verifier that the master's body includes a Sources section" is a verifier check; "should improve" is not.

#### Anti-patterns for findings

- **"We should improve communication."** No destination, no change, no verifier check. Drop it.
- **"The flow was too long."** No destination. Drop it or replace with "add a 7th gate to compress the flow at `methodology/04d-decide-flow-spec.md` §3."
- **"The verifier caught it."** Not a finding; that's the system working as designed.
- **"The agent was slow."** No evidence. Replace with a delta: "the verifier node took 4.2x the expected runtime; see run-log t_X.verifier."
- **One big finding with ten observations.** Split it. One finding per axis per issue. If ten observations cluster, file ten findings with cross-references.

---

## Part 2.5: The 2-part retro shape (Amendment 3 reference)

This section is a **reference card** for the 2-part retro pattern codified in Rule 5. The full rules are in Part 2; this section is the cheat-sheet for workers filing the retro children of a master ticket.

### Reference card

```
Master ticket (t_M, done-gate: operator-LGTM-done per 04c Part 7.5)
├── Retro-A (t_RA) — agent half
│   ├── assignee: verifier (or council / coach)
│   ├── parents: [t_M, t_P4, t_V4, t_VoV, corrective-children-done, ship-done-when-applicable]   ← Amendment 5 (post-council fan-in)
│   │                                                                                          (when no corrective/ship: reduces to [t_M, t_P4, t_V4, t_VoV])
│   ├── spawn-position: POST-COUNCIL (NOT Phase 0; Amendment 5 supersedes v0.6.0 Phase 0 retro spawn)
│   ├── title: "Retro-A: <flow title> (master t_M)"
│   ├── body: Rule 4 §"What goes in the Retro-A task body" template
│   ├── lifecycle: drafting → ready → running → open → done
│   │   (the parent-edge gate is the gate; Retro-A promotes to ready only when ALL listed parents reach done)
│   └── child of master: yes (1 of 2 retro children) — required-by-master-done-gate
│
└── Retro-H (t_RH) — human half
    ├── assignee: operator (literal-human ticket)
    ├── parents: [t_RA]   ← blocks on Retro-A being done (Amendment 4: master NOT in parent list; avoids circular gate)
    ├── spawn-position: AFTER Retro-A reaches done (Amendment 4)
    ├── title: "[HUMAN ACTION] Retro-H: <flow title> (master t_M)"
    ├── body: Rule 4 §"What goes in the Retro-H task body" template
    ├── lifecycle: drafting → ready → running → open → applied → done
    ├── child of master: yes (2 of 2 retro children, via the master's required-children set — NOT via parent edge)
    └── required-by-master-done-gate: yes (operator-LGTM-done per 04c Part 7.5)

Master auto-closes only when BOTH t_RA.status = done AND t_RH.status = done
AND every condition in the master-ticket-flow's done-gate fires (the
v0.7.0 8-condition gate for x-article-review or the equivalent
flow-specific gate for other flows).
```

### Why this card matters

Workers filing retro children for an x-article-review master (the canonical case for v0.7.0) consult this card to confirm the post-council retro-spawn shape (Amendment 5). A worker that files Retro-A at Phase 0 (with `parents=[master]` only — the v0.6.0 shape), or files Retro-H with `parents=[master, retro_a]` (the prior Amendment 3 shape — now superseded; creates circular gate), or files Retro-H with `parents=[master]` only (no retro_a edge — Retro-H would unblock immediately), or assigns Retro-H to a verifier, is filing a malformed retro and will fail the verifier-gate at `applied → done` (for Retro-H) or the post-council parent-edge gate (for Retro-A).

For NON-x-article-review flows that still use Phase 0 retro spawn (per the v0.1.1–v0.6.0 rule), the card's "parents: [t_M]" line is still the canonical Phase 0 shape; the Amendment 5 fan-in parent list is a flow-specific override for x-article-review and any future flow that adopts it.

### Anti-patterns (the card's failure modes)

- **One retro child instead of two.** Wrong. File both.
- **Retro-H assigned to verifier.** Wrong. Retro-H is `operator` (human).
- **Retro-H without `parents=[retro_a]`.** Wrong. Retro-H must block on Retro-A via the retro_a edge. The master is NOT in the parent list (Amendment 4); the master is in Retro-H's required-children set via the done-gate.
- **Retro-H with `parents=[master, retro_a]`.** Wrong (Amendment 4). This was the Amendment 3 shape; it creates a circular gate (master blocks on Retro-H done; Retro-H blocks on master done via the parent edge; neither can transition). The correct shape is `parents=[retro_a]` only.
- **For x-article-review: Retro-A with `parents=[master]` only (Phase 0 retro spawn).** Wrong (Amendment 5). The Phase 0 retro spawn is the v0.6.0 shape that v0.7.0 supersedes; Retro-A's parent list must reference the post-council fan-in `[master, P4, V-4, V-of-V, corrective-children-done, ship-done-when-applicable]` (or the conditional subset when no ship lane ran / no corrective children exist). Retro-A fires AFTER Phase 4 + V-4 + V-of-V + corrective children + ship lane, NOT at master creation.
- **Master closing with only one retro `done`.** Wrong. Master must wait for both retro halves (Retro-A AND Retro-H) via the required-children set + the done-gate.
- **Retro-A running without an independence check.** Wrong. Retro-A writer must not be the executor.

The full rules are in Part 2 (Rules 1–6); the lifecycle is in Part 3; the disposition step is in Part 4; the worked example is in Part 8.

---

## Part 3: The retro lifecycle (v2)

The retro is stateful. The lifecycle is **expressed as kanban task status transitions**, because per Rule 4 the retro IS two kanban tasks — not files. v2 retains v1's 4-state lifecycle with three adjustments, plus **Amendment 3's 2-path split**: Retro-A and Retro-H each have their own lifecycle, in sequence.

### The dual lifecycle (Amendment 3)

```
                                                       ┌─────────────┐
                              master opens (Phase 0)   │  Retro-A    │
                              per 04c Part 7.5         │  drafting   │
                              ▼                        └──────┬──────┘
                       ┌─────────────┐                        │ writer fills the body, runs 6-axis
                       │  Retro-A    │ ◀── master is         │ assessment, proposes findings
                       │  drafting   │     created with      ▼
                       └──────┬──────┘     retro child  ┌─────────────┐
                              │                         │  Retro-A    │
                              │ Retro-A writer publishes│  ready      │
                              ▼                         └──────┬──────┘
                       ┌─────────────┐                        │ dispatcher spawns verifier
                       │  Retro-A    │ ◀── writer transitions│
                       │  open       │     to running → done │
                       └──────┬──────┘                        ▼
                              │                         ┌─────────────┐
                              │ Retro-A reaches `done`  │  Retro-A    │
                              ▼                         │  done       │
                       ┌─────────────┐                  └──────┬──────┘
                       │  Retro-H    │ ◀── Retro-H is filed       │
                       │  drafting   │     as child of master AND │
                       └──────┬──────┘     Retro-A; blocks on RA  │
                              │                                   │ operator reads Retro-A's
                              │ operator dispositions all findings │ findings, adds observations
                              ▼                                          ▼
                       ┌─────────────┐                          ┌─────────────┐
                       │  Retro-H    │ ◀── Retro-H transitions  │  Retro-H    │
                       │  open       │     to running          │  applied    │
                       └──────┬──────┘                          └──────┬──────┘
                              │                                          │
                              │ window closes (24h) or                   │
                              │ all findings dispositioned               │
                              ▼                                          ▼
                       ┌─────────────┐                          ┌─────────────┐
                       │  Retro-H    │ ◀── verifier-gate       │  Retro-H    │
                       │  applied    │     confirms closure    │  done       │
                       └──────┬──────┘     conditions          └─────────────┘
                              │                                         ▲
                              │ Retro-H done + Retro-A done            │ master waits for
                              ▼                                         │ BOTH retros before
                       ┌─────────────┐                                   │ auto-closing
                       │  master     │ ◀── master auto-closes only  ┌───┴──────────┐
                       │  done       │     after both retro halves │  master      │
                       └─────────────┘     reach `done`            │  still open │
                                                                    └──────────────┘
```

The two paths run sequentially, not in parallel. Retro-H cannot start until Retro-A reaches `done`. The master waits for both.

### Lifecycle states per retro half

| State | Retro-A | Retro-H |
|---|---|---|
| `drafting` | writer fills the body template (Rule 4) | operator reviews Retro-A's findings; mirrored into Retro-H body |
| `ready` | writer signals ready to publish; dispatcher can pick up | operator ready to disposition; dispatcher can pick up |
| `running` | writer (verifier) executes the retro: runs 6-axis, fills findings, sets Disposition: PROPOSED | operator (via Retro-H worker or directly) dispositions each finding |
| `open` | Retro-A's findings are visible to the operator; Retro-H is unblocked and can open | operator has 24h window to ACCEPT/REJECT/DEFER each finding |
| `applied` | n/a (Retro-A does not have an `applied` state) | all findings have a disposition; ACCEPTED findings have child tickets |
| `done` | Retro-A writer publishes (audit-line `retro-a-published`); Retro-A closes | verifier-gate confirms closure; audit-line `retro-h-applied`; Retro-H closes |

### Three v2 adjustments

1. **Trigger.** `drafting` of Retro-A starts when the **master ticket opens** (Phase 0, per `methodology/04c-decide-master-ticket.md` Part 7.5) — not when the master reaches `done` (the original Amendment 1 timing). The operator-LGTM-done flow requires the retro children to exist from Phase 0 so the master cannot auto-close before the retro can fire. For auto-done masters (not operator-facing), Retro-A is filed at master `done`; Retro-H is filed at Retro-A `done`.
2. **Window length.** v1 used 24h fixed. The initial v2 draft used the "flow's natural review window" (24–72h+). **Amendment 2 (2026-08-06) restored the fixed 24h window.** The 24h clock starts on Retro-H's `open` transition, not on Retro-A's. Reasoning: predictable windows are easier to reason about; ad-hoc windows invite the operator to forget a retro mid-vacation. The operator may amend the window length per retro (e.g. for a release-grade flow), but the default is 24h.
3. **Closure gating.** For each retro half (`applied → done` for Retro-H, `open → done` for Retro-A), the transition is gated by a verifier-check, per `methodology/05-op-guards.md` op-guard-13 (the verifier-gate pattern). The verifier confirms that every finding has a disposition (Retro-H) or that every finding has `Disposition: PROPOSED` (Retro-A) and that every ACCEPTED finding in Retro-H has a child ticket in the kanban DB.

The master auto-closes only after **both Retro-A and Retro-H reach `done`**. This is Amendment 3's contribution to the lifecycle: the prior lifecycle had only one retro ticket; the dual lifecycle has two, and the master waits for both.

### What "the writer is different" means in the lifecycle

The Retro-A writer's profile is recorded in the Retro-A task body's `Writer profile:` field. This is mandatory; without it, Retro-A is malformed. The verifier-gate at `applied → done` (Retro-H) checks the Retro-A writer's profile against Rule 1 before Retro-H transitions to `done` (and indirectly, before the master closes).

A Retro-A whose writer was the executor is malformed. The verifier-gate blocks the transition; a different writer is assigned; Retro-A is re-published.

### The silent-on-no-action discipline

The retro cron is silent when there is no retro to surface. A master ticket that completed 1 hour ago and the Retro-A task is still in `drafting` is NOT a surface event — the retro cron waits until Retro-A transitions to `open` (writer published), then surfaces Retro-H's window to the operator. The cron's job is to file the retro children at Phase 0, to remind the operator of an open Retro-H nearing its 24h close, and to fire the auto-apply at the window's close; the cron's job is NOT to nag the writer. The writer is bound by the flow's done-gate plus the writer's own latency budget.

### Amendment 2 (2026-08-06) — Window length restored to fixed 24h

The initial v2 draft proposed the "flow's natural review window" (24–72h for typical flows, 1 week for releases). The operator's clarification on 2026-08-06 (per `t_f0cd340b`) restores the fixed 24h window. The window is **24 hours from the Retro-H's `open` transition** (per Amendment 3 — the prior phrasing "from the retro's `open` transition" assumed a single retro). The operator may amend per retro (e.g. extend to 1 week for a release), but the default is 24h and the amendment must be in the Retro-H task body.

### Amendment 3 (2026-08-07) — The retro is 2 parts: agent (Retro-A) + human (Retro-H)

The retro is split into two tasks: **Retro-A** (the agent half — verifier runs the 6-axis evaluation and produces findings) and **Retro-H** (the human half — operator reviews Retro-A's findings, adds observations, and dispositions each finding as ACCEPT / REJECT / DEFER). The master has **two retro children** in its required-child set under the operator-LGTM-done gate; both must reach `done` for the master to auto-close.

**Triggering ticket:** `t_323ad698` (operator clarification on the retro step, 2026-08-07).

#### What changed

- **Rule 1** ("different agent runs the retro") — clarified: the Retro-A writer must be a different agent from the executor (verifier default). The Retro-H writer is the operator (human), and the "different agent" rule is structurally satisfied.
- **Rule 4** ("the retro is a kanban task") — updated: the retro is **two** kanban tasks (Retro-A + Retro-H), not one. Both Retro-A and Retro-H are children of the master; Retro-H is also a child of Retro-A. The Rule 4 body templates now show two templates (Retro-A template + Retro-H template) instead of one. The "What goes in the retro task body" section was renamed and split into "What goes in the Retro-A task body" and "What goes in the Retro-H task body". The "Closing the retro" section was renamed "Closing the retros" with separate closure conditions for each half.
- **New Rule 5** added: "The retro is 2 parts: agent (Retro-A) + human (Retro-H)" — codifies the split, the order, the assignee per half, where each half lives, what each half does, and the anti-patterns.
- **Old Rule 5** ("Structured findings") renumbered to **Rule 6**.
- **Part 2.5** added: a reference card for workers filing retro children at Phase 0. The card shows the parent edges, the lifecycle states, and the anti-patterns.
- **Part 3** (the retro lifecycle) — updated: the single 4-state lifecycle is replaced by a dual-path lifecycle. Retro-A runs `drafting → ready → running → open → done`. Retro-H runs `drafting → ready → running → open → applied → done` and **starts only after Retro-A reaches `done`**. The master auto-closes only after **both** retro halves reach `done`.
- **Part 5** (worked example, formerly Part 5) — will be updated to show 2 retro children, 2 body templates, and the dual disposition flow. See Part 5 for the full walkthrough.

#### Why these changes

The 2-part retro shape codifies the operator's 2026-08-07 clarification: "The retro is 2 parts, one is a retro by an agent (not the agent that performed the execution) that looks at how the flow performed and makes recommendations to improve future runs. The other part of the retro is the human input. So there should be 2 retro tasks, one agent assigned and one human assigned."

The clarification came from a real incident: the x-article-review of article 1 (`t_62a99460`) auto-closed prematurely because the master didn't have an explicit done-gate (fixed by `t_248722d8` adding Part 7.5 to `methodology/04c-decide-master-ticket.md`). The recovery added a single retro ticket. After the operator clarified, the single retro split into Retro-A (`t_bfe37f39`, assignee=verifier) and Retro-H (`t_49af582a`, assignee=operator). The 2-part shape is now the canonical pattern.

The structural rationale:

- **The agent's evaluation (Retro-A) and the operator's review (Retro-H) are distinct voices.** Collapsing them into one ticket muddied the audit trail — the agent wrote the operator's voice, or the operator amended the agent's body. The 2-part shape keeps both voices distinct.
- **The operator's review is not delegable.** Retro-H is a literal-human ticket; `assignee=operator`. The dispatcher surfaces Retro-H to the operator's chat via the `operator-action-dm` cron. Retro-H assigned to an agent is a structural violation.
- **The 24h clock starts on Retro-H's `open` transition, not Retro-A's.** The operator reviews Retro-A's findings; the agent's evaluation must land first. This sequencing is structural, not a suggestion.
- **The master auto-closes only after both retro halves are `done`.** Under `done-gate: operator-LGTM-done`, the master waits for Retro-A and Retro-H both. A master that closes with only one retro `done` is a violation of Part 7.5 of `04c` + this amendment.

#### Cross-references for Amendment 3

- `methodology/04c-decide-master-ticket.md` Part 7.5 (v0.1.1, ticket `t_248722d8`) — the operator-LGTM-done gate that makes the 2-part retro possible. Part 7.5 currently describes the retro child as a single ticket; a follow-up amendment to 04c will update Part 7.5 to describe the retro as **two** children (Retro-A + Retro-H). This is a separate ticket (out of scope for this amendment); until that follow-up lands, treat Part 7.5's "retro child" language as "Retro-A + Retro-H children" per this amendment.
- `methodology/M-decide-human-digest.md` — the human-digest generator is the operator-facing surface for Retro-A's findings. The digest lives in Retro-A's workspace and is mirrored into Retro-H's body. The dual ticket shape is consistent with the digest generator's purpose: Retro-A writes the digest (technical), Retro-H carries it (operator-facing).
- `t_62a99460` — the master ticket that auto-closed prematurely (the incident that motivated the operator-LGTM-done gate).
- `t_49af582a` — Retro-H (the human retro on `t_62a99460`).
- `t_bfe37f39` — Retro-A (the agent retro on `t_62a99460`).
- `t_248722d8` — the v0.1.1 structural fix ticket for the x-article-review loop; includes the operator-LGTM-done gate.

### Amendment 4 (2026-08-11) — Retro-H parent edge is `[retro_a]` only (circular-gate bug fix)

Amendment 3 specified Retro-H's parent list as `parents=[master, retro_a]` (master + Retro-A). The intent was sound: Retro-H is in the master's required-children set (so the master auto-closes only after Retro-H is `done`) AND Retro-H blocks on Retro-A being `done`. **But putting master in Retro-H's parent list created a circular gate:** the master's done-gate waits for Retro-H done; Retro-H's parent edge waits for master done. Neither child could transition. The 2-part retro done gate never fired.

**Triggering ticket:** `t_33843787` (operator amendment, 2026-08-11; this ticket's own source).

#### What changed

- **Rule 4 (the retro is two kanban tasks)** — clarified: Retro-A is filed with `parents=[master]`. Retro-H is filed with `parents=[retro_a]` ONLY. The master's required-children set (per `methodology/04c-decide-master-ticket.md` Part 7.5) still contains BOTH Retro-A AND Retro-H; the master still waits for both to reach `done` before auto-closing. But the parent's edge list (the `parents=[...]` parameter on `kanban_create`) for Retro-H no longer contains the master. The parent edge is a separate mechanism from the required-children set: the parent edge controls whether Retro-H can promote to `ready`; the required-children set controls whether the master can transition to `done`.
- **Rule 5 (the retro is 2 parts)** — clarified: step 2 of the ordering now reads "Retro-H is filed as a child of Retro-A (with `parents=[retro_a]` per Amendment 4); Retro-H is NOT a child of the master via parent edges." The structural rule (Retro-H blocks on Retro-A being `done`) is unchanged; only the parent edge list changed.
- **Rule 5 anti-pattern** — extended: the prior anti-pattern ("Filing Retro-H at Phase 0 with `parents=[master]` only (and not `parents=[master, retro_a]`) is a violation") is now split. The `parents=[master]` only shape is STILL a violation (no retro_a edge means Retro-H would unblock immediately, not after Retro-A is `done`). A new anti-pattern is added: `parents=[master, retro_a]` is ALSO a violation (creates the circular gate). The correct shape is `parents=[retro_a]` ONLY.
- **Part 2.5 reference card** — Retro-H's `parents:` field changed from `[t_M, t_RA]` to `[t_RA]`. Added explicit `child of master: yes (2 of 2 retro children, via the master's required-children set — NOT via parent edge)` and `required-by-master-done-gate: yes` fields to make the parent-edge vs required-children distinction load-bearing for workers filing retro children.
- **Part 2.5 anti-patterns** — added the new anti-pattern (Retro-H with `parents=[master, retro_a]`) explicitly. The "Retro-H without `parents=[retro_a]`" anti-pattern is preserved (Retro-H still must block on Retro-A).
- **Part 5 worked example** — Retro-H's `parents` changed from `[t_404, t_410]` to `[t_410]`. The narrative explaining Retro-H's blocking is updated.
- **Q&A 17** — "Retro-H runs before Retro-A is done" — the parent-edge description updated from `[master, retro_a]` to `[retro_a]` (the master is in the required-children set, not the parent list).
- **Touch-points with other methodology docs** — `methodology/04c-decide-master-ticket.md` Part 7.5 follow-up amendment description updated: the parent-edge shape is now `parents=[retro_a]` only (was `[master, retro_a]`).
- **Implementation example** — the `kanban_create(parents=[...])` call for Retro-H now reads `parents=[retro_a_id]` (was `[master_ticket_id, retro_a_id]`).

#### Why these changes

The circular-gate bug is real and verified. The verified case:

- **Master:** `t_6c49fbd9` (alice-framework, blocked/needs_input — the wandermist article-3 master).
- **Retro-A:** `t_59b36bd5` (alice-framework, done — Retro-A reached `done` cleanly).
- **Retro-H:** `t_993f88f7` (alice-framework, done — operator disposition ticket; but reached `done` only after manual unlink).
- **Manual unlink:** operator ran `hermes kanban unlink t_6c49fbd9 t_993f88f7` to clear the master-as-parent edge before Retro-H could close. The unlink event was logged.

With `parents=[master, retro_a]`: Retro-H could not transition to `done` because master was still blocked. Retro-H could not promote to `ready` because `parents_not_done` (Retro-A was done, master was not). Retro-A was done but the master was blocked. The dispatcher correctly refused the transition because the parent-edge gate is structural.

The fix: Retro-H's parent edge is `[retro_a]` only. The master's required-children set is the canonical mechanism for the master→Retro-H "wait for both retro halves to be done" relationship. The required-children set does not introduce a circular edge because it is a one-way list (master's children-list → what master waits for), not a parent edge (child's parent-list → what child waits for).

#### Structural rationale

The parent-edge mechanism and the required-children-set mechanism are distinct:

- **Parent edge** (`parents=[...]` on `kanban_create`) — controls whether a child task can promote to `ready`. A child with `parents=[X]` cannot promote to `ready` until X reaches `done`. Parent edges are part of the child's definition.
- **Required-children set** (the `done-gate` field on the master) — controls whether the master can transition to `done`. The master waits for its required-children set to reach `done`. The required-children set is part of the master's definition.

Putting the master in Retro-H's parent list is a **structural violation** because parent edges are part of the child's definition, not the parent's. The master waits for Retro-H via the master's required-children set; Retro-H does NOT wait for the master via a parent edge. The retro_a parent edge is sufficient: Retro-H promotes to `ready` when Retro-A reaches `done`, regardless of the master's status.

#### Cross-references for Amendment 4

- `methodology/04c-decide-master-ticket.md` Part 7.5 — the operator-LGTM-done gate. Part 7.5's wording should be updated to clarify that the master's required-children set (not Retro-H's parent list) is what makes the master wait for Retro-H. This is a follow-up amendment to 04c (separate ticket).
- `t_6c49fbd9` — the wandermist article-3 master ticket where the bug surfaced.
- `t_993f88f7` — Retro-H for the wandermist article-3 master (operator disposition ticket for F-R1..F-R12; done after manual unlink).
- `t_59b36bd5` — Retro-A for the wandermist article-3 master (done; verifier 6-axis eval).
- `t_882f78a8` — Retro-A verifier PASS ticket (the verifier evaluated Retro-A's findings; pending master's done-gate).
- `t_33843787` — this ticket (the durable fix).
- `t_248722d8` — the original operator-LGTM-done gate ticket (Part 7.5 of 04c); the structural foundation Amendment 4 inherits.

### Amendment 5 (2026-08-11) — Retro-A spawns post-council with parents=fan-in (premature master auto-close fix)

Prior versions of this doc (Rules 1+4) plus the v0.5.0 done-gate codification of `~/.hermes/methodology/M-decide-x-article-review-flow.md` (instance-side) implied that Retro-A + Retro-H fired at Phase 0 (master creation), with the retro children's `kind=needs_input` blocking the master until operator disposition after Phase 4. That "first in graph, last in execution" pattern was the original structural fix for "master auto-closes before retro fires" (the `t_62a99460` article-1 incident). **The fix worked but introduced a NEW defect** that v0.7.0 closes: when Retro-A reached `done`, the dispatcher's auto-close logic fired `master_auto_done_via_children` and the master auto-closed even with Retro-H still `blocked`. The verified incident:

- **Master:** `t_092e629c` (alice-framework, `done` 2026-08-11 06:41:37 — the wast3 article).
- **Retro-A:** `t_3f241047` (alice-framework, `done` 2026-08-11 06:41:37 — Retro-A reached `done` cleanly and triggered the master auto-close the same second).
- **Retro-H:** `t_057d6495` (alice-framework, `blocked/kind=needs_input` — stranded; the operator never had a chance to disposition Retro-H because the master auto-closed).

The durable fix is structural: Retro-A is moved from Phase 0 to the END of the flow graph, AFTER Phase 4 council verdict + V-4 PASS + V-of-V PASS + every corrective-action child has reached `done` + the conditional ship lane has shipped (when applicable). Retro-A's parent-edge list becomes the post-council fan-in `[master, P4, V-4, V-of-V, corrective-children-done, ship-done-when-applicable]` — every parent in the list must reach `done` before the dispatcher promotes Retro-A to `ready`. Retro-H's parent edge remains `parents=[retro_a]` only (Amendment 4; unchanged).

**Triggering ticket:** `t_42e0c91c` (META; alice-framework, doc-writer). Operator directive 2026-08-11 (verbatim): *"move the retros (A and H) to happen at the end after the council output and any corrective actions have been completed."* Paired-wiki amendment to `~/.hermes/methodology/M-decide-x-article-review-flow.md` (instance-side per op-guard-17) v0.7.0 (amendment 6) is the canonical sister doc.

#### What changed

- **Rule 4** (retro is two kanban tasks) — clarified: Retro-A's parent-edge list now references the post-council fan-in, NOT just `[master]`. The list is `[master, P4, V-4, V-of-V, corrective-children-done, ship-done-when-applicable]` (conditional on whether corrective-action children + ship lane ran). The fan-in expands as the flow progresses.
- **Rule 5** (retro is 2 parts) — clarified: step 1 of the order now reads "Retro-A opens AFTER every upstream gate condition fires — after Phase 4 council verdict + V-4 PASS + V-of-V PASS + every corrective-action child has reached `done` + every conditional ship lane has shipped (when applicable). Retro-A is filed with `parents=[master, P4, V-4, V-of-V, corrective-children-done, ship-done-when-applicable]`. **Retro-A does NOT fire at Phase 0** (the v0.6.0 shape that v0.7.0 supersedes)."
- **Rule 5 anti-pattern** — extended: a new anti-pattern is added for x-article-review: "Retro-A with `parents=[master]` only (Phase 0 retro spawn)." Wrong. Amendment 5 supersedes that shape; Retro-A's parent list must reference the post-council fan-in.
- **Part 2.5 reference card** — Retro-A's `parents:` field changed from `[t_M]` to `[t_M, t_P4, t_V4, t_VoV, corrective-children-done, ship-done-when-applicable]` with the conditional reduction when no ship lane / corrective children exist. The card's Master auto-close line is extended to include "AND every condition in the master-ticket-flow's done-gate fires (the v0.7.0 8-condition gate for x-article-review or the equivalent flow-specific gate for other flows)."
- **Rule count history** — Amendment 5 is a clarification, not a new rule. The canonical Part 2 still has 6 rules.
- **Backwards compat for non-x-article-review flows** — the Amendment 5 fan-in parent list is the canonical form for x-article-review and any future flow that adopts it. A non-x-article-review master-ticket flow that still uses Phase 0 retro spawn (per the v0.1.1–v0.6.0 rule) does NOT violate this doc unless the flow's methodology says otherwise. x-article-review specifically mandates Amendment 5 per `~/.hermes/methodology/M-decide-x-article-review-flow.md` (instance-side per op-guard-17) v0.7.0 Part 5 + Part 7.5.

#### Why these changes (structural rationale)

The Phase 0 retro spawn worked for the article-1 incident (`t_62a99460`, 2026-08-07) because the canonical pattern at the time was "retro fires from `done`" (the strike-17 timing artifact). When the doc-writer codified the 2-part retro shape (Amendment 3, 2026-08-07), Retro-A + Retro-H both filed at Phase 0 with `kind=needs_input` so the master couldn't auto-close until the operator dispositioned the retro findings. The fix introduced a NEW coupling: when Retro-A reached `done` and the dispatcher's auto-close logic fired `master_auto_done_via_children`, the master auto-closed even with Retro-H still `blocked`. The Retro-A `done` state + the master's required-children set (which contains Retro-H) created a race: Retro-A's transition to `done` was the dispatcher's signal that retro work was proceeding, but the master's auto-close logic didn't gate on Retro-H's `done` (Retro-H was still `blocked` from the operator's perspective).

The Amendment 5 fix is structural: Retro-A's parent-edge list references the post-council fan-in, so the parent-edge gate prevents Retro-A from promoting to `ready` until every upstream gate fires. Even when Retro-A would otherwise be ready to run, the parent-edge gate holds it in `blocked/kind=needs_input` until Phase 4 + V-4 + V-of-V + corrective children + ship lane complete. The master cannot auto-close before Retro-A even exists.

#### Touch-points with other methodology docs

- `~/.hermes/methodology/M-decide-x-article-review-flow.md` (instance-side per op-guard-17) v0.7.0 (amendment 6) — paired wiki; codifies the new Part 5 flow order (Council → corrective actions → ship lane → retro) + Part 7.5 8-condition done-gate + Part 8 child graph (retro at END) + Part 10 verification recipe items 13/14/15. The x-article-review methodology is the canonical sibling.
- `methodology/04c-decide-master-ticket.md` Part 7.5 — the operator-LGTM-done gate. Part 7.5 currently describes the retro child as firing at Phase 0; this Amendment 5 changes the parent-edge shape but does NOT change Part 7.5's wording (Part 7.5 is flow-agnostic). A paired-wiki amendment to 04c Part 7.5 is a follow-up (out of scope for Amendment 5) to clarify that x-article-review masters file Retro-A + Retro-H at the END of the flow rather than at Phase 0.
- `~/.hermes/loops/intents/x-article-review-intent.md` v0.7.0 — companion instance change; ships the post-council retro-spawn sequence as a worker-runnable surface per op-guard-17 instance-second.
- `~/.hermes/loops/hermes.yaml` x-article-review v0.8.0 — companion instance change; updates `stop_when` + `worker_prompt` to reflect the new done-gate.

#### Cross-references for Amendment 5

- `~/.hermes/methodology/M-decide-x-article-review-flow.md` (instance-side per op-guard-17) v0.7.0 — paired wiki; Amendment 6 of that doc.
- `t_42e0c91c` — this amendment's META ticket (alice-framework, doc-writer, running).
- `t_092e629c` — the wast3 article master that surfaced the defect (master auto-closed prematurely).
- `t_3f241047` — the wast3 Retro-A that triggered the premature master auto-close.
- `t_057d6495` — the wast3 Retro-H that was stranded at `blocked/kind=needs_input`.
- `t_62a99460` — the article-1 incident (the prior bug that motivated the Phase 0 retro spawn in the first place).
- `t_33843787` — Amendment 4 (circular-gate bug fix; Retro-H parent edge).

---

## Part 4: The disposition step (what happens to findings in Retro-H at `applied`)

When the Retro-H window closes (24h after Retro-H's `open`, or sooner if the operator dispositions all findings), the disposition step runs. **The disposition step is operator-driven, not cron-driven.** The operator has dispositioned each finding in the Retro-H task body during the window; at Retro-H's `applied`, the Retro-H task body carries the operator's decisions. The auto-apply step then files child tickets for ACCEPTED findings.

**As of Amendment 3 (2026-08-07), Part 4 describes the Retro-H disposition step.** Retro-A does not have an `applied` state (Retro-A's lifecycle ends at `open → done` once the writer publishes the findings). All ACCEPT / REJECT / DEFER mechanics happen in Retro-H. Retro-A is the technical record (no dispositions); Retro-H is the operator-facing record (dispositions live here).

For each ACCEPTED finding in Retro-H, a follow-up ticket is filed. The mapping:

| Finding destination | Follow-up board | Parent edge |
|---|---|---|
| Methodology doc change (`methodology/*`) | work-graph substrate board | parent = the methodology doc + the Retro-H task id |
| Skill change (skill prompt, skill content) | agent-profiles board | parent = the skill + the Retro-H task id |
| Flow-spec change (`methodology/04d-decide-flow-spec.md`) | work-graph substrate board | parent = the flow-spec doc + the Retro-H task id |
| Op-guard change | platform-config board | parent = the relevant op-guard file + the Retro-H task id |
| Profile / agent change | agent-profiles board | parent = the profile + the Retro-H task id |
| Anything else | default-routing board | parent = the master ticket that ran + the Retro-H task id |

These board assignments match `methodology/04b-decide-board-routing.md`. Each follow-up ticket's body must reference the finding id (`Finding F-N`) and the Retro-H task id (`t_<id>`) so the work can be traced back to the originating flow. The Retro-H task remains `applied` until the verifier-gate confirms that every ACCEPTED finding has a child ticket; then the Retro-H task transitions to `done`. **The master auto-closes only after both Retro-A and Retro-H reach `done`** (per Part 3 + Part 7.5 of 04c).

### Vetoes and amendments within the window

Within the 24h Retro-H window, the operator has three valid actions per finding (per Rule 4):

1. **ACCEPT** — operator edits the finding's `Disposition:` to `ACCEPTED` and adds a `Operator's observation:` line. The disposition step files a child ticket per the table above.
2. **REJECT** — operator edits the finding's `Disposition:` to `REJECTED` and adds a `Operator's observation:` line stating the reason. No child ticket is filed. The audit-line records `finding-rejected: F-N by operator at <ISO> — <reason>`.
3. **DEFER** — operator edits the finding's `Disposition:` to `DEFERRED — <future date or condition>`. No child ticket is filed now. The deferred-findings index entry is created.

### What if every finding was rejected or deferred

Retro-H still closes via `applied → done`. The audit-line reads: `retro-h-closed-with-no-action: all findings rejected or deferred by operator within window`. That is a valid retro outcome; the system honors the operator's decisions. (Note: this is distinct from "no findings proposed" — see next section.) Retro-A is already `done` at this point; the master auto-closes once Retro-H reaches `done`.

### What if there are no findings

Both retro halves close silently. Retro-A's audit-line: `retro-a-closed-no-findings: 6 axes evaluated, no actionable issues proposed`. Retro-H's audit-line: `retro-h-closed-no-findings: 0 findings to disposition`. This is the correct outcome for a clean run — the writer found nothing to change, the operator had nothing to disposition, the system is stable, no ratchet needed. **Clean runs are the most important retro outcomes**; they tell the operator the system is in equilibrium, which is itself a learning signal. The master auto-closes after both retro halves reach `done`.

---

## Part 5: Worked example (canonical shape for v2, retro-as-2-kanban-tasks)

### The run

Master ticket `t_404: ship the v0.2 methodology release`. Flow has 6 children:

- 2 doc-producing agent nodes (extend 04a and 04b)
- 2 code-producing agent nodes (add a small CLI tool + smoke test)
- 1 verifier agent node (run all smoke tests)
- 1 human node (operator LGTM on the release)

All 6 children complete over 4 days. Verifier passes; operator LGTMs; master transitions to `done`.

### The 2 retro children

Per `methodology/04c-decide-master-ticket.md` Part 7.5, the master has **`done-gate: operator-LGTM-done`** and 2 retro children filed at Phase 0. The retro children are:

- **Retro-A** (`t_410: Retro-A for t_404`) on the work-graph substrate board with `parents=[t_404]`, `assignee=verifier`. Retro-A runs `drafting → ready → running → open → done` as the verifier publishes the 6-axis assessment + findings.
- **Retro-H** (`t_411: [HUMAN ACTION] Retro-H for t_404`) on the same board with `parents=[t_410]`, `assignee=operator` (per Amendment 4, the master is in Retro-H's required-children set via the done-gate, not in the parent list). Retro-H is filed at Phase 0 but blocks on Retro-A being `done`; Retro-H opens when Retro-A reaches `done`.

**Retro-A writer profile:** verifier (per Rule 1; the verifier did not execute any children, only verified).
**Retro-H writer profile:** operator (human; structurally satisfies Rule 1's "different agent" rule).
**Retro-H window:** opens 2026-08-10T15:00Z, closes 2026-08-11T15:00Z (24h, per Rule 4 + Part 3).

### Retro-A body (using the Rule 4 template)

```markdown
# Retro-A: ship the v0.2 methodology release (master t_404)

## Context
- Master ticket: t_404
- Flow run window: 2026-08-06T09:00Z — 2026-08-10T14:30Z
- Writer profile: verifier
- Writer member: verifier-1
- Writer independence: writer did not execute any child

## 6-axis assessment
| Axis | Assessment | Evidence |
|---|---|---|
| a. Right information at start | pass | Both doc-producing nodes had the prior versions of 04a/04b in context; no rework due to missing context. |
| b. Right result first time | partial | Doc nodes shipped clean. Code-producing node's CLI tool shipped without `--help` flag; verifier caught it at end. |
| c. Skills adequate | pass | The polish-loop skill (worked-example-skill-01) covered the cross-reference audit need. |
| d. Flow defined optimally | partial | The two code nodes could have run in parallel; flow spec had them sequential due to a shared test fixture that turned out not to be shared. |
| e. Followed the flow as spec'd | pass | All 6 children followed the flow-spec's gate definitions; no deviation. |
| f. Right agents used | pass | Doc-producing nodes for doc work, code-producing for code, verifier at end. |

## Findings

### Finding F-1
- **Axis:** b (Right result first time)
- **Observed issue:** The code-producing node's CLI tool shipped without `--help`, `--version`, or `--dry-run` flags. The verifier caught this in the final smoke-test pass (event log: t_404.cli-tool.verifier, 1 failure at step 4 of smoke-test). The flags were missing in 100% of the code-producing node's prior runs; the verifier caught it 4 of 5 times.
- **Proposed corrective action:** Update the code-producing skill (skill `code-producing-agent-defaults`) to require `--help`, `--version`, `--dry-run` as default flags on every CLI tool the skill produces. Verify with the verifier that the next code-producing run includes the three flags by default.
- **Disposition:** PROPOSED

### Finding F-2
- **Axis:** d (Flow defined optimally)
- **Observed issue:** The flow spec for t_404 had the two code-producing nodes running sequentially with a `wait_for` edge between them. Investigation showed the shared test-fixture was never actually shared (each node ran in its own scratch workspace). The sequential edge cost ~6 hours of wall-clock time.
- **Proposed corrective action:** Update `methodology/04d-decide-flow-spec.md` Part 3 to add a default-parallelism rule: code-producing nodes without a documented shared fixture should default to parallel. Verify with the verifier that the next flow spec's parallel edges are correctly inferred.
- **Disposition:** PROPOSED

## Retro-A → Retro-H handoff
- Retro-A status: open (waiting for writer to publish)
- Retro-H task id: t_411 (linked)
- Operator's review window opens when Retro-A reaches `done`.
```

### Retro-H body (using the Rule 4 template — findings mirrored from Retro-A)

```markdown
# Retro-H: ship the v0.2 methodology release (master t_404) — operator review

## Context
- Master ticket: t_404
- Retro-A task id: t_410 (linked)
- Retro-A writer: verifier / verifier-1
- Retro-A finished: 2026-08-10T15:00Z
- Operator review window: 2026-08-10T15:00Z — 2026-08-11T15:00Z

## Findings (mirrored from Retro-A)

### Finding F-1
- **Axis:** b (Right result first time)
- **Observed issue:** The code-producing node's CLI tool shipped without `--help`, `--version`, or `--dry-run` flags.
- **Proposed corrective action:** Update `code-producing-agent-defaults` skill to require the three flags by default.
- **Disposition:** ACCEPTED
- **Disposition set:** 2026-08-10T18:00Z
- **Operator's observation:** Three of our last five CLI tools shipped without these flags. Codifying the requirement in the skill is the right fix.

### Finding F-2
- **Axis:** d (Flow defined optimally)
- **Observed issue:** Flow-spec had two code nodes sequential with a wait_for edge; shared test-fixture was never actually shared.
- **Proposed corrective action:** Add a default-parallelism rule to `methodology/04d-decide-flow-spec.md` Part 3.
- **Disposition:** DEFERRED — 2026-09-01 (operator wants to ship v0.2 release first)
- **Disposition set:** 2026-08-10T19:00Z
- **Operator's observation:** Deferred to next retro for v0.3 prep; deferred_findings.md entry created.

## Operator's overall observations
- The flow ran in 4 days instead of the projected 5. The main delay was the parallel-vs-sequential misjudgment (Finding F-2).
- The verifier (Retro-A writer) had clear context — neither doc nor code nodes over-claimed. Retro-A's evidence is solid.

## Operator review window
- Opens: 2026-08-10T15:00Z
- Closes: 2026-08-11T15:00Z
- Default window length: 24 hours
```

The disposition step at Retro-H's window close (or sooner, since the operator dispositioned both findings within 4h) files one child ticket:

- `t_412` (agent-profiles board, parents: skill `code-producing-agent-defaults` + retro `t_411`, finding ref: F-1) — skill change.

`F-2` is deferred; a `deferred_findings.md` entry is created at the work-graph substrate board's inbox referencing `finding_ref: F-2, deferred_to: 2026-09-01, retro_h_ref: t_411`.

### Closure timeline

| Time (UTC) | Event | Source |
|---|---|---|
| 2026-08-10T15:00Z | Retro-A reaches `done`; audit-line `retro-a-published: t_404 at <ISO> — 2 findings proposed` | Retro-A writer publishes |
| 2026-08-10T15:00Z | Retro-H transitions from `drafting` to `ready` (Retro-A is `done`, parent unblock) | dispatcher |
| 2026-08-10T15:30Z | Retro-H transitions to `open` (operator available to disposition) | dispatcher |
| 2026-08-10T18:00Z | Operator dispositions F-1 as ACCEPTED in Retro-H body | operator edit |
| 2026-08-10T19:00Z | Operator dispositions F-2 as DEFERRED in Retro-H body | operator edit |
| 2026-08-10T19:30Z | `kanban_create(parents=[t_411], assignee=jarvis, body=<F-1 spec>)` returns `t_412` | dispatcher |
| 2026-08-10T20:00Z | Verifier-gate confirms Retro-H closure conditions met | verifier check |
| 2026-08-10T20:00Z | Retro-H transitions to `done`; audit-line `retro-h-applied: t_404 at <ISO> — 1 accepted, 0 rejected, 1 deferred; child tickets: t_412` | verifier-gate |
| 2026-08-10T20:00Z | Master `t_404` auto-closes (BOTH Retro-A and Retro-H are `done`; per Part 3 §"Three v2 adjustments" + Part 7.5 of 04c) | dispatcher |

The next release (`t_5XX: ship v0.3`) auto-loads `t_412` (F-1's child) as an input. The CLI tool ships with the right flags. The retro's findings land *as work, not as aspirations*. The system ratchets forward.

### What the operator reads

Two kanban tasks. Retro-A's body has the structured technical record (Rule 4 template); Retro-H's body has the operator-facing disposition (mirrored findings + per-finding ACCEPT/REJECT/DEFER + operator's observations). The operator can scan axis-by-axis in Retro-A for the flow's health, or finding-by-finding in Retro-H for the actionable changes. Both readings are designed to take under 5 minutes. The human-digest (per `methodology/M-decide-human-digest.md`) is the optional translation layer if the operator wants a single-page summary.

---

## Part 6: What this doc is NOT (implementation boundaries)

### Not a cron spec

The retro cron (or its v2 replacement) is a separate ticket. This doc defines the *protocol* — the 6 axes, the writer's profile, the finding shape, the accept/reject/defer flow — not the implementation. The doc-producing agent does not write the cron; the cron-implementing agent writes the cron once the protocol is documented here. The v2 cron must file a retro TASK when a master transitions to `done`, not write a retro FILE to `0-INBOX/`.

### IS tied to a board

The retro IS tied to a board. Specifically: the retro is a kanban task on the appropriate **substrate board** (per `methodology/04b-decide-board-routing.md`). The substrate board is determined by the master ticket's domain:

| Master's domain | Retro board |
|---|---|
| alice-framework methodology work | `alice-framework` |
| skills/profiles work | `agent-resources` |
| hermes platform work | `hermes` |
| patchwork / family / homeschool work | `patchwork` |
| msaa / north-star income work | `msaa-pipeline` |
| Anything else | `default` |

The retro tasks have parent edges as defined by Amendment 4 (2026-08-11): Retro-A is filed with `parents=[master_ticket_id]` and `assignee=<writer-profile>` (default: verifier); Retro-H is filed with `parents=[retro_a_id]` ONLY and `assignee=operator`. The board routing is consistent with `methodology/04b-decide-board-routing.md`. The retro is NOT a free-form note that may live anywhere; it is a structured task on a specific board.

### Not a feedback channel for unrelated thoughts

The retro captures *what was learned from this flow*. If the operator has a thought that's not about this flow — "I want to redesign the agent-fallback chain" — that's a new master ticket, not a retro amendment. Retro amendments stay scoped to the flow that produced them.

### Not a replacement for post-mortems

A retro fires on `done` runs and produces findings about the flow's design and execution. A post-mortem fires on failed / blocked / aborted runs and produces root-cause analysis. The two are different artifacts and live in different places. A flow that aborted mid-run does not get a retro; it gets a post-mortem.

### Not a per-node evaluation

The retro is flow-level. A finding that is purely per-node ("node 3 was slow") is anti-pattern unless the slowness has flow-level implications ("node 3's slowness caused node 4 to start late, which compressed the verifier's window"). Per-node observations without flow-level implications belong in the per-node event log, not in the retro.

---

## Part 7: Diff from v1 (what was preserved, what was replaced)

### Preserved from v1

- The 4-state lifecycle (none → drafting → open → applied). [v2-amendment-2 adds `done` as the verifier-gated terminal state. **Amendment 3 splits this into two lifecycles: Retro-A (`drafting → ready → running → open → done`) + Retro-H (`drafting → ready → running → open → applied → done`)**.]
- The 24h human-feedback window (Amendment 2 restores v1's fixed 24h; **Amendment 3 clarifies the clock starts on Retro-H's `open`, not Retro-A's**).
- The board routing for follow-up tickets (work-graph substrate / agent-profiles / platform-config / default-routing).
- The operator's three valid actions in the window (amend / veto / no-response). [v2-Rule-4 re-expresses these as ACCEPT / REJECT / DEFER per finding. **Amendment 3 specifies the dispositions live in the Retro-H body, not Retro-A.**]
- The retro's anti-pattern list (no silent application, no per-node retro, no indefinite window, no meeting-style retro).

### Replaced from v1

- **Scope:** per-node → **per-flow**.
- **Writer:** executor → **different agent** (verifier default).
- **Trigger eligibility:** every graph execution → **master-ticket flows only**.
- **Finding shape:** 3 freeform fields → **structured findings** (observed issue + proposed corrective action, evaluated on 6 axes).
- **Window length:** v1 was 24h; initial v2 was "flow's natural review window"; **Amendment 2 restores fixed 24h**.
- **Number of tasks:** v1 had 1 retro file (or 1 comment block); **Amendment 3 splits into 2 tasks**: Retro-A (agent half, `assignee=verifier`) + Retro-H (human half, `assignee=operator`). Master auto-closes only after both reach `done`.

### Amendment 1 changes (2026-08-06) — what initial-v2 changed that this amendment supersedes

- **Artifact format:** initial v2 published the retro as a **file** (vault note + ticket comment). **Amendment 1 supersedes this:** the retro is a **kanban task** on a substrate board, with the 6-axis assessment + findings in the task body. The retro file pattern is no longer used.
- **Operator's window actions:** initial v2 said "amend / veto / no response." **Amendment 1 re-expresses as per-finding ACCEPT / REJECT / DEFER.** The disposition lives in the retro task body, not in a comment.
- **Auto-apply:** initial v2 had a cron-driven auto-apply step at window-close. **Amendment 1 makes the disposition operator-driven** (the operator dispositioned each finding during the window; the verifier-gate confirms at `applied`). The cron no longer auto-files follow-up tickets.

### Amendment 3 changes (2026-08-07) — what Amendment 1 + 2 had that this amendment supersedes

- **Number of tasks:** Amendment 1 + 2 had **one** retro task per master (a single ticket with the verifier as writer and the operator dispositions in the body). **Amendment 3 supersedes this:** the retro is **two** tasks (Retro-A + Retro-H). The Retro-A ticket is the technical record; the Retro-H ticket is the operator-facing review. Both are required children of the master under the operator-LGTM-done gate.
- **Assignee per half:** Amendment 1 + 2 had the verifier as the sole writer (with operator amendments in the comments). **Amendment 3 splits the assignee:** Retro-A is `verifier` (per Rule 1); Retro-H is `operator` (literal-human ticket, surfaces via `operator-action-dm` cron).
- **Lifecycle:** Amendment 1 + 2 had a single 4-state lifecycle (`drafting → open → applied → done`). **Amendment 3 splits the lifecycle:** Retro-A runs `drafting → ready → running → open → done`; Retro-H runs `drafting → ready → running → open → applied → done` and starts only after Retro-A is `done`. The master waits for both.
- **Window length:** Amendment 2 set the 24h clock to start on the retro's `open` transition. **Amendment 3 clarifies:** the 24h clock starts on **Retro-H's** `open` transition (not Retro-A's), since the operator's review begins when Retro-H opens, not when Retro-A opens.
- **Master close:** Amendment 1 + 2 had the master auto-close after the single retro's `applied → done`. **Amendment 3 changes this:** the master auto-closes only after **both** retro halves reach `done`. Under `done-gate: operator-LGTM-done`, the master's required-child set is `{Retro-A, Retro-H}` (per `methodology/04c-decide-master-ticket.md` Part 7.5 + this amendment).

### Removed from v1

- The "What worked" freeform field (replaced by axis assessments; positive observations live inside the assessment).
- The optional "What surprised us" 4th field (replaced by the 6 axes; surprise is captured as a partial or fail on the relevant axis).
- The per-node triggering rule (no longer applicable).

If you find yourself wanting v1's per-node retro or its 3-freeform-fields shape, you are looking for a post-mortem or a flow-level audit, not a retro. Those are different artifacts with different protocols.

---

## Maintenance

This section defines the maintenance cadence for the flow-level retro pattern. Retros are the engine of self-improvement, so this surface is reviewed more frequently than slow-moving methodology: every 30 days.

### 1. Audit cadence

Audit the retro pattern every **30 days**. The audit reviews the preceding period's master-ticket flows and checks whether eligible flows generated the two retro tasks (Retro-A + Retro-H) per the Amendment 3 contract. Record the audit date, reviewer, number of eligible flows, number of retro pairs, and next-audit date in the audit ticket or methodology changelog. Run an off-cycle audit when any drift signal below fires.

### 2. Quality threshold

The retro pattern passes when all of these conditions hold:

- Every eligible master-ticket flow generated BOTH a Retro-A and a Retro-H task (Amendment 3). A flow with only one retro task is a failed check.
- **Retro-H's parent edge is `parents=[retro_a]` only** (Amendment 4, 2026-08-11). Retro-H with `parents=[master, retro_a]` is a circular-gate violation; Retro-H with `parents=[master]` only is a missing-parent-gate violation. Both fail the check.
- Retro-A was written by a different agent than the executor (Rule 1). Retro-H is `assignee=operator` (literal-human ticket; structurally satisfies Rule 1).
- All **6 evaluation axes** in Retro-A have a pass/partial/fail assessment with evidence.
- Every finding contains an observed issue and a proposed corrective action with a destination.
- Findings were ACCEPTED, REJECTED, or DEFERRED by the operator in Retro-H within the fixed **24-hour** Retro-H window (starting on Retro-H's `open`, per Amendment 3) — therefore within the **7-day** maintenance ceiling — and each ACCEPTED finding has a child ticket with `parents=[retro_h_id]`.
- The master auto-closed only after BOTH Retro-A and Retro-H reached `done` (per Part 3 + Part 7.5 of 04c).

A missing axis, unsupported finding, missing disposition, missing retro half, an incorrect Retro-H parent edge (per Amendment 4), or a master that closed with only one retro `done` is a failed check, not partial credit. Record the verdict and corrective action for each failed condition.

### 3. Drift signals

Surface drift before the next scheduled audit when any of these occurs:

- No eligible master-ticket flow has produced a Retro-A + Retro-H pair in **30 days**.
- A master-ticket flow has Retro-A but no Retro-H (or vice versa) — the 2-part shape is broken.
- **A Retro-H is filed with `parents=[master, retro_a]` or `parents=[master]` only** — the parent-edge shape is wrong (Amendment 4, 2026-08-11). The correct shape is `parents=[retro_a]` only.
- Retro-A or Retro-H findings repeatedly remain PROPOSED or are not actioned after acceptance.
- Retro-A or Retro-H tasks accumulate in `ready` or `running` without resolution.
- A retro omits one or more axes, lacks verifiable evidence, or uses an executor as Retro-A writer.
- Retro-H is assigned to an agent (verifier / coder / planner / jarvis / doc-writer) instead of `operator`.
- The 24-hour Retro-H review window is routinely missed or dispositions are not reflected in the Retro-H body.
- A master closed with only one retro `done` (violates Part 3 + Part 7.5 of 04c).

These signals indicate that the retro pattern or its implementation is no longer producing usable learning.

### 4. Fix actions

When no retro has fired in 30 days, file a `retro-trigger` ticket against the relevant master-ticket flow or, if no eligible flow exists, record that fact in the audit. When findings are not actioned, trace each ACCEPTED finding to its child ticket; file a maintenance ticket for any missing edge and escalate unresolved disposition or execution gaps to the operator. When tasks build up in `ready`, inspect board routing and assignee availability before changing this methodology. Do not edit the quality threshold merely to make a failed audit pass; fix the trigger, routing, writer, or follow-up flow and verify the next eligible run.

### 5. Retirement conditions

If retro tasks consistently go **30+ days without acceptance** or the pattern produces no actionable learning across repeated audits, treat the retro pattern as failing and propose a council review. The council may retire, merge, or revise this section, but retirement is an operator decision. Preserve existing retro tasks, findings, and child-ticket links as historical evidence; update cross-references if a successor pattern is adopted.

### Maintenance parity check

The maintenance contract is portable: map audit, quality checks, drift detection, corrective routing, and retirement review to the kanban and review mechanisms available in the deployment. Any deviation from the 30-day cadence, 24-hour window, or six-axis threshold must be recorded with its reason and next review date.

---

## Human-readable digest

After Retro-A publishes the technical kanban task, generate the separate operator-facing digest according to `methodology/M-decide-human-digest.md` and `templates/human-digest.md.template`. The digest is a translation layer, not a replacement: the retro task remains the canonical audit record, while the digest gives Retro-H a plain-language headline, six-axis summary, recommendations, Accept / Reject / Defer decision table, operator observations, collapsible evidence anchors, and per-seat / per-axis individual responses. The digest is saved in the retro workspace and surfaced through the Retro-A and master comments; a connected operator chat may receive it as an attachment. The 10-section shape is operator-led: actionable top tier (sections 1–3), transitional interpretation layer (sections 4–5), operator action surface (sections 6–7), technical-detail bottom tier (sections 8–10).

The digest does not alter the Rule 4 lifecycle, auto-accept findings, or remove technical evidence. Its generation and delivery implementation belongs in a separate worker ticket; this methodology document defines the contract.

## See also

- `methodology/06-iteration-loop.md` — the iteration loop primitive that the retro wraps around; the retro is the *post-execution* step that closes the loop after one full iteration. The retro is also the `feedback` element of the iteration loop, raised from per-tick (cron) to per-flow (retro).
- `methodology/M-decide-human-digest.md` — operator-facing translation layer for Retro-A findings; the technical retro remains canonical. The human-digest is the optional single-page summary the operator reads before opening Retro-H.
- `methodology/04c-decide-master-ticket.md` — master tickets are the scope of the retro. The retro fires on a master's `done` transition. **v0.1.1 (2026-08-07, t_248722d8)**: Part 7.5 of 04c codifies that operator-facing flows MUST explicitly opt into `done-gate: operator-LGTM-done` in the master body, and the retro child MUST be spawned at Phase 0 (master creation) — not at phase completion — so the master cannot auto-close before the retro can fire. **v0.1.3 (Amendment 3, 2026-08-07, t_323ad698)**: Part 7.5's "retro child" language is interpreted as **two** retro children (Retro-A + Retro-H) per this amendment. A follow-up amendment to 04c Part 7.5 will update the wording from "the retro child" to "the two retro children (Retro-A + Retro-H)" with the parent-edge shape described in Part 2.5 of this doc; until that follow-up lands, treat Part 7.5 as describing the 2-children shape per Amendment 3.
- `methodology/04a-decide-work-graph.md` — work-graph states (`ready`, `running`, `done`); the retro fires on the `done` transition of a master.
- `methodology/04d-decide-flow-spec.md` — the 6-field flow spec; the retro's findings on axis d (flow defined optimally) target this doc.
- `methodology/05-op-guards.md` — operational guards often originate from retro findings on axis c (skills adequate) or axis f (right agents used). The `[HUMAN ACTION]` child-ticket protocol (op-guard-3 + `human-action-child-pattern.md`) is what Retro-H uses for its title prefix.
- `methodology/03b-decide-operator-agent-interaction.md` — the operator-action ticket lifecycle (surface → respond → resume); the retro's window is one instance of this lifecycle.
- `methodology/07-council-methodology.md` (forthcoming) — councils may run retros for cross-flow findings when the verifier is not enough.
- `methodology/06a-decide-retro.md` — v1 of this doc; superseded by v2. Preserved in git history for reference; do not edit.
- `2-ATOMIC/concepts/human-action-child-pattern.md` — the canonical `[HUMAN ACTION]` child-ticket protocol. Retro-H's title prefix is `[HUMAN ACTION]` per this protocol.

## What's next

- `methodology/06b-decide-retro-cron-v2.md` (forthcoming) — the cron that files **two** retro TASKS (Retro-A + Retro-H) when a master transitions to `done`, manages the 24h Retro-H window, and fires the verifier-gate at the window's close. The prior v2 cron (which wrote `0-INBOX/retro-*.md` files) is superseded; the v2 cron must be re-implemented to call `kanban_create` twice (Retro-A + Retro-H) instead of writing a file.
- `methodology/07-council-methodology.md` (forthcoming) — when to use a council as the Retro-A writer (cross-flow findings, methodology-level decisions).
- `methodology/06c-decide-coach-profile.md` (forthcoming, conditional) — if the retro volume justifies a dedicated coach profile, this doc defines the profile.
- `references/tool-mapping-guide.md` — how to implement retro-as-2-kanban-tasks + 6-axis + structured findings + ACCEPT/REJECT/DEFER in concrete tools (`kanban_create` twice for Retro-A + Retro-H, body templates from Rule 4, verifier-gate at Retro-H's `applied → done`).

## Anti-patterns to watch for

1. **"The retro is a per-node event."** → No. v2 retros are flow-level. **TWO retro tasks** (Retro-A + Retro-H) per master (Amendment 3), not one.
2. **"The executor writes the retro."** → No. v2 mandates a different agent. Retro-A's writer is the verifier (or council/coach). Retro-H's writer is the operator. The executor's perspective is biased; the writer's perspective is independent.
3. **"The retro fires on every cron tick."** → No. v2 restricts retros to master-ticket flows. Crons have their own feedback mechanism (the iteration loop's `feedback` element).
4. **"The findings are a wish list."** → No. Each finding has observed evidence (path, ticket, count, delta) and a corrective action with a destination (doc, skill, flow, profile).
5. **"One finding with ten observations."** → No. One finding per axis per issue. Split multi-observation findings into N findings with cross-references.
6. **"The window is fixed at 24h, no exceptions."** → No, but also no. v2 uses fixed 24h by default; the operator may amend per retro (e.g. extend to 1 week for a release). The amendment must be in the **Retro-H task body** (per Amendment 3; the clock starts on Retro-H's `open`, not Retro-A's).
7. **"The retro is a meeting."** → No. The retro is **two kanban tasks** (Retro-A + Retro-H) with fixed body shapes (Rule 4 templates). It is not a synchronous gathering and it is NOT a file.
8. **"The retro applies silently to the next run."** → No. The retro's ACCEPTED findings spawn follow-up tickets with parent edges back to the Retro-H task. The next iteration's work is the union of those tickets, not a silent diff.
9. **"The retro on a failed run is fine."** → No. Failed/blocked/aborted runs get post-mortems, not retros. The retro fires only on `done` masters.
10. **"The retro waits indefinitely for the writer."** → No. The Retro-A writer is bound by the flow's done-gate plus a latency budget (proposed in this doc; the cron-implementing agent owns the exact number). If Retro-A doesn't publish in time, the cron surfaces the staleness. Retro-H waits on Retro-A.
11. **"The retro is a file in 0-INBOX/."** → No. The retro is **two kanban tasks** on a substrate board (Rule 4 + Amendment 3). Files in `0-INBOX/` were the v1 pattern and the initial v2 draft's pattern; both are superseded.
12. **"The cron auto-applies unvetoed findings."** → No. The disposition is operator-driven (ACCEPT / REJECT / DEFER per finding, per Rule 4). The cron's role at Retro-H's window close is to fire the verifier-gate, not to file follow-up tickets.
13. **"The operator dispositions the retro all-or-nothing."** → No. The disposition is per-finding. A Retro-H with 5 findings may have 3 ACCEPTED, 1 REJECTED, 1 DEFERRED.
14. **"The retro closes when the writer publishes."** → No. The retro pair closes (`Retro-A → done` and `Retro-H applied → done`) only after every finding has a disposition in Retro-H and every ACCEPTED finding has a child ticket, gated by the verifier-gate (op-guard-13). **The master auto-closes only after BOTH retro halves reach `done`** (per Amendment 3 + Part 7.5 of 04c).
15. **"The retro is a single task."** → No (Amendment 3). The retro is two tasks: Retro-A (agent half, `assignee=verifier`) + Retro-H (human half, `assignee=operator`). A single-task retro is a structural violation as of 2026-08-07.
16. **"Retro-H is assigned to a verifier or other agent."** → No. Retro-H is `assignee=operator` (literal-human ticket). Retro-H assigned to verifier / coder / planner / jarvis / doc-writer is a structural violation.
17. **"Retro-H runs before Retro-A is done."** → No. Retro-H is a child of Retro-A via the retro_a parent edge; Retro-H cannot open until Retro-A reaches `done`. The parent edge `parents=[retro_a]` is structural (per Amendment 4, 2026-08-11; the master is NOT in the parent list — the master is in Retro-H's required-children set via the done-gate, which is a separate mechanism).
18. **"The master closes with only one retro `done`."** → No. Under `done-gate: operator-LGTM-done`, the master waits for BOTH Retro-A and Retro-H to reach `done`. Closing with one retro `done` violates Part 3 of this doc + Part 7.5 of `methodology/04c-decide-master-ticket.md`.

## Touch-points with other methodology docs

This doc touches:

- `methodology/06-iteration-loop.md` — append paragraph: "the iteration loop's `feedback` element is per-tick for crons and per-flow for master-ticket flows. The retro is the per-flow `feedback`. Per Rule 3 of 06a-v2, the retro does not fire for cron-only flows. **Per Amendment 3, the per-flow `feedback` has two tasks (Retro-A + Retro-H), not one.**"
- `methodology/04a-decide-work-graph.md` — note in Part 1: "the `done` state transition of a master triggers TWO retro tasks (Retro-A + Retro-H per 06a-v2 Rule 5) on the appropriate substrate board (per 06a-v2 Rule 4). The retro tasks are co-located with the master's `done` event, not separately scheduled. Per Rule 3, retros do not fire on non-master tickets or on non-`done` transitions."
- `methodology/04c-decide-master-ticket.md` — Part 7.5 (v0.1.1, t_248722d8) needs a **follow-up amendment** to update the wording from "the retro child" (singular) to "the two retro children (Retro-A + Retro-H)" per Amendment 3. The parent-edge shape (Retro-H `parents=[retro_a]` only, per Amendment 4 — the master is in Retro-H's required-children set, not the parent list) and the master-auto-close gate (`done-gate: operator-LGTM-done` requires BOTH retro halves to reach `done`) are documented in Part 2.5 of 06a-v2. Until the 04c follow-up lands, treat Part 7.5 as describing the 2-children shape per Amendment 3.
- `methodology/04b-decide-board-routing.md` — add retro board routing table from Part 6 (alice-framework → alice-framework, skills → agent-resources, hermes → hermes, patchwork → patchwork, msaa → msaa-pipeline, default → default). Both Retro-A and Retro-H live on the same substrate board per Amendment 3.
- `methodology/04d-decide-flow-spec.md` — extend the 6-field spec with a 7th optional field: `retro_writer` (the profile that runs Retro-A; default verifier; council for cross-flow findings). The 24h window field is implicit per Amendment 2 + 3.
- `methodology/05-op-guards.md` — add op-guard-17 candidate: "Retro-A writer is the executor" — same shape as op-guard-13's verifier-gate pattern; the Retro-A writer's profile must be checked against the executor's profile at Retro-H's `applied → done`. Add op-guard-18 candidate: "retro is a file in 0-INBOX/" — the durable artifact is the kanban task (Retro-A + Retro-H), not a vault file. Add op-guard-19 candidate: "Retro-H is assigned to an agent" — Retro-H must be `assignee=operator`, surfaces via `operator-action-dm` cron.
- `methodology/04e-decide-spec-hierarchy.md` — note: a retro finding that proposes a methodology doc change should specify the destination section/Part, not just the doc title. Findings without a destination section are anti-patterns.

## Operator-environment implications

The retro-as-kanban-task pattern (Rule 4) — extended to **two tasks** (Retro-A + Retro-H) per Amendment 3 — has implications for the operator's existing cron + script infrastructure. Specifically:

**If the operator's environment currently has a `post-graph-retro` cron (or equivalent) that produces retro FILES in `0-INBOX/`, that infrastructure is superseded by the retro-as-kanban-task pattern.** The cron must be re-implemented (per the spec-first flow in `methodology/M-decide-spec-first-flow.md`) to:

1. **File TWO retro TASKS** (Retro-A + Retro-H) per master, not one. Retro-A via `kanban_create(parents=[master_ticket_id], assignee=verifier, board=<substrate board>)`; Retro-H via `kanban_create(parents=[retro_a_id], assignee=operator, title_prefix='[HUMAN ACTION]', board=<substrate board>)` (per Amendment 4, 2026-08-11: the master is NOT in Retro-H's parent list; it is in the master's required-children set via the done-gate). The prior single-file pattern (writing to `0-INBOX/retro-*.md`) is fully superseded.
2. **Use the Rule 4 body templates** (the Retro-A + Retro-H templates from Rule 4's "What goes in the Retro-A task body" + "What goes in the Retro-H task body" sections) as the retro tasks' `body` parameters. The Retro-A template carries Context + 6-axis assessment + Findings; the Retro-H template carries mirrored findings + Operator's observations + per-finding disposition.
3. **Manage the 24h Retro-H window** (per Amendments 2 + 3) instead of the "flow's natural review window" the initial v2 draft proposed. The clock starts on Retro-H's `open`, not Retro-A's.
4. **Fire the verifier-gate** at Retro-H's window close (per Part 3's Closure gating and Part 4's disposition step) — the verifier confirms every finding has a disposition and every ACCEPTED finding has a child ticket, before Retro-H transitions to `done`. The master auto-closes only after BOTH Retro-A and Retro-H reach `done`.

**This is a separate spec.** Per the spec-first flow rule (`methodology/M-decide-spec-first-flow.md`, `op-guard-16-spec-first-flow`), the doc-writer (this doc's author) does NOT modify the operator's cron or script. The doc is the spec. The operator (or a coder, per the spec-first flow) will file a separate ticket to update the cron + script to call `kanban_create` (twice — for Retro-A + Retro-H) instead of writing a file.

**The doc-writer does NOT file infrastructure tickets.** Filing the cron-update ticket is the operator's decision, not the doc-writer's.

**Until the cron is re-implemented, the existing `post-graph-retro` cron continues to run as-is.** The pattern in this doc is the forward-looking spec; the cron migration is a separate work-stream.

**For the operator's immediate action:** none. This doc is a spec; the implementation is a separate ticket. The operator may, if desired, file a `cron-update` ticket against the hermes board (`assignee=coder` or `default`, parent=this retro spec doc).

---

## Open questions for the operator

These four questions were open in the initial v2 draft. **Amendment 1 + 2 (2026-08-06) resolved three of them.** The remaining question is tracked under tag `kind:retro-open-question`.

- **Q1.** Which of the 3 retro-agent options should the doc recommend as the default? **RESOLVED (per `t_f0cd340b`, 2026-08-06): verifier.** The doc recommends verifier because it is the lowest-ceremony option that satisfies the "different agent" rule. Coach is reserved for high-volume periods. The recommendation stands.
- **Q2.** What about planner/council flows that don't have a formal master ticket? Are those "ad-hoc master" (retro applies) or "ad-hoc work" (no retro)? **RESOLVED (per `t_f0cd340b`, 2026-08-06): no retro by default.** The rationale is Rule 3's master-scope requirement. Planner/council flows that don't file a master ticket are treated as ad-hoc work; no retro fires. The operator may amend by defining a "synthetic master" pattern for planner/council flows in a future revision.
- **Q3.** What is the operator's preferred default for the operator review window? **RESOLVED (per `t_f0cd340b`, 2026-08-06): fixed 24h.** The initial v2 draft proposed a variable "flow's natural review window"; Amendment 2 restores the fixed 24h default. The operator may amend per retro (e.g. extend to 1 week for a release), but the default is 24h.
- **Q4.** Where do the structured findings get filed — in the retro file, in a separate audit doc, in the master ticket body? **RESOLVED (per `t_f0cd340b`, 2026-08-06): in the retro TASK body (Rule 4).** Each finding is a `### Finding F-N` block in the retro task body, with axis / observed issue / proposed corrective action / disposition / disposition set / notes fields. Follow-up tickets reference the finding id (`Finding F-N`) and the retro task id (`t_<id>`). No separate file or audit doc is created.

The resolution of Q1, Q2, Q3, Q4 produced doc amendments + audit-line entries. **Amendment 3 (2026-08-07, t_323ad698)** superseded the prior canonical statement with the 2-part retro shape (Retro-A + Retro-H). The doc is canonical as of Amendment 3.

### Future amendment candidates (not currently open questions)

- **Q5.** Should the Retro-A task have a dedicated assignee (a named verifier, e.g., `verifier-1`) or a profile-level assignee (`verifier`)? The current doc says profile-level. A future amendment may switch to named-assignee for accountability.
- **Q6.** When the Retro-A writer is a council (per Rule 1's option), how are council members recorded in the Retro-A task body — a list of names, or a single `council` profile with member names in the body's `Writer member:` field? The current doc says single profile + member list. A future amendment may switch to a `Council members:` array.
- **Q7.** Should Retro-H's title prefix be `[HUMAN ACTION]` (current canonical, per `2-ATOMIC/concepts/human-action-child-pattern.md`) or a different prefix? The current doc says `[HUMAN ACTION]`. A future amendment may standardize the prefix across all retro-h tickets.
- **Q8.** Should the master-ticket methodology (Part 7.5 of `04c`) update its "retro child" wording to "two retro children (Retro-A + Retro-H)" as a follow-up amendment, or stay with the singular "retro child" wording and rely on cross-reference to Amendment 3? The current 06a-v2 cross-references the follow-up as a separate ticket.

## Historical note: v1

v1 of this document (`methodology/06a-decide-retro.md`, dated 2026-08-05) documented the 1-retro-per-node variant with 3 freeform fields. v2 supersedes it on 2026-08-06 per the operator's direction.

The preserved content from v1 (the lifecycle, the window, the board routing, the anti-patterns) is incorporated into v2 with the v2 adjustments. v1's per-node trigger, executor-as-writer rule, and freeform-fields shape are explicitly removed.

v1 is preserved in git history. Do not edit v1; the canonical retro spec is v2. If you find yourself editing v1, you are looking for v2.

## Historical note: initial v2 draft (before Amendment 1 + 2)

The initial v2 draft (shipped by `t_3569c32c` on 2026-08-06 at 07:42Z) had two design choices that Amendment 1 + 2 (per `t_f0cd340b`, 2026-08-06) supersede:

1. **Retro as a file.** The initial v2 draft published the retro as a file (vault note + ticket comment). Amendment 1 changes this to a kanban task on a substrate board.
2. **Variable window.** The initial v2 draft used the "flow's natural review window" (24–72h+). Amendment 2 restores the fixed 24h window.

Both amendments are operator-driven; the doc-writer (this doc's author) implemented them as edits to the doc, not as new docs. The doc is canonical as of the Amendment 1 + 2 edits.

The diff between the initial v2 draft and the Amendment 1 + 2 version is summarized in Part 7 ("Diff from v1") and is documented in `t_f0cd340b` (this ticket's body).

## Historical note: Amendment 3 (after Amendment 1 + 2)

Amendment 3 (per `t_323ad698`, 2026-08-07) split the retro into two parts: Retro-A (agent half, `assignee=verifier`) + Retro-H (human half, `assignee=operator`). The Amendment 1 + 2 design had **one** retro ticket with the verifier as writer and operator dispositions in the body. Amendment 3 separates the agent's evaluation (Retro-A) from the operator's review (Retro-H) into two distinct tickets with distinct assignees and distinct lifecycles. The master now has **two retro children** (Retro-A + Retro-H) under `done-gate: operator-LGTM-done`; both must reach `done` for the master to auto-close.

The doc is canonical as of Amendment 3. Future amendments (Q7, Q8 in the Future amendment candidates section above) track remaining open questions about Retro-H's title prefix and the follow-up amendment to `methodology/04c-decide-master-ticket.md` Part 7.5.

## Audit-line

`## [2026-08-06T07:40Z] retro-v2-methodology — methodology/06a-decide-retro-v2.md shipped; 4 rules (different agent / 6 axes / master-only / structured findings), flow-level scope, supersedes v1. Open questions Q1–Q4 await operator answers; v1 preserved in git history.`

`## [2026-08-06T08:00Z] retro-v2-amendment-1+2 — methodology/06a-decide-retro-v2.md amended per t_f0cd340b. Rule 4 added (retro is a kanban task, not a file); prior Rule 4 (structured findings) renumbered to Rule 5. Window length restored to fixed 24h (Amendment 2). Operator disposition is per-finding ACCEPT/REJECT/DEFER (Amendment 1); verifier-gate at applied→done (op-guard-13). Q1, Q2, Q3, Q4 resolved. Operator-env implication: existing post-graph-retro cron (if any) is superseded; cron-update is a separate ticket (per spec-first flow, op-guard-16). Doc is canonical as of this amendment.`

`## [2026-08-07T18:30Z] retro-v2-amendment-3 — methodology/06a-decide-retro-v2.md amended per t_323ad698 (operator clarification 2026-08-07). The retro is now 2 parts: Retro-A (agent half, assignee=verifier, runs the 6-axis evaluation + proposes findings) + Retro-H (human half, assignee=operator, dispositions findings ACCEPT/REJECT/DEFER). The master has TWO retro children under done-gate: operator-LGTM-done; both must reach done for the master to auto-close. New Rule 5 codifies the split; old Rule 5 renumbered to Rule 6. Rule 4 updated: 2 body templates (Retro-A + Retro-H), 2 closure paths. Part 3 lifecycle updated to dual-path (Retro-A runs drafting→ready→running→open→done; Retro-H runs drafting→ready→running→open→applied→done and starts only after Retro-A is done). Part 2.5 added: reference card for workers filing retro children at Phase 0. Part 5 worked example updated to show t_410 (Retro-A) + t_411 (Retro-H) + t_412 (F-1 child). Cross-references to 04c Part 7.5 + t_248722d8 updated to note that 04c's "retro child" language is interpreted as 2 children per this amendment; a follow-up amendment to 04c will update the wording. Doc is canonical as of Amendment 3. Frontmatter version 0.1.3, updated 2026-08-07T18:30Z, amended_by adds t_323ad698, tags add amendment:3 + kind:two-part + kind:retro-a + kind:retro-h.`
