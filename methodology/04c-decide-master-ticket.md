---
id: alice-methodology-04c-decide-master-ticket
created: 2026-08-05T00:00:00Z
updated: 2026-08-08T00:00:00Z
title: "Methodology 04c — Decide when to use a master ticket (v0.1.2: operator-LGTM-done and descriptive master titles)"
type: methodology
status: draft
source: alice-framework
version: 0.1.2
tags: [kind:methodology, kind:work-graph, kind:master-ticket, kind:done-gate, kind:operator-lgtm, project:alice]
confidence: 0.0
amended_by: ["[[ticket:t_248722d8]]", "[[ticket:t_2a513f60]]"]
links: ["[[methodology/04a-decide-work-graph.md]]", "[[methodology/04b-decide-board-routing.md]]", "[[methodology/00-decide-ticket-naming.md]]", "[[methodology/03b-decide-operator-agent-interaction.md]]", "[[methodology/06a-decide-retro-v2.md]]", "[[references/kanban-lite-disciplines.md]]", "[[references/tool-mapping-guide.md]]"]
---

# Methodology 04c — Decide when to use a master ticket

> A master ticket is the operator-facing root of a multi-step request. It preserves the original request, holds the planned flow, links the work graph, and gives the operator one place to see progress after the work fans out.

This methodology explains **when and why** to use a master ticket, how to structure its children, how to choose its done-gate, and how to verify the resulting work graph.

It is tool-agnostic by design. The abstract method belongs here. Per-tool commands and invocation details belong in `references/tool-mapping-guide.md`.

---

## Part 1: What a master ticket is

A master ticket is the **single source of truth for one operator-originated, multi-step request**.

It is the root node of the request's work graph. Planning, specification, implementation, review, and other deliverables become descendants of that root instead of becoming disconnected top-level tickets.

The master answers five operator questions:

1. What did I originally ask for?
2. What flow did the system choose?
3. Which stages have completed?
4. Which stages remain open or blocked?
5. When is the request truly finished?

The master is not the work itself. It is the durable container and status surface for the work.

A master ticket has five defining properties:

- **Request fidelity:** it preserves the operator's original request verbatim.
- **Graph ownership:** all normal work tickets connect beneath it through explicit parent/child links.
- **Progress visibility:** it exposes completed children over total children.
- **Closure semantics:** it records the condition that permits the whole request to become done.
- **Title discipline:** the title is descriptive of the topic (per Part 8 §Master Title Discipline); source URLs / identifiers live in the body under a `## Source` line.

### Master ticket versus ordinary parent ticket

Every master is a parent, but not every parent is a master.

An ordinary parent may simply decompose a deliverable into smaller tasks. A master additionally carries the operator-facing contract: original request, planned flow, routing note, done-gate, and top-line status.

### Master ticket versus planning ticket

A master is not a substitute for planning.

The master records that planning is part of the flow. A planning ticket is a normal child that performs decomposition, makes scoped decisions, and produces its own artifact.

### Master ticket versus dashboard

A dashboard aggregates many requests. A master represents one request.

The board sweep may display master tickets as top-line rows, but each row remains a queryable work-graph root with its own evidence and lifecycle.

---

## Part 2: When to open one — the 4-question test

Before opening a master, ask four questions:

1. **Is the request multi-step?** Will it pass through two or more meaningful stages such as clarify, plan, build, and verify?
2. **Is the request multi-board?** Will different work-product domains route to different boards or queues?
3. **Is the request multi-child?** Will it produce at least two normal work tickets, whether immediately or as the flow unfolds?
4. **Is the request operator-originated?** Does it represent a coherent outcome the operator asked the system to deliver?

### Decision rule

Open a master when the answer to question 4 is **yes** and at least one of questions 1–3 is **yes** in a way that will make later status difficult to reconstruct.

A request that is clearly expected to produce two or more children should normally get a master.

A cross-board operator request should normally get a master even when the complete child list is not known at intake. Cross-board fan-out is precisely where the original request becomes hard to follow.

