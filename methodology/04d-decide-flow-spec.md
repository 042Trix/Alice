---
id: alice-methodology-04d-decide-flow-spec
created: 2026-08-05T15:45:00Z
updated: 2026-08-05T22:05:20Z
title: "Methodology 04d — Decide the per-flow spec (the 6-field flow spec)"
type: methodology
status: draft
source: alice-framework
tags: [kind:methodology, kind:flow-spec, kind:work-graph, project:alice]
confidence: 0.0
links: ["[[methodology/04a-decide-work-graph.md]]", "[[methodology/04c-decide-master-ticket.md]]", "[[methodology/06-iteration-loop.md]]", "[[methodology/02-decide-skills.md]]", "[[references/tool-mapping-guide.md]]"]
---

# Methodology 04d — Decide the per-flow spec (the 6-field flow spec)

> A flow is a graph of nodes that produces a named outcome. Every flow needs the same six pieces of metadata before it can be filed, dispatched, reviewed, or recovered. This doc defines the six fields, what each one is for, and what it is **not** for.

The 4a-decide-work-graph doc defines the **edges** and **states** of work. This doc defines the **per-flow content** — the metadata that travels with a flow when it spawns children, when a worker reads the brief, and when a reviewer checks the outcome. Without the 6-field spec, a flow is just a node graph with no contract.

The flow spec is per-flow, not per-node. **Node types** (Human, Scripts/Code, Agents) are a separate taxonomy and live in a sibling doc. Per-flow metadata is the contract the flow publishes to its consumers; per-node metadata is the contract each node publishes to its dispatcher. The two are complementary, not interchangeable.

---

## Part 1: Why the 6-field spec exists

A flow without a spec is a flow that nobody can review. The worker who picks it up reads the brief; the reviewer who audits it checks the outcome; the operator who triages it decides what to do next. All three consumers want the same six facts and they want them in the same shape, every time.

### The problem the spec solves

Without a per-flow spec:

- **The worker invents the goal.** Two workers on the same flow produce two different interpretations of "done." One treats the goal as "build the API endpoint"; the other treats it as "build the API endpoint AND the integration test." The integration test never ships; the reviewer can't tell whether it was in scope.
- **The retry policy is implicit.** When the flow fails, the worker either retries forever (silently burning budget) or gives up after one attempt (giving the flow no chance to recover from a transient failure). Both are wrong; neither is documented.
- **The escalation path is undefined.** When the flow encounters a real blocker — missing credentials, a design question, an irreversible action — nobody knows who to ask. The worker guesses; the operator is surprised; the ticket sits in `blocked` until somebody notices.

### What the spec is for

The 6-field flow spec is the contract that makes a flow:

1. **Dispatchable.** A worker who reads the brief can answer "what am I supposed to produce?" without guessing.
2. **Reviewable.** A reviewer can answer "did the worker produce what was asked?" without re-reading the operator's original prompt.
3. **Recoverable.** When the flow fails, the spec says how many times to retry, with what backoff, before escalating.
4. **Escalatable.** When escalation is needed, the spec says who to ask, what to tell them, and what to do if they don't answer.

A flow that has all six fields is a flow that can move through the work graph without a human in the loop on every transition. A flow missing any of the six fields is a flow that requires operator judgment at every step.

### What the spec is NOT for

The 6-field spec is not a substitute for the work-graph (`methodology/04a`) or the master ticket (`methodology/04c`). The work-graph doc says "every unit of work has a state and edges"; the master-ticket doc says "operator-originated requests have a root with a done-gate." The flow spec lives **inside** both of those, defining the contract that travels with the flow when it spawns children.

The spec is also not a skill brief. The skill brief (`methodology/02-decide-skills.md`) is the contract for a reusable procedure; the flow spec is the contract for one execution of a flow that may invoke zero or more skills. They overlap when a flow is a single skill, and diverge when a flow chains skills, agents, and human nodes.

---

## Part 2: The six fields

The six fields, in the order they appear in the spec, are:

1. **Goal** — the named outcome the flow produces.
2. **Inputs** — what the flow consumes to produce the outcome.
3. **Outputs** — what the flow produces, in concrete artifact form.
4. **Success criteria** — how a reviewer knows the outcome is acceptable.
5. **Retry parameters** — how the flow behaves when a node fails.
6. **Escalation process** — what happens when retry is exhausted or the failure is unrecoverable.

