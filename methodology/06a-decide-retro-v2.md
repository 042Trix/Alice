---
id: alice-methodology-06a-decide-retro-v2
created: 2026-08-06T07:40:00Z
updated: 2026-08-06T08:00:00Z
title: "Methodology 06a (v2, Amendment 1+2) — Decide the retro (flow-level retro by a different agent on master-ticket flows; retro is a kanban task)"
type: methodology
status: draft
supersedes: ["[[methodology/06a-decide-retro.md]]", "[[methodology/06a-decide-retro-v2.md#initial-v2-draft]]"]
amended_by: "[[ticket:t_f0cd340b]]"
amendment_initial_v2_draft_by: "[[ticket:t_3569c32c]]"
source: alice-framework
tags: [kind:methodology, kind:retro, kind:iteration-loop, kind:feedback, kind:self-improvement, kind:flow-level, project:alice, amendment:1, amendment:2]
confidence: 0.0
links: ["[[methodology/06-iteration-loop.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[methodology/04b-decide-board-routing.md]]", "[[methodology/04c-decide-master-ticket.md]]", "[[methodology/04d-decide-flow-spec.md]]", "[[methodology/05-strike-rules.md]]", "[[methodology/M-decide-spec-first-flow.md]]", "[[methodology/06a-decide-retro.md]]", "[[worked-examples/01-solo-founder-skeleton/AGENTS.md]]"]
---

# Methodology 06a (v2, Amendment 1+2) — Decide the retro (flow-level retro by a different agent on master-ticket flows; retro is a kanban task)

> v1 documented a 1-retro-per-node shape with 3 freeform fields ("what worked / what didn't / what to change"). v2 changes the shape, the writer, the scope, and the format. The retro is now **flow-level**, **written by a different agent than the executor**, **scoped to master-ticket flows**, **structured as observed-issue + proposed-corrective-action findings**, and **filed as a kanban task on a substrate board (NOT a file in `0-INBOX/`)**. The operator dispositions each finding as ACCEPT / REJECT / DEFER within a fixed 24h window; ACCEPTED findings spawn child tickets. This document supersedes v1. Read this one; archive v1 to the historical notes section.

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

## Part 2: The 4 rules of v2

These four rules replace the v1 "three fields" pattern. They are the canonical retro contract; any retro that violates one of them is malformed.

### Rule 1 — A different agent runs the retro

The agent that ran the flow does **not** run the retro on itself. Three options, with trade-offs:

| Option | Profile | Pro | Con |
|---|---|---|---|
| **Verifier** | The agent that verified the flow's output (per `methodology/04a-decide-work-graph.md`) | Already has the context, lowest ceremony, no new profile to maintain | May be biased toward "the work is fine" since they signed off on it |
| **Council** | The multi-perspective review (per `methodology/07-council-methodology.md`, forthcoming) | Independent perspective, surfaces blind spots a single writer misses | More ceremony; council is heavier than most flows need |
| **Coach** | A new dedicated agent focused on flow-level learning | Focused scope, no conflict of interest | Yet another agent profile to build, maintain, and keep consistent with the rest of the fleet |

**Recommendation.** Start with the **verifier** as the default retro writer. The verifier is already in the flow's lifecycle; routing the retro through them avoids the ceremony of a council and the overhead of a new profile. Graduate to a coach profile **only when** the volume of retros justifies it — a rough threshold is one full-time coach worth of work per week, which the operator's instance has not yet reached.

**Council is reserved for retros that find something the verifier cannot judge alone** — typically cross-flow patterns, methodology-level findings, or operator-facing decisions that need multiple perspectives. Most retro findings do not need a council; the verifier is enough.

#### What "different agent" means in practice

The retro writer must not be the agent that:

- Drafted the master ticket's body.
- Wrote any of the flow's children.
- Signed off on the verifier step (when the writer is *not* the verifier; if the verifier is the writer, this rule is satisfied because the verifier signed off independently of the executor).