### Strong signals

Use a master when:

- A specification will be followed by planning, implementation, and verification.
- A request crosses work-product domains.
- Multiple specialists will contribute independent deliverables.
- The operator will reasonably ask for an overall status after several handoffs.
- Completion means more than one child ticket reaching done.

### Weak signals

Do not open a master merely because:

- A ticket is long.
- A worker has several internal steps.
- The work might receive comments.
- The request is important but still only one bounded deliverable.
- Routine maintenance happens repeatedly.

Importance does not create a master. Work-graph fan-out does.

---

## Part 3: Who opens and owns it

The **orchestrator** opens the master when the operator requests multi-step work.

The orchestrator is the operator-facing role that can preserve the request, choose the initial flow, route children, and keep the top-level status coherent. A worker should not create a second master merely because it receives one child of the flow.

In the source system, operator-facing orchestrator roles may be named `jarvis`, `planner`, `council`, or `dorothy`. These names are implementation examples, not roles a reader must copy. In another system, the role may be called coordinator, lead agent, project agent, or dispatcher.

### Ownership rule

Assign the master to the orchestrator lane, not to a worker lane.

The owner is responsible for:

- preserving request fidelity;
- maintaining the planned-flow summary;
- ensuring children have explicit parent links;
- reporting the completion ratio;
- applying or surfacing the chosen done-gate;
- preventing duplicate masters.

The master must not be dispatched as though it were a leaf deliverable. Its work is coordination and status integrity.

### Operator instruction boundary

The orchestrator opens the master because the operator requested the outcome. Opening the master does not erase later approval boundaries.

If a downstream stage requires a distinct operator decision, encode that gate in the relevant child or in the master's done-gate. Approval of one artifact is not automatically approval to begin a different, irreversible stage.

---

## Part 4: What goes in the body

A useful master body has four mandatory sections.

### 1. Original request

Copy the operator's original request **verbatim**.

Do not paraphrase away constraints, tone, scope, or uncertainty. The planned flow can interpret the request; the original-request section preserves the evidence.

### 2. Planned flow

List the expected stages in order.

The flow may evolve, but it should be specific enough to explain why each first-generation child exists. Name the work product of each stage rather than the person or tool performing it.

### 3. Cross-board routing note

State which work-product domains will own which children.

The master stays on one originating board. Children may live on different boards according to the routing method in `methodology/04b-decide-board-routing.md`.

### 4. Done-gate choice

Write one explicit closure rule:

- auto-done when all required children are done; or
- operator-LGTM-done after all required children are done.

Do not leave the gate implicit.

### Tool-agnostic body skeleton

```markdown
## Original request
[Operator's request, copied verbatim]

## Planned flow
1. [First work product]
2. [Second work product]
3. [Verification or acceptance work product]

## Cross-board routing
- [Work-product domain] → [owning board or queue]
- [Work-product domain] → [owning board or queue]

## Done gate
[Auto-done after every required child is done | Operator LGTM after every required child is done]
```

This is a content shape, not a tool invocation.

### Scope changes

When the operator adds a requirement mid-flow, preserve the original request and add a linked child for the added requirement. Record the change in the ticket history or a clearly marked amendment section.

Do not silently rewrite the original request. Request fidelity depends on distinguishing intake from later scope changes.

---

## Part 5: What the children look like

Children are normal work tickets.

They use the same fields, evidence expectations, worker protocol, and state model as any other ticket. The master pattern changes their place in the graph, not the nature of their work.

Every first-generation child has:

- a bounded deliverable;
- a suitable worker or role assignee;
- an explicit parent link to the master;
- acceptance or stop conditions;
- evidence at completion;
- the normal 4-state lifecycle.

### Child lifecycle

The minimum child lifecycle follows `methodology/04a-decide-work-graph.md`:

```
ready → running → done → archived
```

A child may enter a blocked state or needs-input condition when the implementation supports it. Those conditions do not make the master complete.