The fields are deliberately ordered. **Goal first** because every other field answers a question about the goal. **Inputs and outputs** next because they bound the flow's contract. **Success criteria** before retry because the reviewer needs to know "done" before the worker needs to know "try again." **Retry before escalation** because escalation is the last resort, not the first.

### Field 1: Goal

**What the operator writes:** A one-sentence statement of the named outcome the flow produces, written in language a reviewer can verify. The goal names the artifact, the change, or the state — not the activity.

**Good:** *"Produce a one-page PDF cheat sheet for the 6-field flow spec."*
**Good:** *"Mark the master ticket as done when all children reach done."*
**Bad:** *"Work on the cheat sheet."* (names the activity, not the outcome)
**Bad:** *"Make the flow spec useful."* (verifiability impossible — "useful" is not measurable)

**The goal is one sentence.** If the goal is two sentences, the flow is doing two things. Split it.

**The goal is the outcome, not the work.** The operator writes what should exist when the flow is done, not what the worker should do. The worker derives the activity from the goal; the operator does not pre-specify the activity.

**Anti-pattern — the goal is a restatement of the trigger.** *"When the operator asks for a cheat sheet, produce a cheat sheet."* The trigger belongs in the iteration-loop doc; the goal belongs in the flow spec. Restating the trigger is a sign that the flow is over-coupled to the surface.

**Anti-pattern — the goal contains success criteria.** *"Produce a cheat sheet that covers all six fields and fits on one page."* The "fits on one page" part belongs in success criteria. The goal says what exists; success criteria says what's acceptable about it.

### Field 2: Inputs

**What the operator writes:** A list of the artifacts, state, and external conditions the flow needs before it can start. Each input is named with its type and source. The flow is not allowed to start if any input is missing.

**Good:** *"Inputs: (a) the 6-field spec text at `methodology/04d-decide-flow-spec.md`; (b) one design template at `templates/one-page-pdf.md.template`; (c) operator approval on the draft before PDF export."*
**Bad:** *"Inputs: the spec."* (too vague — which spec? where? in what version?)

**Inputs are explicit, not implicit.** Every input is named with its source path or its external source. The worker reads the spec and knows "I need these three things" without having to discover them.

**Inputs distinguish "must have" from "may have."** A flow that *can* run without a design template but *runs better* with one should mark the design template as optional. The flow spec uses a `required:` / `optional:` tag for each input.

**Anti-pattern — the inputs list is empty.** Every flow has inputs. Even a "pure" cron-style flow consumes the current time, the cron registry, and the operator's standing rules. If the inputs list is empty, the operator has under-specified the flow and the worker will guess what to read.

**Anti-pattern — the inputs list is unbounded.** *"Inputs: anything in the vault."* A worker cannot read the entire vault. The inputs list must name specific files, specific paths, or specific external sources. Unbounded inputs are a sign that the operator has not thought about scope.

### Field 3: Outputs

**What the operator writes:** A list of the artifacts the flow produces, each named with its destination path or external target. Outputs are concrete artifacts — files, ticket transitions, messages, database rows. The worker does not need to infer where the output goes.

**Good:** *"Outputs: (a) `deliverables/flow-spec-cheatsheet.pdf` (the cheat sheet); (b) `kanban_complete` on ticket `t_X` with summary 'cheat sheet shipped'."*
**Bad:** *"Outputs: a cheat sheet."* (no destination — the worker does not know where to write it)

**Outputs are named with their destination, not just their content.** A flow that produces a PDF without naming the destination path produces a PDF that the worker may save anywhere, may not save at all, or may save with a guessed name. The spec names the path.

**Outputs are atomic per node.** When a flow has multiple nodes, each node's output is named separately. The flow's *overall* output is the union of node outputs, in order. The operator does not have to read the node graph to know what comes out.

**Anti-pattern — outputs are activities, not artifacts.** *"Outputs: review the spec, then update the doc."* The output is the updated doc, not the review. The review is an activity that produces the update.

**Anti-pattern — outputs are not verifiable.** *"Outputs: a good cheat sheet."* "Good" is not verifiable. The output must be a concrete artifact the reviewer can read, count, or test.

### Field 4: Success criteria

**What the operator writes:** A list of conditions the reviewer checks to decide whether the flow's outcome is acceptable. Each criterion is a yes/no question or a measurable threshold. The reviewer answers each criterion independently; the flow is acceptable when every criterion is satisfied.