The retro writer may be the verifier *if* the verifier did not also execute a child. In practice this is the common case: the verifier runs at the end, after all children, and the verifier has not been the executor.

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
- **Failed / blocked / aborted runs** — the crash/block/abort is itself the lesson; record it in the kanban event log and the post-mortem (per `methodology/05-strike-rules.md`). A retro on a non-`done` run is a post-mortem, not a retro; the two are different artifacts.
- **Council verdicts that did not spawn a flow** — see Open Question Q2.

#### Why this rule exists

The retro is a **learning event** and the master is the **scope of the learning**. Without a master, the "flow" is implicit and the retro has no clear subject. A retro without a subject becomes a wish list — the writer invents scope, the operator reads prose without context, and the findings do not land where they should.

Crons are the canonical example. A cron fires every 10 minutes; if each tick were a retro, the operator would read 144 retros a day. The cron's feedback is captured in the iteration loop's `feedback` element (an audit-line + a state adjustment), not in a flow-level retro.

### Rule 4 — The retro is a kanban task (not a file)

The retro is **a task on the kanban board**, filed when the master ticket transitions to `done`. The retro task's body carries the 6-axis assessment + structured findings. **No separate `0-INBOX/retro-*.md` file is written.** The retro file pattern (per v1 and the prior v2 draft) is superseded by this rule.

#### Why a task, not a file

The retro has three durable obligations: (1) be searchable by the operator, (2) be amendable within the window, (3) produce follow-up tickets that the next iteration consumes. A file in `0-INBOX/` satisfies (1) only weakly (no first-class search index) and forces ad-hoc mechanisms for (2) and (3). A kanban task satisfies all three:

- (1) Search — the task lives on a substrate board; it has `assignee`, `block_kind`, `status`, `priority`, `parents`, `children`, `comments`, `events` — all queryable.
- (2) Amend — the operator amends the body via edits; the kanban DB records the edit events. No `git diff` ceremony.
- (3) Follow-ups — accepted findings become child tickets via `kanban_create(parents=[retro_task_id])`. The parent edge is recorded in the kanban DB, not inferred from prose.

#### What goes in the retro task body

The retro task body is a structured template. The writer fills the fields; the operator reads them. The template:

```markdown
# Retro: <flow title> (master t_<id>)

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

## Operator review window
- Opens: <ISO when retro transitions to `open`>
- Closes: <ISO + 24h, or the operator-amended timestamp>
- Default window length: 24 hours (1 day)
- Operator actions during window: ACCEPT / REJECT / DEFER per finding
```

#### The accept / reject / defer flow

When the retro transitions to `open` (writer publishes by completing the draft task), the operator has the window to review each proposed finding. Three valid dispositions:

| Disposition | Operator signal | Retro's response | Follow-up action |
|---|---|---|---|
| **ACCEPT** | Operator edits the finding's `Disposition:` to `ACCEPTED` and adds a `Notes:` line stating the action | The finding is marked accepted in the retro body | A child ticket is filed via `kanban_create(parents=[retro_task_id], assignee=<right-profile>, body=<one-paragraph spec derived from the finding's proposed corrective action>)`. The child ticket's body references the finding id (`Finding F-N`). |
| **REJECT** | Operator edits the finding's `Disposition:` to `REJECTED` and adds a `Notes:` line stating the reason | The finding is marked rejected in the retro body | No child ticket is filed. The retro's audit-line records `finding-rejected: F-N by operator at <ISO> — <reason>`. |
| **DEFER** | Operator edits the finding's `Disposition:` to `DEFERRED — <future date or condition>` | The finding is marked deferred in the retro body | No child ticket is filed now. A `deferred_findings.md` index entry is created at the substrate board's inbox, with `finding_ref: F-N`, `deferred_to: <date/condition>`, `retro_ref: t_<id>`. |

The operator's disposition for a finding is **per-finding, not all-or-nothing**. A retro with 5 findings may have 3 accepted, 1 rejected, 1 deferred. The retro closes only after every finding has a disposition.

#### Closing the retro

The retro task transitions from `open` to `applied` when:

1. Every proposed finding has a disposition (ACCEPTED / REJECTED / DEFERRED).
2. For each ACCEPTED finding, a child ticket exists in the kanban DB (`kanban_create` returned an id; `kanban_show` confirms the child row).
3. The retro's audit-line is appended: `retro-applied: <master-id> at <ISO> — <N accepted, N rejected, N deferred>; child tickets: <id-list>`.

The operator (or a verifier-gate, per `methodology/05-strike-rules.md` strike-13) verifies the closure conditions before the retro task transitions to `done`. The retro task is closed via `kanban_complete` once all child tickets are filed.

#### What the executor MUST NOT do

- Edit the retro task body after publishing. The body is mutable only by the operator (within the window) or by an explicit amendment comment.
- Pre-emptively propose findings in the master ticket body or in flow comments. The writer's evidence base is the run, not the executor's pre-narrative.
- File the master ticket `done` faster than the writer can publish the retro. The master's done-gate (per `methodology/04c-decide-master-ticket.md`) includes the retro's transition to `open`.

### Rule 5 — Structured findings: observed issue + proposed corrective action

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

- **A destination.** The artifact (doc, skill, flow, profile, strike-rule file) that the corrective action will modify.
- **The change.** What will be added, removed, or edited. Prefer concrete edits ("add a 7th field to the flow spec") over aspirational goals ("make flows more rigorous").
- **A verifier check** when possible. How the operator or verifier will confirm the corrective action took effect. "Verify with the verifier that the master's body includes a Sources section" is a verifier check; "should improve" is not.

#### Anti-patterns for findings

- **"We should improve communication."** No destination, no change, no verifier check. Drop it.
- **"The flow was too long."** No destination. Drop it or replace with "add a 7th gate to compress the flow at `methodology/04d-decide-flow-spec.md` §3."
- **"The verifier caught it."** Not a finding; that's the system working as designed.
- **"The agent was slow."** No evidence. Replace with a delta: "the verifier node took 4.2x the expected runtime; see run-log t_X.verifier."
- **One big finding with ten observations.** Split it. One finding per axis per issue. If ten observations cluster, file ten findings with cross-references.

---

## Part 3: The retro lifecycle (v2)

The retro is stateful. The lifecycle is **expressed as kanban task status transitions**, because per Rule 4 the retro IS a kanban task — not a file. v2 retains v1's 4-state lifecycle with three adjustments:

```
┌──────────┐  master → done   ┌─────────────┐
│ (none)   │ ───────────────▶ │  drafting   │  (different agent fills the body template from Rule 4)
└──────────┘                  └──────┬──────┘
                                       │  writer transitions to ready → running → done (publish)
                                       ▼
                                ┌─────────────┐
                                │  open       │  (operator has 24h window to amend disposition per finding)
                                └──────┬──────┘
                                       │  window closes OR operator dispositions all findings
                                       ▼
                                ┌─────────────┐
                                │  applied    │  (findings dispositioned; child tickets filed for ACCEPTED)
                                └──────┬──────┘
                                       │  verifier-gate confirms closure conditions
                                       ▼
                                ┌─────────────┐
                                │  done       │  (retro closed; child tickets become the next iteration's work)
                                └─────────────┘
```

Three v2 adjustments:

1. **Trigger.** `drafting` starts when the master ticket transitions to `done`, not when a node completes. The writer waits for the whole flow to land before drafting. The retro task is filed with `parents=[master_ticket_id]` so the parent's `done` transition auto-promotes the retro to `ready`.
2. **Window length.** v1 used 24h fixed. The initial v2 draft used the "flow's natural review window" (24–72h+). **The operator's clarification on 2026-08-06 (per `t_f0cd340b`) restores the fixed 24h window.** Reasoning: predictable windows are easier to reason about; ad-hoc windows invite the operator to forget a retro mid-vacation. The operator may amend the window length per retro (e.g. for a release-grade flow), but the default is 24h.
3. **Closure gating.** `applied → done` is gated by a verifier-check, per `methodology/05-strike-rules.md` strike-13 (the verifier-gate pattern). The verifier confirms that every finding has a disposition and that every ACCEPTED finding has a child ticket in the kanban DB.

The other states (`open`, `applied`) and their transitions are unchanged from v1. The auto-apply step at `applied` is described in Part 4.

### What "the writer is different" means in the lifecycle

The writer's profile is recorded in the retro task body's `Writer profile:` field. This is mandatory; without it, the retro is malformed. The verifier-gate at `applied` checks the writer's profile against Rule 1 before the retro transitions to `done`.

A retro whose writer was the executor is malformed. The verifier-gate blocks the transition; a different writer is assigned; the retro is re-published.

### The silent-on-no-action discipline

The retro cron is silent when there is no retro to surface. A master ticket that completed 1 hour ago and the retro task is still in `drafting` is NOT a surface event — the retro cron waits until the retro transitions to `open` (writer published), then surfaces the window. The cron's job is to file the retro task when the master transitions to `done`, to remind the operator of an open retro nearing its 24h close, and to fire the auto-apply at the window's close; the cron's job is NOT to nag the writer. The writer is bound by the flow's done-gate plus the writer's own latency budget.

### Amendment 2 (2026-08-06) — Window length restored to fixed 24h

The initial v2 draft proposed the "flow's natural review window" (24–72h for typical flows, 1 week for releases). The operator's clarification on 2026-08-06 (per `t_f0cd340b`) restores the fixed 24h window. The window is **24 hours from the retro's `open` transition**. The operator may amend per retro (e.g. extend to 1 week for a release), but the default is 24h and the amendment must be in the retro task body.

---

## Part 4: The disposition step (what happens to findings at `applied`)

When the window closes (24h after `open`, or sooner if the operator dispositions all findings), the disposition step runs. **The disposition step is operator-driven, not cron-driven.** The operator has dispositioned each finding in the retro task body during the window; at `applied`, the retro task body carries the operator's decisions. The auto-apply step then files child tickets for ACCEPTED findings.

For each ACCEPTED finding, a follow-up ticket is filed. The mapping:

| Finding destination | Follow-up board | Parent edge |
|---|---|---|
| Methodology doc change (`methodology/*`) | work-graph substrate board | parent = the methodology doc + the retro task id |
| Skill change (skill prompt, skill content) | agent-profiles board | parent = the skill + the retro task id |
| Flow-spec change (`methodology/04d-decide-flow-spec.md`) | work-graph substrate board | parent = the flow-spec doc + the retro task id |
| Strike-rule change | platform-config board | parent = the relevant strike-rule file + the retro task id |
| Profile / agent change | agent-profiles board | parent = the profile + the retro task id |
| Anything else | default-routing board | parent = the master ticket that ran + the retro task id |

These board assignments match `methodology/04b-decide-board-routing.md`. Each follow-up ticket's body must reference the finding id (`Finding F-N`) and the retro task id (`t_<id>`) so the work can be traced back to the originating flow. The retro task remains `applied` until the verifier-gate confirms that every ACCEPTED finding has a child ticket; then the retro transitions to `done`.

### Vetoes and amendments within the window

Within the 24h window, the operator has three valid actions per finding (per Rule 4):

1. **ACCEPT** — operator edits the finding's `Disposition:` to `ACCEPTED`. The disposition step files a child ticket per the table above.
2. **REJECT** — operator edits the finding's `Disposition:` to `REJECTED` and adds a `Notes:` line stating the reason. No child ticket is filed. The audit-line records `finding-rejected: F-N by operator at <ISO> — <reason>`.
3. **DEFER** — operator edits the finding's `Disposition:` to `DEFERRED — <future date or condition>`. No child ticket is filed now. The deferred-findings index entry is created.

### What if every finding was rejected or deferred

The retro still closes via `applied → done`. The audit-line reads: `retro-closed-with-no-action: all findings rejected or deferred by operator within window`. That is a valid retro outcome; the system honors the operator's decisions. (Note: this is distinct from "no findings proposed" — see next section.)

### What if there are no findings

The retro closes silently. Audit-line: `retro-closed-no-findings: 6 axes evaluated, no actionable issues proposed`. This is the correct outcome for a clean run — the writer found nothing to change, the system is stable, no ratchet needed. **Clean runs are the most important retro outcomes**; they tell the operator the system is in equilibrium, which is itself a learning signal.

---

## Part 5: Worked example (canonical shape for v2, retro-as-kanban-task)

### The run

Master ticket `t_404: ship the v0.2 methodology release`. Flow has 6 children:

- 2 doc-producing agent nodes (extend 04a and 04b)
- 2 code-producing agent nodes (add a small CLI tool + smoke test)
- 1 verifier agent node (run all smoke tests)
- 1 human node (operator LGTM on the release)

All 6 children complete over 4 days. Verifier passes; operator LGTMs; master transitions to `done`.

### The retro task

The retro cron files `t_410: Retro for t_404` on the work-graph substrate board with `parents=[t_404]`, `assignee=verifier`. The retro transitions from `drafting` to `ready` as soon as `t_404` reaches `done`.

**Writer profile:** verifier (per Rule 1; the verifier did not execute any children, only verified).
**Window:** opens 2026-08-10T15:00Z, closes 2026-08-11T15:00Z (24h, per Rule 4 / Part 3).

**Body excerpt (using the Rule 4 template):**

```markdown
# Retro: ship the v0.2 methodology release (master t_404)

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
- **Disposition:** ACCEPTED
- **Disposition set:** 2026-08-10T18:00Z
- **Notes:** Operator accepted; child ticket filed against agent-profiles board.

### Finding F-2
- **Axis:** d (Flow defined optimally)
- **Observed issue:** The flow spec for t_404 had the two code-producing nodes running sequentially with a `wait_for` edge between them. Investigation showed the shared test-fixture was never actually shared (each node ran in its own scratch workspace). The sequential edge cost ~6 hours of wall-clock time.
- **Proposed corrective action:** Update `methodology/04d-decide-flow-spec.md` Part 3 to add a default-parallelism rule: code-producing nodes without a documented shared fixture should default to parallel. Verify with the verifier that the next flow spec's parallel edges are correctly inferred.
- **Disposition:** DEFERRED — 2026-09-01 (operator wants to ship v0.2 release first)
- **Disposition set:** 2026-08-10T19:00Z
- **Notes:** Deferred to next retro for v0.3 prep; deferred_findings.md entry created.

## Operator review window
- Opens: 2026-08-10T15:00Z
- Closes: 2026-08-11T15:00Z
- Default window length: 24 hours
```

The disposition step at the window's close (or sooner, since the operator dispositioned both findings within 4h) files one child ticket:

- `t_411` (agent-profiles board, parents: skill `code-producing-agent-defaults` + retro `t_410`, finding ref: F-1) — skill change.

`F-2` is deferred; a `deferred_findings.md` entry is created at the work-graph substrate board's inbox referencing `finding_ref: F-2, deferred_to: 2026-09-01, retro_ref: t_410`.

The retro task transitions to `applied` when the verifier-gate confirms `t_411` exists. The retro task then transitions to `done`. The audit-line is appended: `retro-applied: t_404 at 2026-08-11T15:00Z — 1 accepted, 0 rejected, 1 deferred; child tickets: t_411`.

The next release (`t_5XX: ship v0.3`) auto-loads `t_411` (F-1's child) as an input. The CLI tool ships with the right flags. The retro's findings land *as work, not as aspirations*. The system ratchets forward.

### What the operator reads

A single kanban task. The body has the structured template (Rule 4); the comments thread has the disposition discussion; the events table has the lifecycle transitions. The operator can scan axis-by-axis for the flow's health, or finding-by-finding for the actionable changes. Both readings are designed to take under 5 minutes.

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

The retro task has `parents=[master_ticket_id]` and `assignee=<writer-profile>` (default: verifier). The board routing is consistent with `methodology/04b-decide-board-routing.md`. The retro is NOT a free-form note that may live anywhere; it is a structured task on a specific board.

### Not a feedback channel for unrelated thoughts

The retro captures *what was learned from this flow*. If the operator has a thought that's not about this flow — "I want to redesign the agent-fallback chain" — that's a new master ticket, not a retro amendment. Retro amendments stay scoped to the flow that produced them.

### Not a replacement for post-mortems

A retro fires on `done` runs and produces findings about the flow's design and execution. A post-mortem fires on failed / blocked / aborted runs and produces root-cause analysis. The two are different artifacts and live in different places. A flow that aborted mid-run does not get a retro; it gets a post-mortem.

### Not a per-node evaluation

The retro is flow-level. A finding that is purely per-node ("node 3 was slow") is anti-pattern unless the slowness has flow-level implications ("node 3's slowness caused node 4 to start late, which compressed the verifier's window"). Per-node observations without flow-level implications belong in the per-node event log, not in the retro.

---

## Part 7: Diff from v1 (what was preserved, what was replaced)

### Preserved from v1

- The 4-state lifecycle (none → drafting → open → applied). [v2-amendment-2 adds `done` as the verifier-gated terminal state.]
- The 24h human-feedback window (Amendment 2 restores v1's fixed 24h).
- The board routing for follow-up tickets (work-graph substrate / agent-profiles / platform-config / default-routing).
- The operator's three valid actions in the window (amend / veto / no-response). [v2-Rule-4 re-expresses these as ACCEPT / REJECT / DEFER per finding.]
- The retro's anti-pattern list (no silent application, no per-node retro, no indefinite window, no meeting-style retro).

### Replaced from v1

- **Scope:** per-node → **per-flow**.
- **Writer:** executor → **different agent** (verifier default).
- **Trigger eligibility:** every graph execution → **master-ticket flows only**.
- **Finding shape:** 3 freeform fields → **structured findings** (observed issue + proposed corrective action, evaluated on 6 axes).
- **Window length:** v1 was 24h; initial v2 was "flow's natural review window"; **Amendment 2 restores fixed 24h**.

### Amendment 1 changes (2026-08-06) — what initial-v2 changed that this amendment supersedes

- **Artifact format:** initial v2 published the retro as a **file** (vault note + ticket comment). **Amendment 1 supersedes this:** the retro is a **kanban task** on a substrate board, with the 6-axis assessment + findings in the task body. The retro file pattern is no longer used.
- **Operator's window actions:** initial v2 said "amend / veto / no response." **Amendment 1 re-expresses as per-finding ACCEPT / REJECT / DEFER.** The disposition lives in the retro task body, not in a comment.
- **Auto-apply:** initial v2 had a cron-driven auto-apply step at window-close. **Amendment 1 makes the disposition operator-driven** (the operator dispositioned each finding during the window; the verifier-gate confirms at `applied`). The cron no longer auto-files follow-up tickets.

### Removed from v1

- The "What worked" freeform field (replaced by axis assessments; positive observations live inside the assessment).
- The optional "What surprised us" 4th field (replaced by the 6 axes; surprise is captured as a partial or fail on the relevant axis).
- The per-node triggering rule (no longer applicable).

If you find yourself wanting v1's per-node retro or its 3-freeform-fields shape, you are looking for a post-mortem or a flow-level audit, not a retro. Those are different artifacts with different protocols.

---

## See also

- `methodology/06-iteration-loop.md` — the iteration loop primitive that the retro wraps around; the retro is the *post-execution* step that closes the loop after one full iteration. The retro is also the `feedback` element of the iteration loop, raised from per-tick (cron) to per-flow (retro).
- `methodology/04c-decide-master-ticket.md` — master tickets are the scope of the retro. The retro fires on a master's `done` transition.
- `methodology/04a-decide-work-graph.md` — work-graph states (`ready`, `running`, `done`); the retro fires on the `done` transition of a master.
- `methodology/04d-decide-flow-spec.md` — the 6-field flow spec; the retro's findings on axis d (flow defined optimally) target this doc.
- `methodology/05-strike-rules.md` — strike rules often originate from retro findings on axis c (skills adequate) or axis f (right agents used).
- `methodology/03b-decide-operator-agent-interaction.md` — the operator-action ticket lifecycle (surface → respond → resume); the retro's window is one instance of this lifecycle.
- `methodology/07-council-methodology.md` (forthcoming) — councils may run retros for cross-flow findings when the verifier is not enough.
- `methodology/06a-decide-retro.md` — v1 of this doc; superseded by v2. Preserved in git history for reference; do not edit.

## What's next

- `methodology/06b-decide-retro-cron-v2.md` (forthcoming) — the cron that files a retro TASK when a master transitions to `done`, manages the 24h window, and fires the verifier-gate at the window's close. The prior v2 cron (which wrote `0-INBOX/retro-*.md` files) is superseded; the v2 cron must be re-implemented to call `kanban_create` instead of writing a file.
- `methodology/07-council-methodology.md` (forthcoming) — when to use a council as the retro writer (cross-flow findings, methodology-level decisions).
- `methodology/06c-decide-coach-profile.md` (forthcoming, conditional) — if the retro volume justifies a dedicated coach profile, this doc defines the profile.
- `references/tool-mapping-guide.md` — how to implement retro-as-kanban-task + 6-axis + structured findings in concrete tools (`kanban_create` for the retro task, body template from Rule 4, verifier-gate at `applied`).

## Anti-patterns to watch for

1. **"The retro is a per-node event."** → No. v2 retros are flow-level. One retro per master, not per child.
2. **"The executor writes the retro."** → No. v2 mandates a different agent. The executor's perspective is biased; the writer's perspective is independent.
3. **"The retro fires on every cron tick."** → No. v2 restricts retros to master-ticket flows. Crons have their own feedback mechanism (the iteration loop's `feedback` element).
4. **"The findings are a wish list."** → No. Each finding has observed evidence (path, ticket, count, delta) and a corrective action with a destination (doc, skill, flow, profile).
5. **"One finding with ten observations."** → No. One finding per axis per issue. Split multi-observation findings into N findings with cross-references.
6. **"The window is fixed at 24h, no exceptions."** → No, but also no. v2 uses fixed 24h by default; the operator may amend per retro (e.g. extend to 1 week for a release). The amendment must be in the retro task body.
7. **"The retro is a meeting."** → No. The retro is a kanban task with a fixed body shape (Rule 4 template). It is not a synchronous gathering and it is NOT a file.
8. **"The retro applies silently to the next run."** → No. The retro's ACCEPTED findings spawn follow-up tickets with parent edges back to the retro task. The next iteration's work is the union of those tickets, not a silent diff.
9. **"The retro on a failed run is fine."** → No. Failed/blocked/aborted runs get post-mortems, not retros. The retro fires only on `done` masters.
10. **"The retro waits indefinitely for the writer."** → No. The writer is bound by the flow's done-gate plus a latency budget (proposed in this doc; the cron-implementing agent owns the exact number). If the writer doesn't publish in time, the cron surfaces the staleness.
11. **"The retro is a file in 0-INBOX/."** → No. The retro is a kanban task on a substrate board (Rule 4). Files in `0-INBOX/` were the v1 pattern and the initial v2 draft's pattern; both are superseded.
12. **"The cron auto-applies unvetoed findings."** → No. The disposition is operator-driven (ACCEPT / REJECT / DEFER per finding, per Rule 4). The cron's role at the window's close is to fire the verifier-gate, not to file follow-up tickets.
13. **"The operator dispositions the retro all-or-nothing."** → No. The disposition is per-finding. A retro with 5 findings may have 3 ACCEPTED, 1 REJECTED, 1 DEFERRED.
14. **"The retro closes when the writer publishes."** → No. The retro closes (`applied → done`) only after every finding has a disposition and every ACCEPTED finding has a child ticket, gated by the verifier-gate (strike-13).

## Touch-points with other methodology docs

This doc touches:

- `methodology/06-iteration-loop.md` — append paragraph: "the iteration loop's `feedback` element is per-tick for crons and per-flow for master-ticket flows. The retro is the per-flow `feedback`. Per Rule 3 of 06a-v2, the retro does not fire for cron-only flows."
- `methodology/04a-decide-work-graph.md` — note in Part 1: "the `done` state transition of a master triggers a retro TASK on the appropriate substrate board (per 06a-v2 Rule 4). The retro task is co-located with the master's `done` event, not separately scheduled. Per Rule 3, retros do not fire on non-master tickets or on non-`done` transitions."
- `methodology/04c-decide-master-ticket.md` — add section: "a master's done-gate includes the retro trigger. The master is `done` only after the retro reaches `open` (i.e., the writer has published). The retro's `applied → done` transition runs in parallel with the master closing — the master does NOT wait for the retro's terminal state, only for the retro's open transition. (This avoids coupling master close to retro close; the retro may stay open for 24h while downstream work proceeds.)"
- `methodology/04b-decide-board-routing.md` — add retro board routing table from Part 6 (alice-framework → alice-framework, skills → agent-resources, hermes → hermes, patchwork → patchwork, msaa → msaa-pipeline, default → default).
- `methodology/04d-decide-flow-spec.md` — extend the 6-field spec with a 7th optional field: `retro_writer` (the profile that runs the retro; default verifier; council for cross-flow findings).
- `methodology/05-strike-rules.md` — add strike-17 candidate: "retro writer is the executor" — same shape as strike-13's verifier-gate pattern; the writer's profile must be checked against the executor's profile at retro `applied → done`. Add strike-18 candidate: "retro is a file in 0-INBOX/" — the durable artifact is the kanban task, not a vault file.
- `methodology/04e-decide-spec-hierarchy.md` — note: a retro finding that proposes a methodology doc change should specify the destination section/Part, not just the doc title. Findings without a destination section are anti-patterns.

## Operator-environment implications

The retro-as-kanban-task pattern (Rule 4) has implications for the operator's existing cron + script infrastructure. Specifically:

**If the operator's environment currently has a `post-graph-retro` cron (or equivalent) that produces retro FILES in `0-INBOX/`, that infrastructure is superseded by the retro-as-kanban-task pattern.** The cron must be re-implemented (per the spec-first flow in `methodology/M-decide-spec-first-flow.md`) to:

1. **File a retro TASK** via `kanban_create(parents=[master_ticket_id], assignee=verifier, board=<substrate board>)` instead of writing a file to `0-INBOX/retro-*.md`.
2. **Use the Rule 4 body template** (the markdown template from Rule 4's "What goes in the retro task body" section) as the retro task's `body` parameter. The template's `Context`, `6-axis assessment`, `Findings`, and `Operator review window` sections are filled by the writer.
3. **Manage the 24h window** (per Amendment 2) instead of the "flow's natural review window" the initial v2 draft proposed.
4. **Fire the verifier-gate** at the window's close (per Part 3's Closure gating and Part 4's disposition step) — the verifier confirms every finding has a disposition and every ACCEPTED finding has a child ticket, before the retro transitions to `done`.

**This is a separate spec.** Per the spec-first flow rule (`methodology/M-decide-spec-first-flow.md`, `strike-16-spec-first-flow`), the doc-writer (this doc's author) does NOT modify the operator's cron or script. The doc is the spec. The operator (or a coder, per the spec-first flow) will file a separate ticket to update the cron + script to call `kanban_create` instead of writing a file.

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

The resolution of Q1, Q2, Q3, Q4 produced doc amendments + audit-line entries; no v3 is required. The doc is canonical as of Amendment 1 + 2 (2026-08-06).

### Future amendment candidates (not currently open questions)

- **Q5.** Should the retro task have a dedicated assignee (a named verifier, e.g., `verifier-1`) or a profile-level assignee (`verifier`)? The current doc says profile-level. A future amendment may switch to named-assignee for accountability.
- **Q6.** When the writer is a council (per Rule 1's option), how are council members recorded in the retro task body — a list of names, or a single `council` profile with member names in the body's `Writer member:` field? The current doc says single profile + member list. A future amendment may switch to a `Council members:` array.

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

## Audit-line

`## [2026-08-06T07:40Z] retro-v2-methodology — methodology/06a-decide-retro-v2.md shipped; 4 rules (different agent / 6 axes / master-only / structured findings), flow-level scope, supersedes v1. Open questions Q1–Q4 await operator answers; v1 preserved in git history.`

`## [2026-08-06T08:00Z] retro-v2-amendment-1+2 — methodology/06a-decide-retro-v2.md amended per t_f0cd340b. Rule 4 added (retro is a kanban task, not a file); prior Rule 4 (structured findings) renumbered to Rule 5. Window length restored to fixed 24h (Amendment 2). Operator disposition is per-finding ACCEPT/REJECT/DEFER (Amendment 1); verifier-gate at applied→done (strike-13). Q1, Q2, Q3, Q4 resolved. Operator-env implication: existing post-graph-retro cron (if any) is superseded; cron-update is a separate ticket (per spec-first flow, strike-16). Doc is canonical as of this amendment.`