### Parent and dependency are different edges

A parent link says, "this work is part of that request."

A dependency edge says, "this work cannot start until that work finishes."

A child can belong to the master without every sibling becoming its dependency. Record execution order separately when it matters.

### Nested decomposition

If a child needs its own subtasks, link those subtasks to the child, not directly to the master.

The shape remains:

```
master → child → subtask
```

This preserves ownership boundaries and keeps the master's direct-child completion ratio meaningful.

### Cross-board children

Children route by work-product domain. Their board does not need to match the master's board.

The parent edge must survive cross-board routing. If the chosen tool cannot represent cross-board links directly, store a durable reciprocal reference that supports traversal from master to child and from child to master.

---

## Part 6: Choose the done-gate

The done-gate answers one question: **what evidence is sufficient to close the operator's whole request?**

There are two supported choices.

### Option A: Auto-done — default

The master becomes done automatically when every required child is done.

Use auto-done when:

- child acceptance criteria are sufficient;
- verifier completion is included among the required children;
- no irreversible or subjective operator decision remains;
- the operator wants status without an extra approval step.

Auto-done is the default because the master itself becomes the completion signal. It avoids leaving completed requests in a stale running state.

### Option B: Operator-LGTM-done

All required children must be done, and then the operator must explicitly approve closure.

Use this gate when:

- the final outcome is subjective;
- the work is public-facing or irreversible;
- the operator asked to retain final acceptance;
- the flow's last meaningful action is an operator decision.

Verifier-done is necessary but not sufficient under this gate.

### Encode the gate in the body

The body must identify the selected option in plain language.

A watcher or orchestrator should not infer the gate from title prefixes, assignee names, or age. Explicit body text is the contract.

### Closure evidence

A master closes only when its chosen gate is satisfied.

Age is not closure evidence. A stale master with open children remains open. The remedy is to unstick the graph, not to hide the root.

---

## Part 7: The master state machine

The master has a deliberately small lifecycle:

```
ready → running → done
```

### `ready` — master just opened

The master exists, the operator request is recorded, and no child has begun work.

`ready` means the request is an active commitment, not that a worker should claim the master as a leaf task.

### `running` — first child starts

The master transitions to `running` when the first child begins.

It remains running while any required child is open, blocked, or awaiting its own valid gate.

### `done` — all children satisfy the done-gate

For auto-done, the master becomes done when all required children are done.

For operator-LGTM-done, the master becomes done only after all required children are done and the operator records approval.

### State invariants

1. A master with no started children should not claim progress.
2. A master with an open required child cannot be done.
3. A master with all required children done cannot remain running indefinitely under auto-done.
4. A master under the LGTM gate remains running until the approval event exists.
5. Archiving is a later retention action, not an alternate completion path.

### New children after apparent completion

If scope legitimately expands before closure, the new child joins the required-child set and the completion ratio changes.

If the master has already closed, decide whether the new request is a correction to the original outcome or a new operator request. Do not re-open or nest masters mechanically; preserve the audit trail.

---

## Part 7.5: The done-gate opt-in rule for operator-facing flows (v0.1.1)

**Default is auto-done. Operator-facing flows must explicitly opt into operator-LGTM-done.** This sub-section codifies the lesson from the x-article-review loop on 2026-08-07: the master ticket `t_62a99460` auto-closed prematurely because the master body did not specify the done-gate, so the operator never got to review the 5 council decisions before the master disappeared.

### The rule

A master ticket is **operator-facing** when **any** of the following is true:

1. The flow's final output is a recommendation, judgement, or synthesis that the operator must review (council verdicts, content reviews, article reviews, retro findings, prioritization decisions).
2. The flow's artifact is the input to a downstream operator decision (e.g., the operator reads the master ticket body to decide whether to spawn implementation tickets).
3. The flow ends with a per-finding accept/reject/defer step that only the operator can perform.
4. The flow is multi-board / multi-profile / multi-specialist and the operator needs visibility into the final shape before the request is "done."
5. The flow's work product is meaningful only if the operator reads it (the operator-facing interaction principles in `methodology/03b-decide-operator-agent-interaction.md` apply).