**Good:** *"Success criteria: (a) the PDF is one page (±1 line); (b) all six fields appear with their definitions; (c) the worked example appears; (d) the PDF passes `pdftotext` extraction without error."*
**Bad:** *"Success criteria: the cheat sheet is good."* (not measurable)

**Success criteria are reviewer-facing, not worker-facing.** The criteria describe what the reviewer checks, not what the worker does. The worker uses the criteria to know when to stop; the reviewer uses the criteria to decide whether to accept.

**Success criteria are independent.** A criterion that depends on another criterion is a compound criterion; split it. The reviewer should be able to mark each criterion pass/fail without reading the others.

**Success criteria are bounded.** Each criterion has a pass condition that does not require subjective judgment. *"Looks professional"* is not a success criterion; *"uses the operator-approved font"* is.

**Anti-pattern — success criteria restate the goal.** *"Success criteria: produce a one-page PDF cheat sheet."* That is the goal. Success criteria say what's acceptable about the artifact, not what the artifact is.

**Anti-pattern — success criteria exceed the goal.** *"Success criteria: covers the 6-field spec AND explains why each field exists AND has a worked example AND has anti-patterns."* If the anti-patterns were not in the goal, they are scope creep. Either add them to the goal or remove them from success criteria.

### Field 5: Retry parameters

**What the operator writes:** The retry policy for each node in the flow, expressed as a count, a backoff schedule, and a stopping condition. The retry policy says how many times the dispatcher should re-run a failed node before escalating.

**Good:** *"Retry parameters: agent nodes retry up to 3 times with exponential backoff (1 min, 5 min, 30 min); deterministic script nodes retry up to 2 times with constant 10s backoff; human nodes do not retry — they escalate immediately."*
**Bad:** *"Retry parameters: retry on failure."* (no count, no schedule, no stopping condition)

**Retry parameters are per node-type, not per flow.** Different nodes have different failure modes. A deterministic script that fails on transient I/O should retry quickly with a tight backoff. An LLM agent that fails on a model timeout should retry with longer backoff and a smaller count. The retry policy is a map from node type to retry behavior.

**Retry parameters distinguish transient from permanent failures.** A retry on a `404 Not Found` is wasteful — the resource isn't going to appear in 30 seconds. A retry on a `503 Service Unavailable` is correct — the service might recover. The retry parameters name the failure codes that should retry and the failure codes that should escalate immediately.

**Retry parameters have a hard stop.** No flow retries forever. After N attempts, the dispatcher escalates per the escalation process. The hard stop is mandatory; an unbounded retry is a bug.

**Anti-pattern — one retry policy for all nodes.** *"Retry up to 3 times."* Different nodes fail differently. The retry policy is per-node-type, not blanket.

**Anti-pattern — retry on permanent failures.** *"Retry on any error."* Permanent failures (auth errors, schema mismatches, missing inputs) never recover with a retry. The spec names the failure codes that retry and the ones that escalate.

**Anti-pattern — no retry parameters at all.** Every flow has retry parameters. Even a "no retry, escalate immediately" flow has a parameter — it's `count=0, escalation=on_failure`. A spec with no retry section is incomplete.

### Field 6: Escalation process

**What the operator writes:** The path the flow follows when retry is exhausted or the failure is unrecoverable. The escalation process names the escalation target, the message template, and the timeout for human response.

**Good:** *"Escalation process: (a) on retry exhaustion, file a `kanban_block(kind=needs_input)` on the originating ticket with reason '<summary of what failed>' and reference '<list of failed node ids>'; (b) DM the operator via the chat channel with the ticket id; (c) wait up to 24h for operator response; (d) after 24h, archive the ticket with reason 'escalation timeout'."*
**Bad:** *"Escalation process: ask the operator."* (no target, no message template, no timeout)

**Escalation names the target.** The escalation target is a specific actor — a chat id, a ticket, a webhook, a council seat. *"Ask the operator"* is not a target; *"DM the operator at chat-id-X"* is.

**Escalation names the message.** The escalation message includes the ticket id, the failed node ids, the failure codes, and the recommended action. A reviewer who receives the escalation can act on it without reading the flow's internals.

**Escalation has a timeout.** No escalation waits forever. After N hours (typically 24h for operator-bound escalations, 1h for automated escalations), the escalation times out and the flow archives the originating ticket with a clear reason. The timeout is mandatory; an unbounded wait is a bug.

