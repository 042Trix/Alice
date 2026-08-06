# Alice — methodology (linear walkthrough)

> A linear overview for readers who want the framework at a glance. The 16 decision guides in `methodology/` go deeper; this file is the index.

Alice is a tool-agnostic methodology for designing a personal or small-team operating system supported by agents. It organizes the decisions behind persistent knowledge, reusable procedures, agent roles, recurring work, work tracking, and constraints derived from repeated failures. This walkthrough presents those decisions in sequence; the detailed guides and fillable templates provide the next step for each one.

## The 15-minute walkthrough

### 1. Decide your vault tier (5 min)
Read `methodology/01-decide-vault-tier.md`. **The vault is one tier of memory — the long-form tier.** For the full memory tier taxonomy, see `methodology/01a-decide-memory.md` (next).

### 1a. Decide your memory tiers (5 min)
Read `methodology/01a-decide-memory.md`. **Memory is the substrate of every other methodology doc.** The 7-tier taxonomy: cold-start, procedural, identity, episodic, long-form, operator preferences, system. Each tier has a purpose, owner, budget, recall trigger, and lifecycle. Use `templates/memory-budget.md.template`.

### 1b. Decide your vault content management (5 min)
Read `methodology/01b-decide-vault-content.md`. **The tier doc tells you WHERE notes live; this doc tells you HOW to write them.** Frontmatter schema, tag namespacing, note IDs, wikilinks, atomicity rules, note lifecycle, version discipline, MOCs (Maps of Contents — index notes linking related atomic notes), embeddings, backups. Use `templates/frontmatter-schema.md.template`.

### 2. Decide which skills to build (3 min)
Read `methodology/02-decide-skills.md`. **Build skills only when a task repeats weekly and the skill-brief has been validated against context.** Don't build a skill for a one-off. The skill-brief template at `templates/skill-brief.md.template` is the first artifact.

### 3. Decide which agents to scaffold (3 min)
Read `methodology/03-decide-agents.md`. **Build agent profiles only when a role recurs 5+ times/week with a stable domain.** The default profile is the catch-all. Use `templates/agent-soul.md.template`.

### 3a. Decide how agents interact with the vault (5 min)
Read `methodology/03a-decide-agent-vault-interaction.md`. **The agent design doc tells you WHO the agent is; this doc tells you HOW THE AGENT ACTS on the vault.** The 5 interaction patterns (read-only, propose, edit, scaffold, archive), the read-then-write discipline, the tool-call budget, concurrent-write protection, agent-as-scaffolder/archivist guardrails. Use `templates/agent-vault-permissions.md.template`.

### 3b. Decide how the operator interacts with agents (5 min)
Read `methodology/03b-decide-operator-agent-interaction.md`. **The agent-vault doc tells you HOW THE AGENT acts on the vault; this doc tells you HOW THE OPERATOR interacts with the agent.** The 5 operator roles (launcher, approver, governor, curator, fallback), the operator-action ticket lifecycle, the chat-output-budget rule, the escalation chain, the DM/DM-back discipline. Use `templates/operator-interaction-patterns.md.template`.

### 3c. Decide your agent communication channels (5 min)
Read `methodology/03c-decide-agent-communication-channels.md`. **The most architecturally significant doc in Alice.** The 3 channels (DM, comment, kanban event), the orchestrator-isolation pattern, the DM-isolation rule, the comment-as-downstream agent-channel rule, the context-window-isolation rationale, the task-specific-info rationale, the escalation path. **Operator talks to the orchestrator only via DM; downstream agents communicate via ticket comments.**

### 4. Decide which crons to run (3 min)
Read `methodology/04-decide-crons.md`. **Every cron must satisfy detect → surface → act.** If any leg is missing, it's a one-shot, not a cron. Use `templates/cron-spec.md.template`.

### 4a. Decide your work graph (5 min)
Read `methodology/04a-decide-work-graph.md`. **Work is stateful and has relationships.** The minimum is 4 states (ready/running/done/archived) and 4 edges (parent/child, blocks/blocked-by). Multi-phase flows also require an explicit independent verifier node between each reviewable phase transition. The kanban board is the canonical implementation. If you have 5+ projects with dependencies, this doc is non-optional.