For any operator-facing master, the body's **Done gate** section MUST say exactly `done-gate: operator-LGTM-done` (or equivalent explicit text — see Part 4 §4 for the body skeleton). The word `auto-done` in the Done gate section is a violation of this rule.

### Why the default is auto-done

Most masters produce machine-checkable deliverables. The auto-done default (Part 6 Option A) avoids leaving completed requests in a stale running state. The default is right for the common case.

The default is wrong for operator-facing flows. Auto-done means the master closes when every required child is done — which is precisely the moment before the operator has had a chance to review the synthesized findings. The operator arrives at the board later, finds the master already closed, and has to manually re-open or work from the children. The lesson is the operator's correction: "auto-close is correct for an auto-done master, but the x-article-review flow is operator-facing — the operator MUST review the findings before the master closes."

### The retro child also gates on the operator

For operator-facing flows, the retro per `methodology/06a-decide-retro-v2.md` MUST be **spawned as a child at Phase 0 (master creation)**, not at phase completion. The retro child carries `kind=needs_input` and `assignee=verifier` and stays blocked until the operator dispositions the retro findings (ACCEPT / REJECT / DEFER per 06a Rule 4).

This is the structural complement of the operator-LGTM-done gate:

- The **master** gates on the operator's LGTM on the synthesized findings.
- The **retro** gates on the operator's disposition of the retro findings.

Both gates are necessary. Spawning the retro at Phase 0 prevents the master from auto-closing before the retro can fire (the original bug that motivated this rule).

### Worked example — the x-article-review master (v0.1.1)

The x-article-review loop produces a master with 4 phase children + 1 retro child. The body MUST include:

```markdown
## Done gate
operator-LGTM-done after all 4 phase children complete AND the operator has reviewed the 5 council decisions (Q1-Q5) AND the retro child has been dispositioned. The master does NOT auto-done.
```

The orchestrator's flow is:

1. **Phase 0** — open master with `done-gate: operator-LGTM-done` in body; spawn retro child at Phase 0 with `kind=needs_input`, `assignee=verifier`, `parents=[master-ticket-id]`, `title` starting with `[HUMAN ACTION]`.
2. **Phases 1–4** — phase children run in order; master accumulates findings.
3. **Phase 5** — call `kanban_block(kind=needs_input, ...)` on the master with the 5-council-decision review request. Do NOT auto-complete.
4. **Operator reviews** the 5 council decisions, calls `kanban_complete` on the master after LGTM.
5. **Retro unblocks** — the operator's master LGTM unblocks the retro child; the retro fires per 06a Rule 4.
6. **Operator dispositions** retro findings (ACCEPT/REJECT/DEFER); retro closes per 06a Part 4.

### What this rule forbids

- Default `done-gate: auto-done` in an operator-facing master body. **Violation.**
- Spawning the retro child AFTER all phase children complete (the original bug). **Violation.**
- Calling `kanban_complete` on the master without an explicit operator LGTM review of the synthesized findings. **Violation.**
- Inferring the done-gate from title prefixes, assignee names, or ticket age. The body text is the contract; the gate is not inferred (per Part 6 §"Encode the gate in the body").

### What this rule does NOT forbid

- Using `done-gate: auto-done` for non-operator-facing flows (cron-driven lint, build pipelines, batch jobs). The default is correct for these.
- Letting the operator pre-approve the master LGTM at intake (e.g., "I'm filing this and I already know the answer is yes"). This is fine; the gate still fires, the body still says `operator-LGTM-done`, and the operator's review is recorded.
- Retro children for non-operator-facing masters. The retro v2 spec already restricts retros to master-ticket flows; for non-operator-facing masters the retro may still be useful but does not gate the master.

### Cross-references