**Anti-pattern — escalation is the same as retry.** *"Retry 5 times, then escalate."* Escalation is a different action. Retry re-runs the failed node; escalation involves a human (or a higher-tier automated actor) to make a decision. The spec separates the two.

**Anti-pattern — escalation has no message template.** *"DM the operator when retry fails."* The operator receives "retry failed" with no context and has to ask. The spec names what the message contains.

**Anti-pattern — escalation has no timeout.** *"Wait for operator response."* Operator responses are not guaranteed. The spec names the timeout and the action on timeout.

---

## Part 3: Worked example — the 6-field spec for a flow

Below is a complete 6-field flow spec for a representative flow: **"Draft and ship a one-page PDF cheat sheet for the 6-field flow spec."** This flow chains one human node (operator approval) and one agent node (a doc-producing agent produces the cheat sheet; an instance such as `doc-writer` is one possible assignment).

```
FLOW: Draft and ship the 6-field flow spec cheat sheet

GOAL
Produce a one-page PDF cheat sheet that summarizes the 6-field flow spec
(goal, inputs, outputs, success criteria, retry parameters, escalation),
suitable for posting to the project's documentation site.

INPUTS
  required:
    - methodology/04d-decide-flow-spec.md (this doc, the spec being summarized)
    - templates/one-page-pdf.md.template (the layout template)
  optional:
    - assets/alice-logo.png (the logo for the page header)

OUTPUTS
  - deliverables/flow-spec-cheatsheet.pdf (the one-page cheat sheet)
  - kanban_complete on ticket t_X with summary 'cheat sheet shipped' and
    metadata {"artifact": "deliverables/flow-spec-cheatsheet.pdf"}

SUCCESS CRITERIA
  - The PDF is exactly one page (±1 line of overflow tolerated).
  - All six field names appear, in order, with a one-line definition each.
  - The worked example block from Part 3 of 04d appears in compressed form.
  - The PDF passes `pdftotext deliverables/flow-spec-cheatsheet.pdf -` without
    error and the extracted text is non-empty.
  - The PDF uses the operator-approved font (Source Sans Pro 11pt).

RETRY PARAMETERS
  agent nodes (doc-producing agent):
    - max_retries: 3
    - backoff: exponential, 1m / 5m / 30m
    - retry_on: [model_timeout, transient_api_error, dispatch_lost]
    - escalate_on: [auth_error, schema_mismatch, spec_not_found]
  human nodes (operator approval):
    - max_retries: 0   (do not re-ask; one request, one response)
    - escalate_on: [approval_timeout]
  deterministic nodes (PDF export):
    - max_retries: 2
    - backoff: constant, 10s
    - retry_on: [transient_io_error, render_timeout]
    - escalate_on: [template_not_found, font_not_found]

ESCALATION PROCESS
  on retry exhaustion (any node):
    1. kanban_block(kind=needs_input) on the originating ticket with reason
       '<node-id> failed after <N> retries: <last error code>'. Reference the
       node ids and the last 3 error messages.
    2. DM the operator at chat-id-X with the ticket id, the failed node id,
       and the recommended action ("re-dispatch with corrected spec" or
       "archive the flow").
    3. Wait up to 24h for operator response.
    4. On timeout, archive the originating ticket with reason
       'escalation timeout — operator did not respond within 24h'.
```

Notice the structure. Every field has a concrete shape. A worker who reads this spec knows exactly what to produce, where to write it, when to retry, and where to escalate. A reviewer who reads the same spec knows exactly what to check. The operator who receives an escalation has all the context they need to act.

---

## Part 4: Field-by-field anti-pattern summary

For quick reference, the per-field anti-patterns from Part 2:

| Field | Anti-pattern | Why it's wrong |
|---|---|---|
| Goal | Names the activity, not the outcome | Worker invents the outcome; reviewer can't verify |
| Goal | Restates the trigger | Spec is over-coupled to the surface; goal is hidden |
| Goal | Contains success criteria | Two things in one field; success criteria are unfindable |
| Inputs | Empty list | Worker guesses what to read; flow is under-specified |
| Inputs | Unbounded (e.g., "anything in the vault") | Worker reads everything; scope is unbounded |
| Outputs | Names activities, not artifacts | Worker doesn't know what to produce or where |
| Outputs | Not verifiable (e.g., "a good X") | Reviewer has no checkable criterion |
| Success criteria | Restates the goal | Criteria are unfindable; review becomes opinion |
| Success criteria | Exceed the goal | Scope creep; reviewer rejects on unstated requirements |
| Retry | One policy for all nodes | Different nodes fail differently; blanket retry is wrong |
| Retry | Retries on permanent failures | 404 / auth / schema won't recover; retry is wasted |
| Retry | No parameters at all | Spec is incomplete; dispatcher guesses |
| Escalation | Same as retry | Escalation is a human decision, not another attempt |
| Escalation | No message template | Operator gets no context; response is delayed |
| Escalation | No timeout | Unbounded wait; flow hangs forever |

