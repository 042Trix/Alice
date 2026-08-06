# Methodology 03 — Decide which agents to scaffold

> The third decision: which agent profiles to scaffold. Agents are **persistent workers with a defined role** — separate from skills (procedures the agent invokes) and separate from chat lanes (operator, you).

## What an agent is (in Alice's terms)

An **agent** is a persistent worker with:
- A **name** that identifies the role (for example, `implementer` or `reviewer`)
- A **role** (one or two sentences: what does this agent do?)
- A **domain** (what knowledge / skills does this agent own?)
- A **tooling** (which tools can this agent use?)
- A **SOUL** (the rules this agent follows — see `templates/agent-soul.md.template`)

Agents are **invoked by name** when work is dispatched to them. They're **persistent** in the sense that their context, history, and configuration survive across sessions. They're **distinct from chat lanes** (which are operator-side, like "operator" or "you").

## When to scaffold an agent

Scaffold an agent when **all four** of these are true:

1. **Recurs as a role** — the role appears in 5+ tasks/week
2. **Has a defined domain** — clear ownership of a knowledge area
3. **Has different skills than other agents** — not just a different name for the same capability
4. **Has a different memory footprint** — benefits from persistent context that other agents don't need

If any of the four fails, **don't scaffold an agent.** Use a `default` assignee for one-offs.

## The 4-step process

```
1. Observe  →  2. Pattern  →  3. Scaffold  →  4. Validate
```

**1. Observe.** For two to four weeks, note which profile receives each dispatched task. Use a `default` profile for most one-offs. Record when the same role recurs—for example, eight weekly tasks that all require an implementer to execute an approved specification.

**2. Pattern.** After 2-4 weeks, look at your notes. Are there 3+ recurring roles with stable descriptions? Those are your candidates.

**3. Scaffold.** For each candidate, write an agent-soul (use `templates/agent-soul.md.template`). The soul should fit in 1-2 pages. Don't write a 10-page soul — that means you don't know the role yet.

**4. Validate.** Run the agent for 2 weeks. Does it actually do the role? If yes, keep. If no, refine the soul or kill the agent.

## The "default" pattern

**Always have a `default` profile.** This is the catch-all for one-off tasks. Without `default`, you'll be tempted to scaffold an agent for every recurring role, even one-offs.

The `default` profile should be:
- Loaded for any task that doesn't match a named profile
- Allowed to use any tool
- Restricted to read-only on most things (sandbox)
- Cheap to spin up (no per-agent tooling)

When `default` starts handling 5+ tasks/week that look like the same role, scaffold a new agent.

## Agent vs skill vs chat lane

| Concept | What | When to use |
|---|---|---|
| **Agent** | Persistent worker with a role | Role recurs 5+ times/week with stable domain |
| **Skill** | Procedure the agent can invoke | Task recurs weekly, repeatable, verifiable, bounded |
| **Chat lane** | Operator-side role (you, in chat) | Tasks that require your judgment, decision, or final approval |

A common confusion: **a "reviewer" agent is not a chat lane.** The reviewer agent is a persistent worker. The "operator" chat lane is you, in chat, approving or denying.

## Agent-soul template

Use `templates/agent-soul.md.template`. The soul has 5 sections:

1. **Identity** — name, role, owner, domain
2. **Boundaries** — what the agent does NOT do
3. **Tools** — which tools the agent can use
4. **Skills** — which skills the agent is expected to load
5. **Audit line** — what the agent writes when it acts

If any section is empty, the agent isn't ready.

## Pitfalls

1. **"I need a 'reviewer' agent, a 'planner' agent, a 'council' agent..."** — maybe. But `default` should be the default, not the exception. If you're scaffolding 5 agents for a solo operator, you've over-scaffolded.
2. **"I need a 'marketing' agent and a 'sales' agent."** — these are roles, not yet agents. Until they have 5+ weekly tasks each, they're `default`.
3. **"I'll have one agent per skill."** — that's not how this works. Skills are the procedures; agents are the workers. One agent can have many skills.
4. **"The agent should be smart."** — agents are role-defined, not smart-defined. A `coder` agent is good at coding because its soul says so, not because it's "smart."
5. **"I'll write the agent soul later."** — no. The soul is the agent. Without it, you have a name without a role.

## What the agent does NOT do