- Part 4 §4 — the body skeleton, including the **Done gate** section.
- Part 6 — the two done-gate options (auto-done vs operator-LGTM-done).
- `methodology/06a-decide-retro-v2.md` Rule 4 — the retro as a kanban task; the retro's `kind=needs_input` blocks until operator disposition.
- `methodology/03b-decide-operator-agent-interaction.md` — operator-facing interaction principles; the gate is one instance of the broader pattern.
- Source: `t_62a99460` (the master ticket that auto-closed prematurely on 2026-08-07) and `t_248722d8` (the v0.1.1 structural fix).

---

## Part 8: The operator-facing surface

The master exists to make status easy to see.

A board sweep should show open masters before ordinary tickets. Each row should include:

- master title;
- originating board;
- current state;
- done children over total children;
- gate status when all children are done but operator approval remains.

### Top-line format

```text
[MASTER] <request summary> — running, 3/5 children done
```

For an operator-LGTM gate:

```text
[MASTER] <request summary> — all children done, operator LGTM pending
```

### Master Title Discipline

A master's title is the operator's first read across a list of 50+ peers. The title MUST be **descriptive of the topic** in plain language. The URL, identifier, or other source reference goes in the body under a `## Source` line, NOT in the title.

**Three rules:**

1. **Title carries the topic.** The title includes the operator-facing topic name (e.g., "x-article-review of rari's 3-layer agent-stack piece") — never the URL or post ID.
2. **Body carries the source.** The body has a `## Source` block with the URL, post ID, author, date, type, and any operator-side context. The title can include the author or short slug if it aids scannability (e.g., "x-article-review / rari 3-layer-stack" — 50 such tickets in a row should still be distinguishable in list view by author + topic).
3. **List-view survival.** Most kanban UIs truncate titles around 60-80 characters. The descriptive topic must fit in that budget. The full URL + boilerplate does NOT.

**Format convention for x-article-review masters (and similar per-source masters):**

```
[MASTER] x-article-review / <author-short-name> <topic-slug> — <one-line description>
```

Examples:

```
[MASTER] x-article-review / rari 3-layer-agent-stack — review + council verdict + retro
[MASTER] x-article-review / osmani prompt-caching — review + comparison to Alice memory
[MASTER] x-article-review / weng agentic-loop-patterns — review + 5-seat council
```

The operator reading 50 of these in list view should be able to identify each by the `<author-short-name> <topic-slug>` pair. Identical "x-article-review" prefixes are fine; the author+topic is the discriminator.

**Anti-pattern:** `[MASTER] x-article-review article 1: https://x.com/0xwhrrari/status/...` — URL in title, no descriptive topic, indistinguishable from article 2 / 3 / 50.

On a board that uses the two-line AREA + topic + descriptor format from `methodology/00-decide-ticket-naming.md`, apply the same scannability discipline to master titles. The `[MASTER]` prefix replaces the AREA line for master tickets; the remaining title carries the topic and descriptive name in a single list-view line. The full source URL or identifier remains in the body under `## Source`.

### Completion ratio

The basic ratio is:

```text
done required children / total required children
```

The ratio is a navigation aid, not a substitute for evidence. A `4/5` master can still be blocked on its most important child. The operator should be able to inspect the remaining child directly.

### Surface discipline

- Put master rows first in board sweeps.
- Report one line per open master.
- Link or identify the blocker when progress is stalled.
- Do not treat the `[MASTER]` prefix as proof that operator action is needed.
- Do not report a closed master as active merely because an old report still contains it.

The operator-facing interaction principles are defined in `methodology/03b-decide-operator-agent-interaction.md`.

---

## Part 9: Six anti-patterns

