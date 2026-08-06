---
id: alice-methodology-04a-decide-work-graph
created: 2026-08-04T12:30:00Z
updated: 2026-08-05T23:25:00Z
title: "Methodology 04a — Decide your work graph (stateful work + relationships)"
type: methodology
status: draft
source: alice-framework
tags: [kind:methodology, kind:work-graph, kind:node-types, project:alice]
confidence: 0.0
links: ["[[methodology/04-decide-crons.md]]", "[[methodology/05-strike-rules.md]]", "[[methodology/07-council-methodology.md]]", "[[methodology/M-decide-spec-first-flow.md]]", "[[worked-examples/01-solo-founder-skeleton/AGENTS.md]]"]
---

# Methodology 04a — Decide your work graph

> Work is rarely an isolated unit. Most work has a **state** (where it is in its lifecycle) and **relationships** (edges to other work — parent/child, blocks/blocked-by, depends-on, relates-to). The work graph is the substrate that lets the system know "what's blocking what" and "what's the next state for this ticket."

This methodology covers **two things that must be designed together:**
1. **Work tracking** — the lifecycle of a unit of work (the states, the transitions, the valid next states)
2. **Work relationships** — the edges between units (parent/child, blocks, depends, relates)

The methodology is **tool-agnostic**, but the canonical implementation in the worked examples is the **kanban board** (see `references/kanban-lite-disciplines.md`). The methodology says "you need these states and these edges." The tool-mapping guide (`references/tool-mapping-guide.md`) says "here's how to implement them in your tool."

---

## Part 1: Work tracking (stateful work)

### Why work is stateful