- The agent does NOT decide which skills to load. That's the orchestrator's job (or the operator's).
- The agent does NOT decide which tools to use. That's the soul's definition.
- The agent does NOT escalate to the operator unless the soul says so.
- The agent does NOT collaborate with other agents unless the orchestrator routes work to it.

The agent's job is to **execute its role** within the boundaries set by the soul. Anything outside that role is the orchestrator's or the operator's call.

## Worked example (skeleton)

See `worked-examples/01-solo-founder-skeleton/methodology-notes/03-agents.md` when v0.0 ships for an anonymized example.

## See also

- `methodology/03c-decide-agent-communication-channels.md` — in a multi-agent system, the operator talks to ONLY the orchestrator (the main agent). Downstream agents communicate via ticket comments. The 3 channels (DM, comment, kanban event) are documented in the communication-channels doc.
- `methodology/03b-decide-operator-agent-interaction.md` — the operator is the bottleneck. The 5 operator roles (launcher, approver, governor, curator, fallback) and the operator-action ticket lifecycle are documented in the operator-agent doc.
- `methodology/04a-decide-work-graph.md` — the work graph is the substrate. The planner agent is needed when parent/child workflows are common (5+ projects with dependencies) AND when sub-tasks need explicit decomposition. Otherwise `default` is fine; the worker on a `default` ticket can still create a child work item mid-task (the work-system command lives in `references/tool-mapping-guide.md`).
- `methodology/04b-decide-board-routing.md` — every ticket file is a routing decision. Agents that file tickets should respect the keyword-routing table. If the agent's role includes filing tickets (e.g., a planner filing decomposition children), the agent's soul must include the routing rules. **Alice documents the method, not specific instances.**

## What's next

- `methodology/04-decide-crons.md` — crons are scheduled tasks, not agents. Different decision.
- `methodology/05-strike-rules.md` — the constraints that prevent agents from overreaching.
- `methodology/06-iteration-loop.md` — the engine that drives agent dispatch.

## Part 12: Agent typology glossary (orchestrator, downstream agent, spawned sub-agent, default profile)

Alice uses 4 distinct terms for the different agent-like things in a multi-agent system. This glossary is the canonical place to disambiguate them. **Each term refers to a different mechanism; conflating them breaks the channel design, the memory design, and the operator-action gate.**

### The 4 agent types

| Term | What it is | Persistence | Context | Tools | Memory | Communication |
|---|---|---|---|---|---|---|
| **Orchestrator** | The main agent the operator talks to | persistent across sessions | full chat history + filtered sub-agent output | full tool set + filter tools | full memory budget (Tier 1-7) | DM (operator), comment (downstream agents), kanban event (system) |
| **Downstream agent** | Persistent worker profile with a defined role | persistent across sessions | task-scoped per ticket | per-profile tool set | per-profile memory budget (Tier 1-7) | comment (orchestrator, other downstream agents on the ticket), kanban event (system) |
| **Spawned sub-agent** | Ephemeral task-scoped instance of the main agent (e.g., Claude Code's `Task` tool, Codex's sub-agent feature) | ephemeral (lives for one task) | fresh per task | same tools as the main agent | no persistent memory; output goes back to the main agent | comment (orchestrator, downstream agents on the ticket), kanban event (system) |
| **Default profile** | The catch-all for one-offs | ephemeral (lives for one session) | session-scoped | default tool set | minimal memory (Tier 1 + Tier 6) | comment (orchestrator, downstream agents on the ticket), kanban event (system) |

### The 4 distinctions that matter

**1. Persistent vs ephemeral:**
- Orchestrator + downstream agents are persistent across sessions (they have SOULs that survive)
- Spawned sub-agents + default profiles are ephemeral (they're instantiated for a single task or session)
- The orchestrator-isolation pattern applies to **all 4**, but the memory model differs

**2. Shared vs distinct SOULs:**
- Orchestrator has its own SOUL (operator-facing)
- Downstream agents each have their own SOULs (role-specific)
- Spawned sub-agents share the **main agent's SOUL** (they're instances of the main agent with a fresh context)
- Default profile has its own SOUL (catch-all)

**3. Communication channel:**
- Orchestrator → DM (operator), comment (downstream + spawned sub-agents + default)
- Downstream agent → comment only (per `methodology/03c-decide-agent-communication-channels.md`)
- Spawned sub-agent → comment only (same as downstream agent — they're instantiated per task, but the channel discipline is the same)
- Default profile → comment only (same as downstream agent)

**4. Memory tier:**
- Orchestrator: full Tier 1-7 (operator-facing memory + system-facing memory)
- Downstream agent: per-profile Tier 1-7 (each downstream agent has its own memory budget)
- Spawned sub-agent: **no persistent memory** (output goes back to the main agent's context)
- Default profile: minimal Tier 1 + Tier 6 (operator preferences only)

### The 4 anti-conflations

**Don't say "sub-agent" when you mean "downstream agent":**
- "Sub-agent" implies a spawned instance of the main agent
- "Downstream agent" implies a persistent worker profile
- Different mechanisms; different memory models; different lifecycles

**Don't say "agent" without qualification:**
- "Agent" is ambiguous — could be orchestrator, downstream, spawned, or default
- Use the specific term

**Don't say "main agent" when you mean "orchestrator":**
- "Main agent" is the same as orchestrator (the operator-facing agent)
- Use "orchestrator" for clarity

**Don't say "worker" without qualification:**
- "Worker" could be downstream agent, spawned sub-agent, or default profile
- Use the specific term

### The tool-mapping guide

Map your tool's mechanism to Alice's four terms using [`references/tool-mapping-guide.md`](../references/tool-mapping-guide.md).

**Distinction:** Claude Code's "sub-agent" is **Alice's spawned sub-agent**, NOT Alice's downstream agent. A Claude Code sub-agent shares the main agent's tools and context; it doesn't have its own persistent SOUL. **In Alice, the analogous concept is "downstream agent"** — a persistent profile with its own SOUL, dispatched via the kanban (not via the `Task` tool).

### Worked example (skeleton)

The 2 worked examples (solo-founder, research-analyst) show the agent typology applied to a specific instance. The examples are skeletons, not snapshots.

Map your tool's agent types to Alice's four terms. Use worked examples to understand the decision process, not as configurations to copy.

---

## Part 13: Who creates an agent (the two paths)

The 4-step process above (Observe → Pattern → Scaffold → Validate) describes **when** to scaffold an agent. It does not describe **who** has the authority to do it. This section makes that explicit.

There are exactly **two paths** to a new agent profile. Both end at the same artifact (a persistent worker with a SOUL); they differ in who initiates the work and on whose authority.

A note on terminology: throughout this section, the canonical agent acting on Path A is called the **role-watcher agent** (also: **dispatch-stream monitor**). The role-watcher is the method — the role of watching the dispatch stream and detecting recurring patterns that meet the scaffold criteria. Concrete Alice operators will name this agent differently in their own deployments (e.g., one operator's role-watcher is their `planner` profile; another's might be a `coordinator`, `supervisor`, or `allocator` profile). The role-watcher is the method; the operator's specific profile name is the instance.

### Path A — Role-watcher agent creates autonomously

The **role-watcher agent** is the agent that watches the dispatch stream for recurring role patterns. When the role-watcher detects that a single role recurs **5+ times/week** with a stable domain, different skills than existing agents, and a distinct memory footprint (i.e. all four conditions in "When to scaffold an agent" are met for a not-yet-scaffolded role), the role-watcher creates a new agent profile on its own authority. (In the canonical Alice implementation, the role-watcher is the operator's `planner` profile — but the role-watcher pattern is the method, and other operators may name this agent differently.)

**Trigger:** the role-watcher detects the 5+ weekly role pattern itself.
**Authority:** the role-watcher agent. No operator approval required to start the scaffolding.
**Output:** a new agent profile — name, role, domain, tooling, SOUL — entered into the agent roster.
**Validation gate:** the role-watcher's scaffolding is provisional. The agent runs for 2 weeks under the 4-step process's "Validate" stage; if it does not earn the role, the role-watcher (or the operator) retires it.

This path exists so the system can scale without the operator bottlenecking on every new role detection. The role-watcher is the role-watcher; the operator is not in this loop unless the role-watcher escalates.

### Path B — Operator requests via the agent-resources board

The **operator** can also create a new agent profile on demand — for example, when the operator sees a role emerge that the role-watcher has not yet detected, or when the operator wants to seed a role before the pattern has formally emerged (a "declare-it" rather than "discover-it" path).

**Trigger:** the operator files a request on the agent-resources board (the canonical implementation calls this the **AR board** — the board that owns agent profile CRUD).
**Authority:** the operator. The operator's word is sufficient.
**Output:** a new agent profile, built by the workboard following the standard CRUD flow, with the new agent's SOUL landing on the AR board.
**Validation gate:** same 2-week validate window as Path A. The operator's request does not bypass validation — it just bypasses pattern detection.

This path exists because the operator can see context the role-watcher cannot (strategic intent, upcoming workload, a role the role-watcher hasn't observed yet). The operator is the role-declarer; the role-watcher is not in this loop unless the operator wants a sanity check.

### Routing: where the new agent's artifacts live

Regardless of which path triggered the creation, the artifacts follow the standard agent-CRUD routing:

- **The agent's SOUL** (Identity / Boundaries / Tools / Skills / Audit line) lands on the **AR board** (agent-resources). SOUL is a profile definition; it belongs on the board that owns profile CRUD.
- **The agent's first workboard tickets** (e.g., the validation tickets produced by Path A's "Validate" step) follow the standard board-routing rules from `methodology/04b-decide-board-routing.md` — they file on whatever workboard owns the new agent's domain, not on the AR board.
- **The workboard's CRUD flow** (create / read / update / delete of the agent profile itself) follows whatever your tool's standard flow is — for the canonical implementation, this is the AR board's ticket lifecycle.

The separation matters: SOUL on AR (profile definition), work on the workboard (domain work). Conflating them means the operator can't tell "is this a profile change or a work item?" at a glance.

### The two-path rule

> An agent profile can be created by exactly one of two paths: **the role-watcher agent** (autonomously, when the role-watcher detects a 5+ weekly role pattern that meets all four scaffold conditions), or **the operator** (on operator request via the AR board). There is no third path. No agent creates itself; no other agent (other than the role-watcher) creates new agent profiles autonomously. The role-watcher is the method; the operator's specific instance of the role-watcher (e.g., a `planner` profile) is named at the operator's discretion.

This is the rule. It is explicit here because the 4-step process above leaves the "who" implicit, and implicit "who" rules fail when two agents both think they have authority.

### Worked example (skeleton)

See `worked-examples/01-solo-founder-skeleton/methodology-notes/03-agents.md` when v0.0 ships for an anonymized example showing Path A (role-watcher-driven) and Path B (operator-driven) side by side.
---

## Touch-points with other methodology docs

- `methodology/02-decide-skills.md` — skills (procedures) are built before agents (workers). The 4-step process for scaffolding an agent parallels the 4-question test for building a skill; both tests must pass before scaffolding. Skills go on the workboard, not the AR board.
- `methodology/03a-decide-agent-vault-interaction.md` — once the new agent exists, its vault reads/writes follow the agent-vault interaction rules (tier-1 only on dispatch, etc.). The Path A and Path B scaffolds land the new agent in the agent roster; the agent-vault doc governs how that agent interacts with the vault after that.
- `methodology/03b-decide-operator-agent-interaction.md` — Path B (operator request via AR board) is one of the 5 operator roles (the "curator" role). Path A (planner autonomous) is the planner's role-watcher function; the operator is not in Path A's loop unless the planner escalates.
- `methodology/03c-decide-agent-communication-channels.md` — even after a new agent is scaffolded via Path A or Path B, it still talks to the operator only through the orchestrator, and to other agents only via ticket comments. New agents do not get their own DM channels; the 3-channel discipline applies immediately.
- `methodology/04a-decide-work-graph.md` — the work graph is the substrate the planner watches in Path A. Without the work graph, the planner cannot detect a 5+ weekly role pattern; Path A is unavailable. Path B does not require the work graph.
- `methodology/04b-decide-board-routing.md` — the routing rules for *what* board a ticket files on. The new agent's first workboard tickets follow 04b; only the new agent's SOUL goes on the AR board per Path B's routing note above.
- `methodology/05-strike-rules.md` — strike rules constrain the planner from over-scaffolding (Path A's failure mode is "planner creates 8 agents that all do the same thing"; the "default" pattern in this doc is the counter). The operator's Path B requests are bounded by strike-1 (no per-ticket evidence → no auto-close) and strike-3 (no three-strike rule → durable fix on 3rd correction).

## Anti-patterns to watch for

1. **"I have 8 agents and they all do the same thing."** → Consolidate. Probably you have 1 agent role and 7 over-scaffolds.
2. **"I have 0 agents and the operator is doing everything."** → Add `default`. Then add named agents as patterns emerge.
3. **"I'll add a 'smart' agent to do whatever I want."** → No. "Smart" is not a role. Define what they do, not how smart they are.
4. **"The agent should be a copy of me."** → No. You're the operator. The agent has a role. The role is not "be the operator."