1. **Opening a master for single-ticket work.** It adds coordination overhead without improving visibility because the ordinary ticket already represents the whole request.
2. **Letting the master sit In Progress after all children are done.** Under auto-done, stale running status destroys the single-source-of-truth value the master was created to provide.
3. **Creating children without `task_links`.** Unlinked work cannot contribute to traversal or the completion ratio, so the master becomes an orphaned summary instead of a graph root.
4. **Making the master assignee a worker.** A worker may claim the coordination container as a leaf deliverable; the master belongs to the orchestrator lane.
5. **Re-creating the master as a child.** Self-nesting or duplicate roots break request identity; extend the graph with normal children or create a genuinely separate master for a new request.
6. **Opening a master for routine maintenance.** Repetition alone does not justify a request root; use a normal maintenance ticket unless a particular operator request genuinely fans out.

---

## Part 10: Verification recipe

Run the master query at the top of a board sweep. It finds every open master and calculates its direct-child completion ratio.

The following SQL is preserved verbatim from the operator's standing rule:

```sql
-- Find all open master tickets and their completion ratio
SELECT
  m.id, m.title, m.board, m.status,
  COUNT(c.id) AS total_children,
  SUM(CASE WHEN c.status='done' THEN 1 ELSE 0 END) AS done_children
FROM tasks m
LEFT JOIN task_links l ON l.parent_id = m.id
LEFT JOIN tasks c ON c.id = l.child_id
WHERE m.title LIKE '[MASTER]%' AND m.status NOT IN ('done','archived')
GROUP BY m.id
ORDER BY m.created_at DESC;
```

### Review each result

For each row, verify:

1. The body contains the original request.
2. The body contains the planned flow.
3. The body contains the cross-board routing note.
4. The body contains one explicit done-gate.
5. Every direct child is truly part of the request.
6. Every expected first-generation child has a parent link.
7. The reported state agrees with child state and gate evidence.

### Interpret special results

- **Zero children, ready:** the master may be newly opened; confirm that decomposition is expected.
- **Zero children, old:** the request may be stranded; route or block it rather than closing on age.
- **All children done, running, auto-done:** apply the completion transition and record evidence.
- **All children done, running, LGTM-gated:** surface the concise approval request to the operator.
- **Some children blocked:** report the blocker and keep the master open.

### Verification is live-state based

Do not rely on stale reports or title searches alone. Query the canonical work graph and inspect current state, links, assignee, and done-gate evidence.

The exact way to run a query or board sweep varies by tool. Put those invocations in `references/tool-mapping-guide.md`, not in this methodology.

---

## Part 11: Worked example — Fantasy Football Draft Tool

This worked example illustrates the method with the request used in the source standing rule. The names are illustrative; readers should derive their own boards, roles, and flow.

### Original request

> Build a Fantasy Football Draft Tool.

### Step 1: Open the master

Create one master on the originating work board.

Its body preserves the request verbatim and records the planned flow:

1. Business analysis and clarifying questions produce an approved specification.
2. Planning defines the implementation sequence and routing.
3. Build work produces the tool.
4. Verification checks the agreed acceptance criteria.

Its done-gate states whether verifier completion closes the master automatically or whether operator LGTM is still required.

### Step 2: Spawn the BA child

Create a business-analysis child linked directly to the master.

The child owns clarifying questions and the specification. It follows the normal child lifecycle and produces acceptance evidence when complete.

The master moves from `ready` to `running` when this first child starts.

### Step 3: Spawn loop, planner, coder, and verifier children

As the approved flow unfolds, create the needed children and link each first-generation child to the master:

- the loop or workflow child owns orchestration setup;
- the planner child owns decomposition and sequencing;
- the coder child owns implementation;
- the verifier child owns independent acceptance review.

Route each child to the board that owns its work-product domain. Do not move the master between boards as children fan out.

If one of these children decomposes further, its subtasks link to that child.

### Step 4: Close the master

As children finish, the master remains `running` and the board sweep reports the ratio, such as `3/5 children done`.

When every required child is done:

- under auto-done, the master closes automatically;
- under operator-LGTM-done, the orchestrator surfaces one concise approval request, and the master closes after approval.

The final master is the durable answer to: "Did we finish the Fantasy Football Draft Tool request?"

### Why the example needs a master