### 4b. Decide your work-type routing system (5 min)
Read `methodology/04b-decide-board-routing.md`. **Every filed ticket requires a routing decision.** The guide covers the five-question test, keyword-routing tables, cross-board moves, and the default-board pattern. Alice documents the method; each operator designs boards for their own context.

### 4c. Decide when to use a master ticket (5 min)
Read `methodology/04c-decide-master-ticket.md`. **Operator-originated multi-step requests get a master.** The master preserves the original request, holds the planned flow, links the work graph, and exposes one progress surface after the work fans out. The 4-question test, the master-vs-ordinary-parent distinction, and the done-gate are all in this doc.

### 4d. Decide the per-flow spec (the 6-field flow spec) (5 min)
Read `methodology/04d-decide-flow-spec.md`. **Every flow ships with the same six fields: goal, inputs, outputs, success criteria, retry parameters, escalation.** The doc defines each field, names the per-field anti-patterns, and shows a worked example. A flow without all six fields is a flow that requires operator judgment at every step.

### 5. Decide which strike rules to write (3 min)
Read `methodology/05-strike-rules.md`. **Strike rules come from observed failures, three of the same shape.** Each rule is one paragraph. Use `templates/strike-rule.md.template`.

### 6. Iteration loop (3 min)
Read `methodology/06-iteration-loop.md`. The detect → surface → act protocol is the engine. Without it, crons generate noise and skills generate no-ops.

### 7. Council methodology (2 min)
Read `methodology/07-council-methodology.md`. 4 seats minimum: Strategist, Engineer, Operator, Skeptic. Use `templates/council-seat.md.template`.

### 8. Inbox routing (2 min)
Read `methodology/08-inbox-route.md`. Items from your external inbox get classified, deduplicated, and routed by `templates/inbox-route.md.template`.

### 9. External sources (X, articles, podcasts) (2 min)
Read `methodology/09-inbox-from-external-sources.md`. The X-ingestion pipeline is a specialization of inbox routing. See `references/x-ingestion-pipeline.md` for the operating spec.

## How the 16 guides relate

```
1. Decide vault tier  →  the substrate (where notes live)
                          ↓
1a. Decide memory     →  the substrate (7-tier memory taxonomy)
                          ↓
1b. Decide vault content →  the content management (frontmatter, tags, lifecycle)
                          ↓
2. Decide skills      →  the procedures (what the agent can do)
                          ↓
3. Decide agents      →  the workers (who does what)
                          ↓
3a. Decide agent-vault → how the agent acts on the vault (5 patterns + budgets)
                          ↓
3b. Decide operator-agent → how the operator interacts with agents (5 roles + lifecycle)
                          ↓
3c. Decide agent-communication → the 3 channels + orchestrator-isolation pattern
                          ↓
4. Decide crons       →  the heartbeat (what runs on a schedule)
                          ↓
4a. Decide work graph →  the substrate (stateful work + relationships + verifier-gated phase order)
                          ↓
4b. Decide routing    →  the file-this-ticket decision (board-routing)
                          ↓
4c. Master ticket     →  the operator-facing root of a multi-step request
                          ↓
4d. Flow spec         →  the per-flow contract (goal/inputs/outputs/success/retry/escalation)
                          ↓
5. Strike rules       →  the constraints (what the agent must NOT do)
                          ↓
6. Iteration loop     →  the engine (detect → surface → act)
                          ↓
7. Council methodology → the deliberation (multi-perspective review)
                          ↓
8. Inbox route        →  the input filter (how external items become work)
                          ↓
9. Inbox from external → X posts, articles, podcasts (special case)
```

Each layer depends on the layers below it. Don't build a council (7) before you have an iteration loop (6). Don't write strike rules (5) before you've observed the failure pattern.

## The 3-bucket feedback rule

When you read each doc and find something that doesn't match your context, classify your feedback:

1. **Aligns** — the doc is correct; my context is just different
2. **Contradicts** — the doc is wrong; needs revision
3. **Adds** — my context has a pattern the doc doesn't cover; should be a new doc

**Aligns** requires no change. Record **Contradicts** and **Adds** in the framework's project tracker or review inbox.