A unit of work has a **current state** and **valid next states.** Without states, the system can't answer:
- "What's the worker doing right now?" (the unit's current state)
- "What's the worker allowed to do next?" (the unit's valid next states)
- "Is this unit stuck?" (the unit's state hasn't transitioned in N time)

A unit of work without a state is a unit of work without a lifecycle. The system can store it, but it can't reason about it.

### The minimum state set

Every work-tracking system has a **minimum state set.** Alice recommends this 4-state set:

| State | Meaning | Worker behavior |
|---|---|---|
| `ready` | Created, dependencies met, awaiting dispatch | Worker can claim |
| `running` | Claimed, in progress | Worker is actively working |
| `done` | Work complete, evidence attached | Worker is finished |
| `archived` | Done + aged out, no longer surfaced | Worker has nothing to do |

The 4-state minimum is enough for a small fleet. Add `blocked` and `needs_input` as you need them, but don't start with them.

### The state transition diagram

```
                 ┌─────────┐
                 │ (start) │
                 └────┬────┘
                      │ create
                      ▼
   ┌──────────┐  claim   ┌─────────┐
   │  ready   │ ────────▶│ running │
   └────┬─────┘          └────┬────┘
        │                     │
   block│  dispatch     complete│
        ▼                     ▼
   ┌──────────┐           ┌──────┐
   │ blocked  │           │ done │
   └──────────┘           └──┬───┘
                             │ age out
                             ▼
                         ┌──────────┐
                         │ archived │
                         └──────────┘
```

The transitions are:
- `ready` → `running` (when a worker claims it)
- `running` → `ready` (when the worker gives up — protocol violation, retry exhausted)
- `running` → `done` (when the worker completes the work)
- `running` → `blocked` (when the worker hits a dependency)
- `blocked` → `ready` (when the dependency is resolved)
- `done` → `archived` (when the unit has aged out — typically 30+ days)

### State-machine constraints

A work-tracking system is a **state machine.** Some constraints are mandatory:

1. **Every transition must be explicit.** A worker that moves a unit from `ready` to `done` without going through `running` is a worker that broke the state machine.
2. **Every transition must be auditable.** Every transition appends a `task_event` row with the timestamp, the actor, and the reason.
3. **A worker can only act on units in `running` state.** A worker that edits a `ready` unit (without claiming it first) is bypassing the dispatch system.
4. **A unit can only be in one state at a time.** A `ready`+`blocked` unit is in an inconsistent state. Pick one.

These constraints are non-negotiable. They prevent the "shadow state" anti-pattern where a unit is in one state in the kanban, another state in the worker's head, and the operator has no way to reconcile.

### The "stuck" detection

A unit is **stuck** when:
- It's in `ready` for >30 min (no worker has claimed it)
- It's in `running` for >1h without a heartbeat (worker is alive but not progressing)
- It's in `blocked` for >24h (the dependency hasn't resolved)

Stuck detection is a cron (see `methodology/04-decide-crons.md`). The cron surfaces stuck items to the operator or auto-recovers them.

### Sub-states (when to add them)

The 4-state minimum is enough for a small fleet. Add sub-states when:
- The same `running` state has multiple distinct progress levels (e.g., `running:implementing`, `running:testing`, `running:reviewing`)
- A `done` state has multiple review stages (e.g., `done:awaiting-review`, `done:approved`, `done:published`)
- A `blocked` state has multiple reasons (e.g., `blocked:needs-human`, `blocked:needs-info`, `blocked:dependency`)

**Don't add sub-states prematurely.** A 4-state system with 5 unit types is simpler than a 12-state system with 1 unit type. Add sub-states when the simple system can't answer a real question.

---

## Part 2: Work relationships (the graph)

### Why work has relationships

A unit of work rarely stands alone. It has:
- **Parent** — the work this unit decomposes from
- **Children** — the work that decomposes from this unit
- **Blockers** — work that must complete before this unit can start
- **Related** — work that's relevant but doesn't block or decompose

Without relationships, the system can't answer:
- "What's blocking this?" (which is the unblocker?)
- "What decomposes from this?" (which children are pending?)
- "If I cancel this, what else cancels?" (which descendants are children?)

### The minimum relationship set

Alice recommends this 4-edge set:

| Edge | Meaning | Direction | Use case |
|---|---|---|---|
| `parent_id` | This unit is a child of another unit | child → parent | Decomposition (epic → story → task) |
| `children` | (inverse of `parent_id`) | parent → child | Same as above, traversable in the other direction |
| `blocks` | This unit blocks another unit | blocker → blockee | Dependency (must complete first) |
| `blocked_by` | (inverse of `blocks`) | blockee → blocker | Same as above, traversable in the other direction |

`parent_id` + `children` are the same edge, traversable in either direction. Same with `blocks` + `blocked_by`. **You only need to store one direction**; the other is computed by traversal.

Optional edges (add when you need them):
- `relates_to` — soft link, no blocking or parent/child semantics. Used for "this ticket is in the same area as that ticket but doesn't block it."
- `duplicates` — this ticket is a duplicate of another. Used for de-duplication.
- `supersedes` — this ticket replaces another. Used for deprecation.

### The relationship diagram

```
   ┌──────────────┐
   │  Epic        │
   │  (parent)    │
   └──────┬───────┘
          │ 1 parent → N children
          ▼
   ┌──────────────┐
   │  Story       │
   │  (children)  │
   └──────┬───────┘
          │ 1 story → N tasks
          ▼
   ┌──────────────┐
   │  Task        │
   │  (leaf)     │
   └──────┬───────┘
          │ 1 task → N blockers
          ▼
   ┌──────────────┐
   │  Blocker     │
   │  (other)    │
   └──────────────┘
```

The diagram shows the **direction** of the edges. The **inverse traversal** (e.g., "what blocks this task?") is computed by the system.

### Relationship cardinality

Most relationships are **1:N** (one parent has N children) or **N:M** (one ticket can block N others; one ticket can be blocked by N others). Some are **1:1** (one ticket is the canonical duplicate of one other).

The cardinality is enforced by the system. If a unit has 2 parents, the system rejects (or the second parent is rejected at creation time).

### Relationship invariants

A work-graph system has invariants. Violations of these invariants are bugs:

1. **Acyclic parent/child.** A unit cannot be its own ancestor. If unit A is a parent of B, and B is a parent of C, then C cannot be a parent of A.
2. **Acyclic blocks/blocked_by.** A unit cannot block itself. If A blocks B, and B blocks C, then C cannot block A.
3. **No orphan children.** Every child has a parent. (Some systems allow children without parents; Alice recommends against this.)
4. **No dangling references.** Every `parent_id` references an existing unit. If a unit is deleted, its children's `parent_id` must be updated or the children must be archived.

These invariants are checked at every write (creation, update, deletion). A violation is a 500 error or a hard-fail guard.

### When to add relationships

Add a relationship when:
- The two units have a real, documentable relationship
- The relationship is needed to answer a question (e.g., "what blocks this?")
- The relationship is bounded (1:N, not unbounded N:M)

Don't add a relationship when:
- The two units are loosely related (use `relates_to` if needed)
- The relationship is one-off (use a comment instead)
- The relationship is unbounded (the system can't reason about it)

### Cascading effects

A relationship has a **cascading effect** when an action on one unit affects the related unit. The standard cascading effects are:

| Action | Cascade |
|---|---|
| Parent → `done` | Children become "ready to start" (if their other dependencies are met) |
| Parent → `archived` | Children may also archive (or stay active, depending on policy) |
| Parent → `cancelled` | Children are also cancelled (cascading cancellation) |
| Blocker → `done` | Blockee becomes `ready` (if its other blockers are also done) |
| Blocker → `archived` | Blockee may need to find a new blocker (or be cancelled) |

**Cascading effects are explicit.** The system should not silently cascade. The operator should be able to ask "what cascades from this action?" and get an answer.

Cascading effects are **not automatic by default.** A system that auto-cascades is a system that surprises the operator. Default behavior: the cron surfaces "X cascades to Y" and the operator (or a worker) acts.

---

## Part 3: Bringing it together (the work graph)

A **work graph** is a unit-of-work system that has both:
- States (Part 1)
- Relationships (Part 2)

Without states, the system can't reason about the unit's lifecycle. Without relationships, the system can't reason about the unit's context. Both are needed.

### The work graph as a queryable structure

The work graph is **queryable.** Common queries:

- "What is the state of unit X?" → state lookup
- "What blocks unit X?" → reverse-traversal of `blocks`
- "What is unit X blocking?" → forward-traversal of `blocks`
- "What are the children of unit X?" → forward-traversal of `parent_id`
- "What is the parent of unit X?" → reverse-traversal of `parent_id`
- "What is the sub-graph rooted at unit X?" → recursive traversal
- "What is the critical path from goal to done?" → graph algorithm over `parent_id` + `blocks`

If your system can't answer these queries, it's not a work graph. It's a work list.

### The work graph and the iteration loop

The iteration loop (`methodology/06-iteration-loop.md`) operates on the work graph. The loop's detect step scans the graph for stuck items; the surface step produces a queryable view; the act step takes a graph action (e.g., close a unit, re-dispatch a parent, archive a sub-tree).

A loop that doesn't traverse the graph is a loop that doesn't see the relationships. A "rescue" loop that rescues only the unit, not the children, leaves children orphaned.

### The work graph and the operator priority

The operator is the bottleneck. The work graph's job is to **reduce operator decisions** by:

- Surfacing only the units that need operator input (not all blocked units, just the ones that are blocked-on-operator)
- Cascading cancel/archive actions through the graph automatically (with operator review)
- Surfacing the critical path (so the operator can prioritize)
- Surfacing duplicate units (so the operator can de-duplicate)

A work graph that doesn't reduce operator decisions is a work graph that's not paying for itself.

---

## Worked example: the kanban board (canonical implementation)

The canonical implementation of the work graph is the **kanban board** (see `references/kanban-lite-disciplines.md`). The mapping:

- **States:** `ready`, `running`, `done`, `archived` are status fields in the kanban
- **Sub-states:** `blocked` and `needs_input` are sub-categories of `running` (a ticket is in `running` but blocked, or in `running` but needs input)
- **Parent/child:** `parent_id` and `child_id` fields in the kanban
- **Blockers:** `blocks` and `blocked_by` fields in the kanban
- **Cascading effects:** surfaced by a cron (e.g., "parent done → children ready") and acted on by the operator or a worker

The kanban board is the **worked example** for this methodology. **The methodology is not the kanban board.** Other tools can implement the work graph differently (e.g., linear issues, GitHub issues, Asana tasks), as long as they satisfy the methodology's requirements.

---

## Part 3.5: Canonical graph node order and verifier gates

The work graph describes more than isolated nodes and legal edges. For any multi-phase flow, it must also make the **execution order** and the **verification gate between phases** explicit. A phase that follows another phase is not ready merely because its predecessor says it is done; its predecessor's output must first pass an independent verifier node.

### The canonical order

For a spec-first flow, use this ordered node pattern:

```text
(doc) → (V-1: validate doc) → (environment update) → (V-2: validate update)
```

The labels are conceptual. A tool may represent the phases and verifier steps as tickets, jobs, workflow nodes, or another equivalent structure. The invariant is that each transition into a downstream phase is owned by a distinct verifier node, assigned to an independent verifier role.

The expanded sequence is:

1. **Doc** — produce the specification or canonical design artifact.
2. **V-1: validate doc** — independently check the artifact against its acceptance criteria, internal consistency, documentation conventions, and parity evidence. No environment change occurs here.
3. **Environment update** — a non-doc-writer implementation role changes the configured environment to match the validated specification.
4. **V-2: validate update** — independently check the implementation against the specification, verify side effects, and confirm that the parity evidence now describes the live environment.

A flow may include additional work phases, but the rule remains the same: **insert an independent verifier node between every phase transition that carries a reviewable work product or changes canonical state**. In notation:

```text
(phase A) → (V-A) → (phase B) → (V-B) → (phase C) → (V-C)
```

This is a graph-design rule, not an implementation detail. Parent/child links express membership; dependency edges express readiness; verifier nodes express the evidence gate that permits the next phase to begin. Do not treat a verifier as a comment, a worker's self-review, or an implicit property of `done`.

### Verifier-node contract

Every verifier node carries the same six required metadata fields as other nodes (see Part 5):

| Field | Verifier-node requirement |
|---|---|
| `goal` | Confirm the preceding phase's output satisfies its acceptance criteria. |
| `original_ask` / `context` | Identify the parent flow and the artifact or state being reviewed. |
| `task_specific_info` | Name the exact artifact path, revision, node id, or environment surface to inspect. |
| `process` / `flow` | Inspect the output → compare it to the prior phase contract → record an evidence-based verdict. |
| `skill_version` | Pin the verifier role or review procedure used. |
| `acceptance_criteria` | Provide yes/no checks and the evidence required for a pass. |

The verifier is an **Agent node** when the review requires judgment, but it is not the same agent that produced the preceding output. A deterministic check may be a Scripts/Code node within the verifier's process; that does not replace the independent verifier gate when interpretation is required. Operator approval remains a separate Human node only for decisions reserved to the operator.

### Gate behavior

- **Pass:** the verifier records its verdict and the next phase becomes eligible to start.
- **Fail with a known, repairable defect:** the verifier records the defect and routes the preceding phase back for bounded rework; the downstream phase remains ineligible.
- **Fail because the contract or assumption is wrong:** the verifier records an escalation with evidence; do not silently rewrite the specification to match the implementation.
- **No verifier result:** the downstream phase is not ready, even if the preceding worker marked its own node `done`.

The gate is therefore a dependency edge, not a suggestion. If a system cannot represent the verifier node explicitly, it must represent an equivalent auditable gate with an owner, acceptance criteria, verdict, and evidence. Otherwise the graph has an un-audited transition.

### Why this is mandatory

Without an explicit verifier node, phase transitions become silent or self-reviewed. That creates two forms of drift: an accepted document whose contract was never checked, and an environment update that no independent reviewer compared with the document. The verifier gate preserves the distinction between producing work and accepting work, keeps the operator out of routine review, and leaves a reconstructable evidence trail.

This rule complements, rather than replaces, the state machine and relationship invariants above. The graph still needs explicit states, auditable transitions, acyclic relationships, and valid parent/dependency edges. The verifier order adds the missing **evidence relationship** between sequential phases.

---

## Part 4: Node types (the 3-node-type taxonomy)

A node is an executable element in the work graph. Parts 1-3 covered the *states* and *relationships* of nodes; this part covers the **types** of nodes. Every node has exactly one type, and the type determines what the node can do, what it can receive as input, and what it can produce as output.

The taxonomy is 3 types. The names are fixed by convention and must not be renamed:

1. **Human** — the operator (or a human delegated by the operator)
2. **Scripts/Code** — deterministic executables; same input → same output, no LLM in the loop
3. **Agents** — non-deterministic executables; LLM-driven, may produce different output across runs

These three types are **exhaustive** over Alice's work graph. If a node doesn't fit one of these three types, it's not yet a node — it's still being designed.

### Why 3 types and not more or fewer

The 3-type split is anchored in **determinism**. The fundamental question the graph has to answer for every node is: *"if I run this node twice with the same input, will the output be the same?"*

- **Yes → Scripts/Code.** Determinism is a feature; you can rely on the output.
- **No → Agents.** Non-determinism is a feature; the LLM may surprise you, and that's the point.
- **Neither → Human.** The output depends on a human's judgment, which is neither deterministic nor model-sampled.

A taxonomy with 4 types (e.g. adding "Service" or "External API" as a fourth) over-partitions. A Service or External API is either a deterministic Scripts/Code wrapper around an API call, or it's an Agent if its responses are LLM-mediated. There is no fourth category that earns its keep.

A taxonomy with 2 types (just Scripts/Code vs Agents) drops the Human type. That's wrong: the operator's judgment *is* a node type in Alice's graph (LGTM gates, approval steps, escalation destinations), and conflating the operator with "a special agent" erases the asymmetry that operators are not LLM-sampled and not idempotent.

### Type 1: Human

**Definition.** A Human node is a unit of work whose output is produced by a human operator (or a human explicitly delegated by the operator). The output is a **decision** — approval, rejection, override, scope change, or judgment call that no deterministic rule can make.

**What the operator uses it for.** Approval gates, scope decisions, escalation destinations, judgment calls that no agent should make alone. Human nodes are **the system's escape hatch** when Scripts/Code and Agents cannot answer a question on their own.

**Example.**

```
[Human]   operator LGTM on the spec at <path>
          ↓
[Agent]   decomposition agent builds the work graph
```

The operator LGTMs the spec (Human node). Only after the LGTM does a decomposition-role Agent dispatch downstream work. The Human node's output is "approve / reject / change-scope" — a decision no agent should be allowed to make on the operator's behalf.

*(Instance note: in the operator's specific environment, the decomposition-agent role is filled by a named decomposition profile — one example being `planner`. The methodology describes the role generically; the operator's profile is one concrete implementation, not the canonical name.)*

**Anti-pattern — what Human is NOT for.**

- **NOT for routine review.** "Did the worker produce a passing test" is a Scripts/Code verification. Don't make the operator check.
- **NOT for delegation chains.** A Human node that says "operator delegates to Maria, who delegates to Dorothy" is not one Human node — it's three Human nodes, each with its own decision.
- **NOT for "ask the operator to clarify" as a default.** Per `strike-13`, the verifier-gate pattern (`assignee` + `block_kind` + `status`) must be checked before claiming an operator ask. Defaulting Human for every "needs input" is the over-trigger pattern that strikes 1-3 corrected.
- **NOT for work that can be scripted.** If the rule "always LGTM on a spec" can be encoded as "spec → reviewer → auto-approve if reviewer passes", it's a Scripts/Code node, not a Human node.

### Type 2: Scripts/Code (deterministic)

**Definition.** A Scripts/Code node is a unit of work whose output is produced by an executable — a shell command, a Python script, a cron job, a database migration, a CI step. Given the same input, the node produces the same output. **No LLM is in the loop.**

**What the operator uses it for.** Routine verification, format conversion, data migration, cron-driven sweeps, deterministic checks that must be reproducible. Scripts/Code nodes are **the system's spine** — they're how Alice keeps Agents and Humans from having to do work a script can do.

**Example.**

```
[Scripts/Code]   run-script: verify kanban.db has no stuck tickets > 24h
                 input: kanban.db path
                 output: {stuck_count: int, stuck_ids: [string]}
```

Same `kanban.db` → same output. The cron runs it every 10 min. The output feeds downstream Agent nodes that decide what to do about stuck tickets. No LLM, no variance.

**Anti-pattern — what Scripts/Code is NOT for.**

- **NOT for any task with an LLM in the loop.** If the script calls `claude` or `gpt-4` and reads the result, the **agent** call is the Agent node, not the script. The Scripts/Code node is the wrapper; the LLM is the Agent.
- **NOT for "I should probably add some LLM-based judgment here."** If the script needs judgment, it must call an Agent node. Don't embed the LLM inside the script — that breaks the deterministic-vs-non-deterministic line.
- **NOT for one-off commands that won't be re-run.** A Scripts/Code node is meant to be reusable. A command run once and never again is a `bash` invocation, not a node. Promote to a node when the command becomes a pattern.
- **NOT for state-bearing processes that need audit.** A Scripts/Code node that mutates state without writing to the audit trail (the kanban event log per `methodology/01a-decide-memory.md`) is an unsafe node. Every state mutation must be auditable.

### Type 3: Agents (non-deterministic)

**Definition.** An Agent node is a unit of work whose output is produced by an LLM-driven agent — a worker with a profile, a soul, a model, and tools. Given the same input, the node may produce **different output across runs** because the LLM is non-deterministic.

**What the operator uses it for.** Open-ended tasks that no script can do — planning, writing, reviewing, summarizing, deciding between ambiguous options. Agent nodes are **the system's judgment layer** — they bridge the gap between deterministic scripts and the operator's intent.

**Example.**

```
[Agent]   dispatch to a decomposition-role profile: decompose this ticket into children
          input: ticket body, body context, board state
          output: child tickets with assignees + parents
```

Same ticket body → different children across runs (the decomposition agent may propose 3 children this run, 4 the next, depending on the LLM's reading). That's the point. The non-determinism is the *feature*, not the bug — the decomposition agent reads context the operator didn't enumerate, and proposes what the operator would have proposed if they had read it.

*(Instance note: in the operator's specific environment, the decomposition-role profile is named `planner`. The methodology describes the role generically; the operator's profile is one concrete implementation, not the canonical name.)*

**Anti-pattern — what Agents is NOT for.**

- **NOT for tasks a script can do.** If the work is "verify the file exists" or "rotate the log file at midnight," it's Scripts/Code. Reaching for an Agent when a script fits is the over-deliberation pattern that drives up token spend and audit noise.
- **NOT for irreversible actions without operator approval.** An Agent node that publishes, deletes, pays, or sends is an Agent that needs a Human LGTM gate first. Per the operator-action-classification rules, irreversible actions are operator-class — never delegated to an Agent.
- **NOT for state changes that must be deterministic.** If the work is "migrate the database," the migration is a Scripts/Code node. Wrapping it in an Agent makes the migration non-reproducible, which is a bug. Agents add judgment, not determinism; they subtract determinism.
- **NOT for "ask the LLM what the answer is."** An Agent node that asks the LLM a question without a worker context, a profile, or a soul is not a node — it's a `delegate_task` call. Promote to a node when the work becomes a pattern with its own context.

### The reconciliation with 07-council-methodology.md

`methodology/07-council-methodology.md` describes a different "3-node-type" idea that overlaps but does **not** match this taxonomy. Reading 07:

- 07 specifies **4 canonical seats** (Strategist, Engineer, Operator, Skeptic), not 3.
- Each seat has a **perspective** (long-term view, implementation view, operator view, failure-mode view).
- Seats **deliberate independently** and produce a verdict; the orchestrator (operator or chair) decides.

The 3-node-type taxonomy in this Part 4 is **not the same as the council-seat taxonomy in 07.** They live at different abstraction levels:

| Dimension | Part 4 (this doc) | 07 |
|---|---|---|
| **What it classifies** | Node types in the work graph | Seats in a council deliberation |
| **Cardinality** | 3 (Human, Scripts/Code, Agents) | 4 minimum (Strategist, Engineer, Operator, Skeptic) |
| **Determinism axis** | Yes — this is the spine of Part 4 | No — seats are perspective axes, not determinism axes |
| **Where it lives** | Every node in the work graph, always | Only when a council is convened (rarely) |
| **Exhaustiveness** | Exhaustive over all nodes | Not exhaustive — councils are convened, not always-on |

A council seat **is itself a node** — but it's an Agent node (per Part 4 Type 3), not a new type. The Operator seat is an Agent that holds the operator's perspective, but the *final verdict* is a Human node (the operator approves or rejects). So a 4-seat council, in graph terms, is 4 Agent nodes (one per seat) feeding into 1 Human node (the verdict). The taxonomy reconciles cleanly; they are not the same enumeration.

**Why keep them separate.** Unifying the two taxonomies would force every node into a council-seat perspective, which is wrong: most nodes (cron sweeps, format conversions, log rotations) have no perspective — they're Scripts/Code. A cron sweep is a Scripts/Code node; it is not "the Operator seat." The two taxonomies answer different questions:

- **Part 4 asks:** "what kind of executable is this node?"
- **07 asks:** "what perspective should this deliberation cover?"

Both questions are real; they don't collapse. A future ticket may add a small cross-reference table (this Part 4's "Type 3: Agents" → 07's "Agent seats"), but the taxonomies stay distinct.

### Worked example: a single ticket as 3 nodes

Consider the master ticket "ship the v0.1 methodology release." Its work graph has 12 child tickets. Most children are Agent nodes (one per agent role — a doc-producing agent, a code-producing agent, a review agent, etc.). A few are Scripts/Code (the kanban-discipline smoke test, the index rebuilder, the `_index/reindex.py` rebuild). And one — the operator LGTM on the spec — is a Human node.

```
[Human]   operator LGTM on the v0.1 spec
            ↓
[Agent]   decomposition-role agent: decompose into children (12 nodes)
            ↓
  ┌─────────┬─────────┬─────────┐
  │         │         │         │
[Agent]  [Agent]  [Agent]  [Scripts/Code]
doc-     code-    reviewer kanban
producing producing                 smoke test
  ↓         ↓         ↓         ↓
[Scripts/Code]                   [Human]
vault_log.py              operator LGTM
append_audit_line         on v0.1 release
```

The graph mixes all three types. Scripts/Code nodes (smoke test, audit-line append) verify; Agent nodes (doc-producing, code-producing, review) produce; the Human node (operator LGTM at start and end) gates. This is the canonical shape: **a work graph is a mix of the three types, not a pure-Agent or pure-Scripts/Code system.**

*(Instance note: in the operator's specific environment, the doc-producing, code-producing, and review roles are filled by named profiles — one example mapping being `doc-writer`, `coder`, and `verifier` respectively. The methodology describes the roles generically; the operator's profile names are one concrete implementation, not the canonical names.)*

### When to add a new type (you don't)

The 3-type taxonomy is **closed.** Do not add a 4th type.

A 4th type is wrong when:

- The candidate is a subtype of Scripts/Code (e.g., "cron" or "service"). Cron is Scripts/Code with a schedule. Service is Scripts/Code with a network. Subtypes don't earn taxonomy seats.
- The candidate is a subtype of Agents (e.g., a "decomposer" agent role or a "reviewer" agent role). The profile is the subtype; the node type stays "Agents." The 3-decide-agents methodology handles profiles.
- The candidate is "External" (e.g., a third-party API call). External is Scripts/Code that calls across the network. Not a new type.
- The candidate is "Time" (a delay or a timer). Time is a Scripts/Code cron, not a node type.

If a real workload can't be expressed in the 3 types, that's a signal the methodology needs a different extension (a new node-property, a new relationship type, a new state) — not a new node type. File a follow-up ticket against `methodology/04a-decide-work-graph.md` and the doc-producing agent (or another method-maintainer profile) will route it.

### See also

- `methodology/04-decide-crons.md` — crons are Scripts/Code nodes with a schedule; this Part 4 explains why they're classified as Scripts/Code, not as their own type.
- `methodology/05-strike-rules.md` — strike rules describe operator corrections. The corrections often shift work from one node type to another (e.g., "this should have been a Scripts/Code verification, not an Agent review").

- `methodology/01a-decide-memory.md` — **the kanban event log is episodic memory (Tier 4).** Every transition is a memory event; the audit trail is the union of these events. Work-graph state is reconstructed from episodic memory.
- `methodology/04b-decide-board-routing.md` — boards are the work-graph substrate; cross-board moves are graph actions.

## Part 5: Required task metadata schema (every node must carry its contract)

Parts 1–4 defined the *scaffolding* of a node — its states, its edges, its type. This Part defines the **content** a node must carry at every point in its lifecycle. Without this content, a node is structurally well-formed but semantically empty: a worker can claim it, but cannot tell what it's supposed to do; a reviewer can audit it, but cannot tell whether it's done.

The rule: **every task carries the same required metadata, in the same shape, regardless of node type.** The metadata travels with the task from `ready` through `done` (or `blocked`); it is read by the worker on claim, by the reviewer on completion, and by the operator on triage. A node that lacks any of the required fields is **under-specified**, not "lean."

### The required fields

A task must carry, at minimum, these six fields. The names are fixed by convention and must not be renamed — the names are the contract between filer, worker, reviewer, and operator.

| # | Field | What it answers | Source |
|---|---|---|---|
| 1 | `goal` | What outcome does this task produce? | The filer (operator or planner) |
| 2 | `original_ask` / `context` | Why does this task exist? What was the operator trying to do when it was filed? | The originating prompt, ticket, or note |
| 3 | `task_specific_info` | What's unique to *this* task — paths, ids, names, artifacts, edge cases — that a worker cannot infer from the goal alone? | The filer |
| 4 | `process` / `flow` | What sequence of steps, sub-tasks, or skill calls does the worker follow? (Either inline or by reference to a named flow.) | The filer or the planner |
| 5 | `skill_version` | What skill(s) does this task invoke, and at what version? (Per `methodology/02-decide-skills.md`.) | The skill registry |
| 6 | `acceptance_criteria` | What conditions must hold for a reviewer to mark this task `done`? | The filer |

The first five describe **how to do the work.** The sixth (`acceptance_criteria`) is the **review contract** — it's what a verifier checks before accepting the task's output, and it's the line the worker stops at. The split matters: a task with only `goal` + `process` is one a worker can start but a reviewer cannot audit.

### Field semantics

**`goal`** is one sentence in the outcome form. It names the artifact, the change, or the state that should exist when the task is `done`. It does **not** name the activity; it does not say "work on X" or "investigate X." A goal that takes two sentences is two tasks; split it. (Same rule as `methodology/04d-decide-flow-spec.md` Field 1.)

**`original_ask` / `context`** is the human-side why. It captures the operator's original prompt, the prior conversation that surfaced the need, the upstream ticket this decomposed from, or the incident that triggered it. A task without `original_ask` is a task the worker must guess at — and "guess at the operator's intent" is the failure mode that produces off-by-one scope errors. The ask is the audit trail's anchor; without it, the task has no history.

**`task_specific_info`** is the per-instance detail — file paths, line numbers, repo slugs, ticket ids, environment variables, prior-decision links — that does not generalize. Two tasks can have the same `goal` and the same `process` and still need different `task_specific_info` because the files they touch are different. The field is the bridge between the generic template and the specific instance.

**`process` / `flow`** is the sequence the worker follows. It may be a numbered list inline, or it may be a reference to a named flow spec (per `methodology/04d-decide-flow-spec.md`). When the process is by reference, the reference includes the flow's version or hash, so the worker knows exactly which process to run. A task with no `process` is a task the worker invents on the fly — and invented processes are not auditable.

**`skill_version`** names the skill(s) the task invokes, at a specific version (commit hash, semver, or registry timestamp). When the task is a script, `skill_version` is the script's git rev. When the task is an agent, `skill_version` is the profile's `soul_version` and the underlying model's pinned revision. The version is mandatory because skills drift — a task that said "use `kanban-discipline v1.0" last month and `kanban-discipline v1.1` this month may behave differently on the same input, and the audit trail needs to know which one ran.

**`acceptance_criteria`** is the reviewer's checklist. Each criterion is a yes/no question or a measurable threshold — same shape as `methodology/04d-decide-flow-spec.md` Field 4. The criteria live on the task; the reviewer reads them on completion; the worker reads them on claim (so they know when to stop). A task without `acceptance_criteria` is a task the reviewer must invent criteria for — and reviewer-invented criteria produce reviewer-vs-worker arguments about what "done" means.

### Anti-pattern — the metadata is implicit

The most common failure mode: the filer writes a goal and a process, files the task, and assumes the worker will discover the rest from context. Symptoms:

- The worker opens the task, asks "what's the original ask?" — and the operator has to re-explain it.
- The reviewer opens the completed task, asks "what was the acceptance criterion?" — and the worker has to argue for the result.
- The audit trail shows the task, but no one can reconstruct *why* it was filed.
- A re-dispatch (after a crash or a retry) loses all the context the operator put into the original prompt.

The fix is structural: every field is required, every field is named, every field is in the same place on every task. The dispatcher (or the filer) refuses to file a task missing any of the six.

### Anti-pattern — the metadata is over-specified

The opposite failure: the filer writes 800 words of context, three pages of process, and an acceptance-criteria list of 40 items. The worker reads the brief, finds the signal in the noise, and produces something that satisfies *some* criteria but not all. The reviewer then audits against the full list, fails the task on items the worker reasonably de-prioritized, and the task re-dispatches.

The fix: **each field is bounded.** `goal` is one sentence; `acceptance_criteria` is a list of 3–7 items; `process` is either a short inline list or a single named-flow reference. The metadata is the **minimum a worker needs to start** and the **minimum a reviewer needs to judge** — not the maximum the filer can write.

### Anti-pattern — the metadata is duplicated

A task that copies its `process` from a skill's body, copies its `acceptance_criteria` from a flow spec, and copies its `original_ask` from a parent ticket is a task whose metadata drifts the moment any source drifts. Three months later, the skill changes; the task still says "follow `skill:run-vault-cleanup`" with the old process inline, and the worker runs the old process against the new skill body.

The fix: **metadata is by reference, not by copy, whenever possible.** `process` references a named flow spec; `acceptance_criteria` references a flow spec's Field 4; `original_ask` references the parent ticket's body. The task carries the references; the canonical copy lives in the source. Drifting a source updates every task that references it.

### Where this Part lives in the larger picture

- **`methodology/04d-decide-flow-spec.md`** — the 6-field flow spec is the per-flow metadata, scoped to the lifetime of one flow. This Part's 6-field task metadata is the per-task metadata, scoped to the lifetime of one node. The fields overlap (goal, inputs, outputs, success criteria map cleanly); the difference is granularity. A flow's `goal` may span many nodes; each node's `goal` is one slice of the flow's.
- **`methodology/02-decide-skills.md`** — the skill brief is the metadata for a reusable procedure. The `skill_version` field references the skill by version; the skill brief is the canonical copy of the procedure the task is using.
- **`methodology/01a-decide-memory.md`** — `original_ask` is a Tier 5 (long-form) memory role: the operator's intent survives across sessions. The audit trail that records *how* the task was dispatched and *what* the worker did is Tier 4 (episodic).
- **`methodology/04c-decide-master-ticket.md`** — the master ticket carries the operator-originated request; each child task inherits `original_ask` from the master and adds `task_specific_info` for its slice.

### See also

- `methodology/04d-decide-flow-spec.md` — the per-flow 6-field spec; this Part is the per-task projection.
- `methodology/M-decide-spec-first-flow.md` — the spec-first flow's explicit verifier nodes; use the canonical order and gate behavior defined in Part 3.5.
- `methodology/02-decide-skills.md` — skills are the procedures the `process` field references.
- `methodology/04c-decide-master-ticket.md` — the master ticket's body is the parent `original_ask` for child tasks.
- `methodology/01a-decide-memory.md` — the `original_ask` field is Tier 5 memory; the kanban event log is Tier 4.

---

## Part 6: Three exit flows per node (success / fail / error)

A node is not done when the worker finishes. A node is done when **all three of its possible terminal outcomes** have a defined next step. The three outcomes are:

1. **Success** — the node completed its `goal`; the worker's output satisfies `acceptance_criteria`. The next step is a state transition to `done` and (typically) parent-unblock or downstream dispatch.
2. **Fail** — the node did not complete its `goal`, but the cause is **known and recoverable.** A retry can succeed: a transient API timeout, a missing input that the parent can supply, a worker that crashed but the brief is still valid. The next step is a retry (per the `process` field's retry parameters) or a `blocked` transition with the reason.
3. **Error** — the node could not complete its `goal`, and the cause is **unknown or unrecoverable.** The brief is wrong, the skill version is broken, the assumption the goal rested on is false. Retrying produces the same error. The next step is an escalation (per the flow spec's `escalation_process`), not a retry.

The distinction between *fail* and *error* is the spine of this Part. **Fail is recoverable; error is not.** Conflating the two produces systems that retry until timeout on bugs that will never recover, and that escalate transient hiccups as if they were design flaws.

### Why three exits, not two

A two-exit model (success / failure) collapses *fail* and *error* into one bucket. The system then has two wrong choices:

- **Retry everything.** A spec-mismatch error retries the same broken spec 5 times and then escalates — burning 30 minutes of worker budget on a non-recoverable bug.
- **Escalate everything.** A transient API timeout escalates to the operator as if the API were permanently broken — pulling the operator into a fix-the-network problem that the worker could have solved by waiting 30 seconds and re-running.

The three-exit split gives the dispatcher three distinct policies:

- **Success** → next state is `done`. No retry. No escalation.
- **Fail** → next state is `retry` (which returns to `ready`) or `blocked` (which waits on a dependency). Bounded retry count; backoff; transient-classification.
- **Error** → next state is `escalate` (which routes to the operator, a council, or a Human node). No retry. The brief is wrong; the worker cannot self-correct.

A node with only a success path and a failure path is a node that cannot tell recoverable from unrecoverable. That is a graph-design bug, not a tool bug. **Any node with fewer than 3 exit flows is incomplete.**

### Reconciliation with the 4-state model

Part 1 defined four states: `ready`, `running`, `done`, plus the `blocked` / `archived` extensions. The 3-exit-flows rule does not add a state; it adds **terminal classifications** to the transitions out of `running`. The mapping:

| Exit flow | Terminal classification | State transition | Bounded by |
|---|---|---|---|
| **Success** | terminal-success | `running` → `done` | Acceptance criteria met |
| **Fail** | terminal-recoverable | `running` → `ready` (retry) or `running` → `blocked` (dependency) | Retry count, dependency resolution |
| **Error** | terminal-unrecoverable | `running` → `blocked` (escalation) or `running` → `done` (with escalation comment) | Escalation policy |

The 4-state machine is preserved. The 3-exit-flows rule is the **policy layer on top of the state machine** — it tells the dispatcher *which* transition to take when the worker terminates. A worker that finishes without classifying its outcome is a worker that violated the protocol; the dispatcher must infer the class from the output and the `process` field's retry/escalation parameters.

**Two important reconciliations:**

1. **`done` is not always success.** A task can transition to `done` *with* an escalation comment ("the work shipped but here is what didn't work — open a follow-up"). This is the success-with-caveat exit: the goal was met, but the worker encountered an error-class issue it did not let block the work. The `done` state holds; the comment is the audit trail. Without the comment, the operator cannot tell a clean success from a success-with-caveat.

2. **`blocked` is not always fail.** A task that is `blocked` because a parent is `blocked` is in a fail-class state (recoverable — wait for the parent). A task that is `blocked` because the brief was wrong is in an error-class state (unrecoverable — fix the brief). The `blocked` state itself does not distinguish; the `block_kind` (per `methodology/05-strike-rules.md`) does: `kind=dependency` is fail-class, `kind=needs_input` and `kind=capability` are error-class.

### The classification test (3 questions, in order)

When a worker terminates, the worker (or the dispatcher, if the worker exited without classifying) asks these three questions in order. Stop at the first yes.

1. **"Did the worker produce output that satisfies `acceptance_criteria`?"** If yes, the exit is **success.** Transition to `done`.
2. **"Is the cause of non-completion recoverable by the same worker with the same brief?"** If yes (transient timeout, transient API error, missing input from the parent), the exit is **fail.** Transition to `ready` (with retry counter incremented) or to `blocked` (with `kind=dependency` and a comment naming the missing input).
3. **"Otherwise."** The exit is **error.** The brief is wrong, the skill version is broken, or the assumption the goal rested on is false. Transition to `blocked` with `kind=needs_input` (operator decision) or `kind=capability` (the system cannot do this), and emit an escalation comment with the failed node's id, the last error message, and the recommended action.

A worker that cannot answer question 1 (output is produced but does not match acceptance criteria) is in a fail-or-error gray zone; the default is **fail** with `kind=needs_input` and a comment asking the operator to confirm whether the brief is right. This biases toward recovery (one more attempt) rather than escalation.

### Anti-pattern — fewer than 3 exit flows

The explicit rule, restated for emphasis: **any node with fewer than 3 exit flows is a graph-design bug, not a tool bug.**

A node with only `success` and `fail` is missing the error class. The first time it hits an unrecoverable failure, the system retries it 5 times, burns 30 minutes, and escalates anyway. The retry budget is wasted.

A node with only `success` and `error` is missing the recoverable class. The first time it hits a transient timeout, the operator is pulled in for a 30-second problem. The operator's attention is wasted.

A node with only `success` (the most common rookie mistake) is missing both. Every non-success outcome is the same to the dispatcher; the dispatcher either retries everything or escalates everything, and both are wrong.

The fix is at graph-design time: **when the node is added to the graph, the node's exit flows are added with it.** A node without three exits is a node that cannot ship.

### Anti-pattern — the "retry until success" loop

A node that retries on both fail and error (because "we don't know why it failed, just try again") is a node that turns transient bugs into permanent budget burns. The retry budget is bounded; the retry budget does not distinguish fail from error. A spec-mismatch error retrying 5 times produces the same broken output 5 times.

The fix: classify first, then decide. The 3-question test is the classification. Once classified, the policy follows.

### Anti-pattern — the "escalate everything" loop

A node that escalates on both fail and error (because "we don't want to silently retry on a bad brief") is a node that pulls the operator in for transient problems. The operator's attention is the bottleneck; spending it on a 30-second network blip is wasted escalation.

The fix: classify first. Failures that are recoverable do not escalate; the worker retries, the parent supplies the missing input, the system recovers without operator involvement. Only error-class exits escalate.

### Where this Part lives in the larger picture

- **`methodology/04d-decide-flow-spec.md` Fields 5 and 6** — `retry_parameters` and `escalation_process` are the per-flow policies that the 3-exit-flows rule consumes. A flow spec that names "retry on transient timeouts, escalate on schema mismatches" is a flow spec that knows about the fail / error split.
- **`methodology/05-strike-rules.md`** — strike 15 ("workers must call `kanban_complete` or `kanban_block` before exit") is the protocol-level enforcement of the 3-exit-flows rule: the worker must explicitly classify its exit so the dispatcher knows which transition to take. Without strike 15, workers exit without classifying and the dispatcher has to guess.
- **`methodology/06-iteration-loop.md`** — the loop's detect step scans for tasks that exited without a clear classification; the loop's act step files the follow-up (retry, fix-brief, escalate) that the worker should have filed.

### See also

- `methodology/04d-decide-flow-spec.md` — Fields 5 and 6 (retry parameters and escalation process) are the policies the 3 exit flows consume.
- `methodology/05-strike-rules.md` — strike 15 enforces explicit exit classification at the worker-protocol level.
- `methodology/06-iteration-loop.md` — the iteration loop catches exits that were not properly classified.
- `methodology/04a Part 1` (this doc) — the 4-state model; this Part is the policy layer on top of it.

## What's next

- `methodology/05-strike-rules.md` — the strike rule "workers must perform a terminal transition (complete or block) before exit" depends on the work-graph state machine
- `methodology/06-iteration-loop.md` — the loop's detect step scans the work graph for stuck items
- `references/kanban-lite-disciplines.md` — the canonical implementation
- `references/tool-mapping-guide.md` — how to implement the work graph in your tool

## Anti-patterns to watch for

1. **"Work doesn't have states — it's just a list."** → No. Without states, the system can't reason about lifecycle. Add the 4-state minimum.
2. **"Work doesn't have relationships — it's flat."** → No. Without relationships, the system can't reason about context. Add the 4-edge minimum.
3. **"The system auto-cascades."** → No. Cascading is explicit, not automatic. The system surfaces "X cascades to Y" and the operator (or a worker) acts.
4. **"The system can have cycles."** → No. Parent/child and blocks/blocked_by are acyclic. A cycle is a bug.
5. **"The unit has multiple states."** → No. A unit is in one state at a time. Multi-state is a state-machine violation.

## Touch-points with other methodology docs

This doc touches:

- `methodology/02-decide-skills.md` — add paragraph: "if you have parent/child workflows, you need a 'decomposition' skill"
- `methodology/03-decide-agents.md` — add paragraph: "the planner agent is needed when parent/child workflows are common; otherwise `default` is fine"
- `methodology/04-decide-crons.md` — add paragraph: "cascading-crons dispatch child work; they require a sub-graph query"
- `methodology/06-iteration-loop.md` — add paragraph: "the loop's scope can be a single ticket, a parent, or a sub-graph"
- `methodology/08-inbox-route.md` — add section: "inbox items that depend on existing tickets become child tickets or new relationships"

## Parity check

**Pattern:** Multi-phase graph execution inserts an independent verifier node between each phase transition that carries a reviewable work product or changes canonical state.

**Evidence:** `methodology/M-decide-spec-first-flow.md` defines the canonical spec-first order with explicit V nodes; `templates/spec-first-flow.md.template` carries V markers; the verifier profile exists in the operator environment; and `methodology/04c-decide-master-ticket.md` uses verifier completion in its worked flow and done-gate.

**Verdict:** confirmed

The graph-node-order rule is now documented here as the general work-graph contract. The spec-first methodology remains the concrete application of that contract.