The flow is multi-step, multi-child, likely multi-board, and operator-originated. Without a master, the specification, planning, implementation, and verification tickets could all finish correctly while the operator still lacks one overall status.

---

## Part 12: Tool-mapping boundary

This methodology defines the conceptual contract:

- when to open a master;
- who owns it;
- what its body contains;
- how children link to it;
- how state changes;
- how the done-gate works;
- what the operator sees;
- what must be verified.

It intentionally does **not** define tool-specific CLI invocations.

`references/tool-mapping-guide.md` is the correct place to document how a specific platform creates a master, adds parent links, queries progress, and performs a terminal transition.

Keep the boundary clean:

- **Methodology:** when and why.
- **Tool mapping:** how in a named tool.
- **Tool-specific skill references:** operational details, edge cases, and implementation behavior.

If a command changes, the tool mapping or skill reference can change without rewriting the method.

---

## When to revise this methodology

Revise the method when one of these changes:

- masters routinely have multiple legitimate roots;
- direct-child completion ratio no longer represents request progress;
- a third done-gate becomes necessary;
- cross-board links cannot preserve graph integrity;
- operators consistently need a different top-line status surface;
- routine requests reveal a new anti-pattern not covered by the six above.

Do not revise the method merely because a tool renames a command or field. That belongs in the tool-mapping guide.

---

## Adoption checklist

Before adopting the pattern, confirm:

- [ ] Your work tracker supports a stable root identity.
- [ ] Parent/child links can cross the relevant boards or queues.
- [ ] The orchestrator lane cannot be mistaken for an ordinary worker lane.
- [ ] Child state is queryable from the master.
- [ ] One of the two done-gates can be encoded explicitly.
- [ ] A board sweep can report completion ratios.
- [ ] Closure events retain evidence.

For each new master, confirm:

- [ ] The 4-question test justifies it.
- [ ] The original request is preserved verbatim.
- [ ] The planned flow is visible.
- [ ] Cross-board routing is documented.
- [ ] The done-gate is explicit.
- [ ] Every first-generation child has a parent link.
- [ ] The operator-facing row reports live state.

---

## See also

- `methodology/04a-decide-work-graph.md` — defines parent/child links, dependency edges, graph invariants, and the normal 4-state lifecycle used by master children.
- `methodology/04b-decide-board-routing.md` — defines work-product routing when a master stays on the originating board and children span other boards.
- `methodology/03b-decide-operator-agent-interaction.md` — defines the operator-facing status, approval, and escalation surfaces used by the master and its done-gate.
- `references/kanban-lite-disciplines.md` — supplies the minimal state, board, and 3-element-system context on which the master pattern builds.
- `references/tool-mapping-guide.md` — owns per-tool CLI invocations and implementation mappings; this methodology owns when and why.
- `~/.hermes/skills/kanban-discipline/references/master-ticket-pattern-2026-07-26.md` — tool-specific worked example, edge cases, and ongoing health check.
- `~/.hermes/skills/kanban-discipline/references/master-ticket-pattern--operator-standing-rule--2026-07-26.md` — source standing rule, mandatory structure, done-gates, and verification query.
- `~/.hermes/skills/autonomous-ai-agents/hermes-orchestrator-handoff/references/master-ticket-pattern.md` — tool-specific orchestrator handoff shape and implementation pitfalls.

---

## Summary

A master ticket is the operator's durable root for a request that will fan out.

Open it for operator-originated work whose multi-step, multi-board, or multi-child shape would otherwise fragment status. Assign it to the orchestrator, preserve the original request, link every first-generation child, choose a done-gate explicitly, surface the live completion ratio, and close only when the gate's evidence exists.

The master does not replace planning, routing, verification, or normal ticket discipline. It connects them into one request-shaped work graph.

---

## Part 13: Maintenance and amendment history

This methodology is amended through the same loop-builder mechanism used for amendments to any other canon doc. Each amendment is a ticket on the alice-framework board, the diff lands in this doc + (when applicable) the cross-referenced doc, and the `version` field in the frontmatter is bumped.