A flow spec that contains any of these anti-patterns is a spec that will produce a worker-vs-reviewer argument at some point. Fix the spec before filing the flow.

---

## Part 5: Where the spec lives

The 6-field spec is stored on the originating ticket (or on the master ticket, when one exists — see `methodology/04c-decide-master-ticket.md`). The spec is the body of the flow's brief; the ticket is the audit trail and the parent link for the children the flow spawns.

When a flow has children, the spec's `outputs` field names the destination path the children write to. The children read the spec from the parent's comment thread (via `kanban_comment`) or from a linked reference doc. The spec is never duplicated in each child; each child reads the parent's spec and acts on the relevant subset.

The spec is not a separate document in `methodology/` — it is an **instance** of this methodology, applied to one flow. The methodology doc (this file) defines the shape; each flow files its own spec inline. The methodology doc does not grow; the instance count grows.

---

## See also

- `methodology/04a-decide-work-graph.md` — the work graph defines the states and edges; the 6-field spec defines the content that travels inside.
- `methodology/04c-decide-master-ticket.md` — operator-originated multi-step requests get a master; the 6-field spec lives on the master and inherits down to children.
- `methodology/06-iteration-loop.md` — the iteration loop (detect → surface → act) is the heartbeat of the cron that runs flows on a schedule. The 6-field spec is the per-flow contract; the iteration loop is the per-cron protocol.
- `methodology/02-decide-skills.md` — skills are reusable procedures; the 6-field spec is the per-execution contract. A flow may invoke zero or more skills; a skill may be invoked by zero or more flows.
- `methodology/03b-decide-operator-agent-interaction.md` — escalation targets a specific operator role (typically the **launcher** for retries and the **governor** for escalations). The escalation field names the role, not a person.
- `references/tool-mapping-guide.md` — the 6-field spec is tool-agnostic; the tool-mapping guide shows how to encode it in your tool (kanban fields, YAML, JSON, etc.).

## What's next

- A future GRAPH ticket covers **node types** (the 3-node taxonomy: Human, Scripts/Code, Agents). Per-node metadata is a separate concept that complements the per-flow spec.
- A future GRAPH ticket covers **exit flows** (3 exit flows per node: success, fail, error) and reconciles them with the 4-state model in `04a`.
- A future HARNESS ticket covers **spec hierarchy** — how the request doc → architecture → impl plan → tests → tickets chain feeds the 6-field spec when a flow is born from a multi-step request.

## Anti-patterns to watch for

1. **"The flow spec is just a goal."** → No. Six fields. Every one is required. A flow with only a goal is a flow with five missing fields; the worker guesses on inputs, outputs, success, retry, and escalation.
2. **"The flow spec is too rigid — flows should be flexible."** → The spec is rigid; the *execution* is flexible. The worker can choose how to satisfy each criterion, but the criteria themselves are fixed by the operator at intake.
3. **"The spec is for the worker; the operator doesn't need to read it."** → The operator writes the spec, the worker executes it, the reviewer audits it. All three consumers read the same six fields. If the operator can't read the spec back to themselves, the spec is wrong.
4. **"I'll add the retry and escalation fields later."** → No. The spec is incomplete without retry and escalation. A flow with no retry parameters retries forever or never; a flow with no escalation process hangs on the first unrecoverable failure. File the full spec or don't file the flow.
5. **"The success criteria are the same as the outputs."** → Outputs are the artifacts the flow produces; success criteria are the conditions the reviewer checks. A flow that produces a PDF (output) is not automatically acceptable; the success criteria say what makes the PDF acceptable.
6. **"The escalation target is 'the operator'."** → 'The operator' is a role, not an escalation target. The spec names the chat id, the ticket, the webhook, or the council seat that receives the escalation. An unbounded "the operator" is the same as no escalation at all.
