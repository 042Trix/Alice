---
id: alice-methodology-04d-decide-flow-derivation
created: 2026-08-05T19:40:00Z
title: "Methodology 04d — Decide how flows are derived"
type: methodology
status: draft
source: alice-framework
tags: [kind:methodology, kind:flow-derivation, project:alice]
confidence: 0.0
links: ["[[methodology/04a-decide-work-graph.md]]", "[[methodology/04b-decide-board-routing.md]]", "[[methodology/04c-decide-master-ticket.md]]"]
---

# Methodology 04d — Decide how flows are derived

> A work graph says what states and relationships exist.
> Flow derivation says who decides the route through that graph, and when.

A **flow** is an ordered pattern of work units, transitions, dependencies,
worker assignments, and gates used to move an outcome from start to finish.

A flow may be:

- **Pre-defined:** the operator specifies the route before execution.
- **Dynamic:** an agent generates the route from the current situation.
- **Hybrid:** the operator fixes the known structure and an agent derives the rest.

This methodology makes that choice explicit.
It does not change how the decomposition agent (e.g. the planner profile) routes work, and it does not refactor any existing flow.

---

## Part 1: Why flow derivation is a separate decision

The work graph in `methodology/04a-decide-work-graph.md` defines:

- the lifecycle states available to a work unit;
- the valid transitions between those states;
- the relationships between work units; and
- the invariants that keep the graph consistent.

Those rules do not determine the sequence of work for a particular outcome.
A graph may permit many valid paths.
Flow derivation chooses one of them.

For example, the graph may permit research, drafting, review, and publication.
It does not by itself say whether every publication must use that exact route,
or whether an agent may omit research for a routine correction.

Treating graph design and flow derivation as the same decision creates ambiguity.
The state machine should remain stable while individual routes vary.

---

## Part 2: The three derivation modes

### Pre-defined flow

A pre-defined flow is written before the work begins.
The operator defines its steps, dependencies, gates, and completion evidence.
Agents execute the flow but do not redesign its essential route at runtime.

Use a pre-defined flow when:

- the work recurs in substantially the same form;
- a missed step creates material risk;
- the sequence is governed by policy, compliance, safety, or quality controls;
- operator review must occur at a known point;
- several workers need a stable contract; or
- the route must be inspectable before execution begins.

Its primary strengths are **predictability** and **reviewability**.
Its primary weakness is that assumptions embedded in the route can become stale.

### Dynamic flow

A dynamic flow is generated at runtime from the goal and current context.
An agent inspects the situation, proposes or creates work units, and connects them.
The resulting route still obeys the work-graph invariants from 04a.

Use a dynamic flow when:

- the situation is genuinely novel;
- the necessary steps cannot be known until discovery begins;
- inputs, dependencies, or available capabilities vary materially;
- experimentation is cheaper than specifying every branch in advance; or
- no stable pattern has yet emerged from prior runs.

Its primary strength is **adaptability to novel situations**.
Its primary weakness is increased variance in quality, cost, and auditability.

Dynamic does not mean unconstrained.
The generated flow must name its goal, dependencies, gates, and stop condition.
It must also preserve the graph's state and relationship rules.

### Hybrid flow

A hybrid flow fixes the known route and generates only the uncertain portions.
The operator defines a stable skeleton; an agent expands designated extension points.

Use a hybrid flow when:

- the beginning and end are stable but the middle varies;
- mandatory review gates coexist with exploratory work;
- a recurring pattern has predictable phases but variable subtasks;
- known failure modes require fixed safeguards; or
- dynamic discovery should not be allowed to bypass operator decisions.

Its primary strength is balance:
**pre-defined for known patterns, dynamic for the rest**.
Its primary weakness is boundary ambiguity unless extension points are explicit.

A hybrid flow should mark each segment as one of:

- fixed step;
- generated sub-flow;
- operator gate; or
- conditional branch with a documented trigger.

---

## Part 3: The 3-question routing test

Ask these questions in order whenever a new flow is needed.

### Question 1: Is the route known, repeated, and consequential if skipped?

If **yes**, choose a **pre-defined flow**.
Write the steps and gates before execution.

Signals include:

- the flow has run successfully several times;
- a checklist already exists;
- external rules constrain the order; or
- omission of one step would be costly or irreversible.

### Question 2: Is the route unknowable until the current context is inspected?

If **yes**, choose a **dynamic flow**.
Require the generating agent to expose the resulting route and evidence contract.

Signals include:

- this is a first-of-kind outcome;
- the work is diagnostic or exploratory;
- dependencies are discovered only through execution; or
- multiple routes are plausible and cheap to test.

### Question 3: Are some steps mandatory while other steps depend on discovery?

If **yes**, choose a **hybrid flow**.
Pre-define the mandatory spine and label the dynamic extension points.

If none of the questions yields a clear answer, default to hybrid.
A small fixed spine provides safety without pretending the whole route is known.

The test can be summarized as:

```text
Known + repeated + consequential?        -> pre-defined
Unknown until context is inspected?      -> dynamic
Stable spine + uncertain branches?       -> hybrid
Still unclear?                           -> hybrid, then revise from evidence
```

---

## Part 4: Trade-offs