### v0.1.1 (2026-08-07) — operator-LGTM-done opt-in rule (Part 7.5)

**Ticket:** `t_248722d8`

**Change:** Added Part 7.5 ("The done-gate opt-in rule for operator-facing flows"). Codifies that **operator-facing masters MUST explicitly opt into `operator-LGTM-done` in the body** — the auto-done default is wrong for operator-facing flows. The retro child MUST be spawned at Phase 0 (master creation), not after phase completion, so the master cannot auto-close before the retro fires.

**Source:** The x-article-review master `t_62a99460` auto-closed prematurely on 2026-08-07 because its body did not specify the done-gate; the operator never reviewed the 5 council decisions before the master disappeared. The retro ticket `t_49af582a` was filed as a workaround. The structural fix is the v0.1.1 amendment.

**Cross-references added:**
- `methodology/06a-decide-retro-v2.md` (in frontmatter `links:` and Part 7.5 body).
- Tags `kind:done-gate` and `kind:operator-lgtm` added.
- Frontmatter `version: 0.1.1`, `updated: 2026-08-07T00:00:00Z`, `amended_by: ["[[ticket:t_248722d8]]"]` added.

**Companion changes (in the same ticket):**
- `~/.hermes/loops/hermes.yaml` `x-article-review` entry bumped to v0.1.1. Worker prompt now contains Phase 0 (master opens with operator-LGTM-done + retro child spawned) and Phase 5 (master close gated on operator LGTM, retro disposition).
- `~/.hermes/loops/intents/x-article-review-intent.md` bumped to v0.1.1.

**Concurrent re-upgrade (2026-08-07, t_ba4b9a90):** The loop yaml was concurrently upgraded to v0.2.0 by the F-1..F-8 ACCEPTED findings from the article-1 retro (council synthesis). The v0.2.0 yaml layers on top of the v0.1.1 gate contract: master assignee = jarvis OR planner (orchestrator lane); STRICT sequential parents (not parents=[master] alone for P2-P4); V-1 hard gate; 4-method retrieval ladder; preflight; master body IS the findings doc; post_graph_retro F-6 gate; attribution URL rule. The 04c Part 7.5 rule remains canonical; the v0.2.0 specs are extent refinements. See intent file `notes:` field for the F-1..F-8 specifics.

### v0.1.2 (2026-08-08) — Master Title Discipline (Part 1 and Part 8)

**Ticket:** `t_2a513f60`

**Change:** Added the Master Title Discipline contract. Master titles must identify the topic in plain language and survive list-view truncation; source URLs and identifiers belong in the body under `## Source`. Added the `[MASTER] x-article-review / <author-short-name> <topic-slug> — <one-line description>` convention, examples, anti-pattern, and a cross-reference to `methodology/00-decide-ticket-naming.md`.

**Source:** Operator direction 2026-08-08, prompted by the vague title on `t_62a99460` and the need to distinguish 50+ per-article masters.

### v0.1.0 (2026-08-05) — initial draft

Original methodology. Parts 1–12 cover the conceptual contract: when to open, who owns, body shape, child shape, done-gate, state machine, operator-facing surface, six anti-patterns, verification recipe, worked example, tool-mapping boundary, and revision guidance.

### When to amend this doc

Amend `methodology/04c-decide-master-ticket.md` when:

- A new done-gate option becomes necessary (Part 6 currently enumerates two).
- A working operator-facing flow that auto-closed prematurely is observed (this is the v0.1.1 trigger; another instance warrants a v0.1.2 with the same rule promoted or refactored).
- The body skeleton (Part 4) needs a new mandatory section.
- A cross-board / cross-tool case changes one of the canonical patterns.
- The 6 anti-patterns (Part 9) grow a seventh (the operator-LGTM-done anti-pattern is a strong candidate for Part 9.7 — the v0.1.1 amendment deferred this to keep the diff focused on the rule itself).