| Mode | Main benefit | Main cost | Best fit |
|---|---|---|---|
| Pre-defined | Predictable and reviewable | Rigid when assumptions change | Repeated, governed, high-risk work |
| Dynamic | Adaptive; handles novel situations | Variable and harder to review in advance | Discovery and first-of-kind work |
| Hybrid | Stable safeguards plus adaptation | Boundaries require careful definition | Recurring work with variable branches |

### Reviewability

Pre-defined flows can be reviewed before any ticket is dispatched.
Dynamic flows are reviewed through their generated artifact and event history.
Hybrid flows require both reviews: the fixed skeleton and generated branches.

### Maintenance

Pre-defined flows require explicit versioning when the process changes.
Dynamic flows require evaluation criteria so agents do not improvise invisibly.
Hybrid flows require clear ownership of both the skeleton and extension rules.

### Operator load

A good pre-defined flow reduces repeated operator decisions.
A good dynamic flow prevents the operator from specifying an unknowable route.
A good hybrid flow reserves the operator for gates with real consequence.

---

## Part 5: Minimum contract for every flow

Regardless of derivation mode, every flow must identify:

1. **Outcome:** what becomes true when the flow succeeds.
2. **Entry condition:** what must be true before it starts.
3. **Work units:** the known steps or the rule for generating them.
4. **Relationships:** parent/child and blocking edges.
5. **Gates:** decisions that cannot be delegated implicitly.
6. **Evidence:** artifacts or verifier results required for completion.
7. **Stop condition:** the observable point at which the flow ends.
8. **Failure route:** how blocked or invalid work returns to a safe state.

A dynamic flow satisfies this contract after generation, not by exemption.
If an agent cannot state the generated route, it has produced improvisation,
not a durable flow.

---

## Part 6: Reconciliation with the work graph (04a)

`04a-decide-work-graph.md` owns the **state machine and graph invariants**.
This document owns the **origin of a route through that graph**.

The division is:

- 04a defines which states and edges are legal.
- 04d decides whether the operator or an agent selects a particular route.
- A generated route may add nodes and legal edges.
- A generated route may not invent illegal states or bypass required transitions.
- A pre-defined route may constrain the graph more narrowly than 04a.
- No route may weaken 04a's audit and evidence requirements.

The graph is the grammar.
The flow is a sentence written using that grammar.
Pre-defined and dynamic flows differ in authorship, not in graph validity.

Board-routing in `04b-decide-board-routing.md` is adjacent but separate.
Board-routing decides where each work unit belongs by domain.
Flow derivation decides how the units and their sequence are produced.
A dynamic flow may derive a board destination, but it must still obey 04b's rules.

---

## Part 7: Anti-patterns

### Anti-pattern 1: Dynamically generate every flow

An agent that dynamically generates every flow is fragile.
It repeatedly rediscovers known procedures, varies safeguards between runs,
and makes failures difficult to compare.

Fix: promote repeated, successful routes into pre-defined flow skeletons.
Keep only genuinely variable sections dynamic.

### Anti-pattern 2: Pre-define every flow

An operator that pre-defines every flow is rigid.
The operator becomes the planning bottleneck and encodes guesses as requirements.
Novel work stalls whenever reality differs from the written route.

Fix: designate dynamic extension points or allow a bounded dynamic flow.

### Anti-pattern 3: Call a flow hybrid without marking boundaries

"Use judgment where needed" is not a hybrid design.
It hides which steps are mandatory and which may be generated.

Fix: label fixed steps, generated sub-flows, gates, and branch triggers.

### Anti-pattern 4: Let generation bypass operator gates

Runtime adaptation is not permission to infer approval for consequential actions.
Approval of a plan is not approval to deploy, publish, purchase, or send.

Fix: preserve operator gates as fixed nodes in every generated route.

### Anti-pattern 5: Freeze a proven dynamic flow forever

A dynamic route that repeats successfully is evidence of a stable pattern.
Generating it from scratch forever wastes effort and increases variance.

Fix: review repeated routes and promote the stable spine to a versioned template.

---

## Part 8: Evolution between modes

Flow modes are not permanent classifications.
They should change as evidence accumulates.

A common lifecycle is:

1. Start dynamic for novel work.
2. Compare several generated routes and outcomes.
3. Extract the stable steps and failure safeguards.
4. Adopt a hybrid flow with explicit extension points.
5. Promote to pre-defined only if variability is no longer valuable.
6. Return a stale pre-defined flow to hybrid when new conditions appear.

Do not promote based on one successful run.
Promote when the repeated structure is clear and the fixed route reduces risk.

---

## Decision record template

```text
Flow name:
Outcome:
Derivation mode: pre-defined | dynamic | hybrid
Why this mode:
Fixed steps:
Dynamic extension points:
Operator gates:
Evidence required:
Stop condition:
Review date or promotion trigger:
Related work-graph rules (04a):
Related board-routing rules (04b):
```

The decision record makes flow authorship inspectable.
It also gives future reviewers enough evidence to change the derivation mode.

---

## Final rule

Pre-define what is known, repeated, and costly to omit.
Generate what cannot be known until runtime.
Use hybrid flows when stable safeguards and adaptive discovery must coexist.

Every route—regardless of who derives it—must remain a valid, auditable path
through the work graph defined in 04a.
