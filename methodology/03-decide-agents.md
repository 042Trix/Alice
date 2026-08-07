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
- `methodology/05-op-guards.md` — the constraints that prevent agents from overreaching.
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
- `methodology/05-op-guards.md` — operational guards constrain the planner from over-scaffolding (Path A's failure mode is "planner creates 8 agents that all do the same thing"; the "default" pattern in this doc is the counter). The operator's Path B requests are bounded by op-guard-1 (no per-ticket evidence → no auto-close) and op-guard-3 (no three-operational guard → durable fix on 3rd correction).

## Anti-patterns to watch for

1. **"I have 8 agents and they all do the same thing."** → Consolidate. Probably you have 1 agent role and 7 over-scaffolds.
2. **"I have 0 agents and the operator is doing everything."** → Add `default`. Then add named agents as patterns emerge.
3. **"I'll add a 'smart' agent to do whatever I want."** → No. "Smart" is not a role. Define what they do, not how smart they are.
3. **"The agent should be a copy of me."** → No. You're the operator. The agent has a role. The role is not "be the operator."

---

## Part 14: Minimum-viable agent roster (the 5 roles)

This part names the **functional roles** Alice's methodology considers the minimum-viable agent roster for any operator running Alice in earnest. **Roles are methodology-level descriptions, not operator-instance profile recommendations.** Different operators will name their own profiles differently — what matters is that each role's function is present in the system. If you find yourself missing one of these roles and cannot combine it with another without breaking the work, that's the signal that you need to scaffold a profile for it.

> **Method-not-instance reminder:** this part describes **roles** (what the work is) and not **instances** (which profile names you should pick). The roles are the method; your profile names are your instance. Use the names you like; keep the functions present.

### The 5 roles at a glance

| Role | One-line function | Canonical methodology file |
|---|---|---|
| **Orchestrator** | The operator-facing main agent that runs the request → plan → dispatch → verify loop | `methodology/03-decide-agents.md` (this file), `methodology/06-iteration-loop.md` |
| **Business Analyst (BA)** | Captures operator-asks, drafts ticket specs, asks clarifying questions before work begins | `methodology/04d-decide-flow-spec.md`, `methodology/04d-decide-flow-derivation.md` |
| **Planner** | Watches the dispatch stream for recurring role patterns; scaffolds new agent profiles when a role recurs and meets the scaffold criteria | `methodology/04a-decide-work-graph.md`, `methodology/03-decide-agents.md` Part 13 (Path A) |
| **Verifier** | Checks completed work against acceptance criteria; flags rework; gate-keep before downstream consumption | `methodology/06-iteration-loop.md`, `methodology/05-op-guards.md` |
| **Council** | Multi-perspective deliberation for high-stakes decisions that span domains or have irreversible consequences | `methodology/07-council-methodology.md` |

### Orchestrator

**What the role does.** The orchestrator is the operator-facing main agent. It receives the operator's request, plans the work, dispatches the right agents or profiles, monitors progress, surfaces decisions back to the operator, and confirms completion. The orchestrator does NOT execute the work itself (the downstream agents do that) — it routes and tracks. The orchestrator is the single agent that holds the operator's full chat history and has access to all operator-side channels (DM, in-chat surfaces).

**When to instantiate.** Always. There is no Alice implementation without an orchestrator role. Even a solo operator running Alice in its smallest form has at least one orchestrator profile.

**When it can be combined.** For very small systems, the orchestrator can be combined with the verifier (the orchestrator checks its own work before reporting back) — but this combination is fragile and only suitable for one-offs or very early validation. Combining orchestrator with planner or BA is acceptable for a tiny solo operator; combining orchestrator with council defeats the purpose of independent deliberation.

**Canonical deeper file.** `methodology/06-iteration-loop.md` (the dispatch / verify / surface loop); `methodology/03-decide-agents.md` Part 12 (orchestrator as one of the 4 agent types).

### Business Analyst (BA)

**What the role does.** The BA captures an operator-ask and turns it into a spec or ticket body that downstream agents can act on. The BA's job is to ask clarifying questions early (so the spec is right before work begins), draft the spec from the operator's words, and gate-keep spec quality. The BA does NOT execute the spec; the BA hands the spec to the orchestrator or directly to a downstream agent for execution.

**When to instantiate.** When the operator's asks have any ambiguity or could be interpreted multiple ways. If the operator's asks are always single-sentence with one obvious interpretation, the BA role can be combined with the orchestrator. If the operator's asks are multi-step or have stakeholders with conflicting requirements, instantiate the BA.

**When it can be combined.** The BA is commonly combined with the orchestrator on small systems ("the orchestrator does its own BA pass before dispatching"). Splitting BA from orchestrator is worth the cost when the operator's volume is high enough that asking clarifying questions slows the loop, or when the BA's output is consumed by multiple orchestrators.

**Canonical deeper file.** `methodology/04d-decide-flow-spec.md` (spec writing), `methodology/04d-decide-flow-derivation.md` (deriving a ticket spec from an operator flow).

### Planner

**What the role does.** The planner watches the dispatch stream for recurring role patterns. When the planner detects a pattern that meets all four scaffold criteria (recurs 5+ times/week, has a defined domain, has different skills than existing agents, has a different memory footprint), the planner scaffolds a new agent profile. The planner is also the canonical "role-watcher agent" from Part 13 (Path A) — it has the authority to create new agent profiles autonomously. The planner does NOT execute the work being dispatched; the planner shapes the system that executes the work.

**When to instantiate.** When you have at least one orchestrator AND you want the system to scale without the operator bottlenecking on every new role detection. If you are a solo operator running 5-10 tasks/week and naming agents manually, you may not need a planner yet — but you will hit the wall when task volume doubles.

**When it can be combined.** The planner can be combined with the orchestrator on small systems ("the orchestrator does its own pattern detection weekly"), but this combination breaks down at higher volume. Combining planner with verifier defeats the role separation (the planner should not be checking its own scaffolding decisions); combining planner with council is fine for periodic "should we scaffold a new role?" reviews.

**Canonical deeper file.** `methodology/03-decide-agents.md` Part 13 (Path A — the role-watcher agent); `methodology/04a-decide-work-graph.md` (the work graph substrate the planner watches).

### Verifier

**What the role does.** The verifier checks completed work against the ticket's acceptance criteria (or, in Alice's terms, the spec's "Stop condition" and "Verifier-gated" sections). The verifier is the last gate before downstream consumption — its pass-or-fail verdict determines whether the work ships, gets sent back for rework, or surfaces to the operator for a decision. The verifier does NOT execute the work; the verifier checks it.

**When to instantiate.** Almost always. Even small Alice implementations benefit from a verifier role because it catches drift and rework loops before they cost more than they save. If you find yourself re-doing work that "looked done" but wasn't, you need a verifier.

**When it can be combined.** The verifier is the role most commonly combined with the orchestrator on the smallest systems ("the orchestrator self-verifies before completing"). Splitting verifier from orchestrator is worth the cost the moment you have any volume, because the orchestrator's self-verification bias is the dominant failure mode for solo operators. Combining verifier with planner is acceptable for periodic "did our scaffolding work?" checks.

**Canonical deeper file.** `methodology/06-iteration-loop.md` (the verify step in the dispatch loop); `methodology/05-op-guards.md` (verifier-as-gate pattern for operational-guard enforcement).

### Council

**What the role does.** The council is a multi-seat deliberation on a single question or proposal. Each seat has a perspective (canonical Alice: strategist, engineer, operator, skeptic; plus optional product). The seats deliberate independently and produce a verdict that the orchestrator then acts on. The council is NOT a vote; the council is structured disagreement that surfaces agreements and disagreements, then the orchestrator (or operator) decides.

**When to instantiate.** Only at high-stakes decision points: irreversible changes, cross-domain decisions, observed failure patterns that need structured review, or major scope expansions. A solo operator who runs Alice for one project may never need a council; a multi-operator team making platform decisions needs one monthly. Convening a council for every decision is over-deliberating; convening one never is reckless.

**When it can be combined.** The council is the LEAST combinable role — combining council with any single-agent role defeats the purpose of multi-perspective deliberation. The closest acceptable combination is a "lightweight council" where the same agent runs 2-3 perspectives in separate sessions (and this is explicitly noted as a degraded mode, not a substitute). For real deliberation, the council must be multiple agents with independent contexts.

**Canonical deeper file.** `methodology/07-council-methodology.md` (full council methodology, including the 4-seat minimum, the 3-step process, and the "when NOT to convene" criteria).

### The starting point (smallest viable roster)

Operators with smaller systems may start with **just orchestrator + verifier** and add the others as needed. Alice's methodology names the roles; the operator's tool provides the mechanism.

A reasonable expansion path as the system grows:

1. **Solo, low-volume (5-10 tasks/week).** Orchestrator + verifier (combined). Add BA when operator-asks start to have ambiguity.
2. **Solo, medium-volume (10-30 tasks/week).** Orchestrator + BA + verifier, all potentially combinable into 1-2 profiles if volume stays predictable.
3. **Multi-domain or multi-project.** Orchestrator + BA + planner + verifier as separate profiles. Council convened quarterly or per major scope expansion.
4. **Multi-operator or platform-scale.** All 5 roles as separate profiles. Council is a standing capability (one of the 5, not a one-off).

### How the roster interacts with the rest of the methodology

- **vs. Part 12 (agent typology).** The 5 roles are functional descriptions; Part 12's 4 agent types (orchestrator, downstream agent, spawned sub-agent, default profile) are implementation mechanisms. A single role can be implemented by any of the 4 agent types. The roster names **what work** needs doing; Part 12 names **how that work runs**.
- **vs. Part 13 (two-path creation).** Roles in the roster are scaffolded via Path A (planner detects the pattern and scaffolds) or Path B (operator requests via the agent-resources board). The roster is the **what**; Path A/B is the **how**.
- **vs. the 4-step scaffold process.** The roster is the starting point, not the destination. The 4-step process (Observe → Pattern → Scaffold → Validate) still governs each new role's emergence. The roster is your hypothesis; the system may grow beyond the 5 if a new pattern emerges, or contract below the 5 if you combine roles safely.

### Anti-patterns to watch for

1. **"I must instantiate all 5 profiles or my system is incomplete."** → No. The roster is a starting point and a checklist for "is the function present?" — not a forced minimum instance count. A solo operator with a combined orchestrator-verifier profile has both functions present; the instance count is 1, not 2.
2. **"I have 8 profiles and they all do the same thing."** → You over-scaffolded. Map each profile to one of the 5 roles; if the mapping is unclear or 2 profiles map to the same role, consolidate.
3. **"My planner and orchestrator are the same profile and it's fine."** → Probably not, at scale. Once the planner is creating new profiles, the orchestrator executing work, and both reporting back to the operator, the contexts diverge and the role combination will start producing bias. Split them.
4. **"I never convene a council."** → Either your system is very small (fine) or you are making irreversible decisions without structured review (not fine). Track which decisions you make without a council; if any of them turned out wrong and were hard to reverse, you needed a council.
5. **"I convene a council every week."** → Over-deliberating. Councils are for high-stakes decisions, not cadence. Most weeks should not have a council.

---

## Part 15: Maintenance

An agent roster is structural — the four scaffold conditions (5+ weekly role recurrence, defined domain, different skills, distinct memory footprint) are what justify each profile's existence. Stale roles are tolerable; scaffolded roles that no longer earn their keep are not. Without an explicit maintenance cadence, the roster grows: profile count creeps up, SOULs go out of date, two profiles end up with overlapping triggers, and the operator ends up with a "default" pattern that is supposed to be the catch-all but is actually one of six overloaded roles. This section gives the methodology a self-audit loop.

The maintenance loop is operator-led, runs on a fixed cadence, and has explicit drift signals that trigger action. It is not a passive review; it is a check that produces a verdict per subsection and a named corrective action when a verdict is `revise`.

### 15.1 Audit cadence

Audit the agent roster every **90 days**. The cadence is structural rather than project-driven: a single quarterly sweep over the active profiles (per `methodology/03c-decide-agent-communication-channels.md`'s agent-roster concept) catches drift before it accumulates past one quarter of work. The audit is a read pass — it does not modify any profile; it produces a list of drift items (per 15.3) and an action queue (per 15.4).

A 90-day cadence is chosen because:

- Shorter intervals (e.g., 30 days) produce noise — most roles are stable across a single quarter.
- Longer intervals (e.g., 180 days) let two quarters of drift accumulate before anyone notices.
- The cadence aligns with the quarterly vault and skill maintenance cadences elsewhere in Alice's methodology.

**Audit-sooner triggers:**

- A worker `kanban_block`s on a profile that lacks a clear SOUL definition.
- Two profiles receive the same dispatch within a 30-day window (overlap signal).
- The operator catches themselves wishing they could combine two profiles into one.
- A council review or retro names agent over-scaffolding as a contributing cause.

### 15.2 Quality threshold

An agent profile passes the 90-day audit when **every link in the profile's contract holds**:

- The profile has a SOUL.md on disk (per `templates/agent-soul.md.template`), and the SOUL is **≥ 100 lines** — short SOULs describe roles that have not been validated; long SOULs describe roles that have drifted into encyclopedias.
- The SOUL's **Boundaries** section names what the agent does NOT do. A profile with no boundaries has no role.
- The SOUL's **Tools** and **Skills** sections are non-empty AND list specific tools / skills, not generic placeholders ("use the right tool").
- The SOUL specifies **trigger conditions** — explicit phrases or dispatch signals that mean "load this profile." A profile without triggers is loaded for everything or for nothing.
- The profile is **invoked at least weekly** in the dispatch stream. A profile that has not been claimed in 7+ days is dormant; treat dormant profiles as drift candidates (see 15.3) before treating them as retired (see 15.5).
- The profile's `last_reviewed` frontmatter field (or equivalent) is **≤ 90 days stale**. A SOUL that has not been touched in a quarter has not been validated.

If any link is missing, the profile has a hole — record it under 15.3 and act on it under 15.4.

### 15.3 Drift signals

The audit hunts for four classes of drift in the agent roster:

1. **Stale SOUL.** A SOUL.md whose `last_reviewed` (or `updated`) field is 90+ days stale. The profile is alive on paper; the role description has not been re-validated against actual dispatch behaviour.
2. **Under-invoked profile.** A profile invoked < 1×/week in the rolling 30-day window. The profile exists; the work does not justify it. The signal is not automatically retirement — it is a maintenance item (per 15.4) that may resolve into retirement (per 15.5) only after the 30-day consecutive-zero-invocations threshold.
3. **Overlapping triggers.** Two or more profiles are loaded for the same dispatch signal — i.e., the dispatcher's router cannot pick one. The SOULs have drifted to claim the same role; consolidate via the overlap-detection algorithm in `methodology/02-decide-skills.md §3`.
4. **SOUL bloat.** A SOUL.md has grown past 500 lines without a corresponding role-narrowing. The role description has accumulated exceptions and edge cases; rewrite to the core role and let the exceptions live in skills or in the orchestrator's judgment.

A drift signal is not a bug — it is a maintenance item. The audit logs each one with its profile id, the drift class, and the date the drift was first observed.

### 15.4 Fix actions

When drift is detected during the 90-day audit, apply the matching fix:

- **Stale SOUL.** Re-run the **4-step scaffold process** (Observe → Pattern → Scaffold → Validate; see "The 4-step process" earlier in this doc) for the affected profile. If the role still recurs with a stable domain, refresh the SOUL with the audit's findings and bump `last_reviewed`. If the role no longer recurs, transition the profile to the retirement queue (15.5).
- **Under-invoked profile.** Distinguish dormancy from abandonment. A profile dormant for 7-29 days is **on hold** — record the dormancy, do not retire. A profile dormant for 30+ consecutive days is a **retirement candidate** (15.5). Before retiring, check the dispatch log: is the work landing on `default` instead? If yes, the profile is dormant because it lost its role; retire it. If no, the profile is dormant because nothing matched its triggers; tighten the SOUL's trigger conditions or merge with a sibling profile.
- **Overlapping triggers.** Apply the overlap-detection algorithm from `methodology/02-decide-skills.md §3` — generate candidate pairs, compare SOUL sections, then merge (one profile absorbs the other's domain) or supersede (the older profile's role is retired in favour of the newer). The agent roster inherits the same overlap policy as skills.
- **SOUL bloat.** Extract the exceptions into skills (the profile owns the role, the skills own the edge cases) or into the orchestrator's routing logic. The SOUL returns to the **≥ 100 lines, ≤ 500 lines** band.

**When multiple profiles overlap with `default`** (operator files 5+ tasks/week on `default` that look like the same role), the fix action is to **scaffold a new profile** following the 4-step process — do not let `default` accumulate a hidden role. The audit is the right time to act on accumulated drift. Do not wait for a verifier or an operator to flag it — drift accumulates quietly and a missed quarter becomes a roster that no longer matches the work.

### 15.5 Retirement conditions

Retire a profile (archive the SOUL.md, remove the profile from the active roster, and stop dispatching to it) when **either** of the following holds:

- **Zero invocations for 30 consecutive days.** The profile has not been claimed by the dispatcher for a full month. The role no longer recurs in the dispatch stream; the profile is dead weight. Archive with a one-line reason in the body ("Archived 2026-XX-XX: 30 days dormant, no work matched the trigger conditions") so future readers can find the successor.
- **Explicit merge decision.** The operator or a council verdict decides that this profile's role is now covered by another profile. The merge target takes over; the source profile is archived with a pointer to the successor ("Merged into `planner` on 2026-XX-XX; see archived SOUL for the role history").

**What "retire" means in practice.** The SOUL.md moves from `status: active` to `status: archived`. The profile is removed from the dispatcher's roster. New work that would have routed to this profile routes to `default` or to the merged-into profile. The retirement is reversible — un-archive the SOUL and add the profile back to the roster if the role re-emerges.

**What retirement is NOT.** Retirement is not deletion of the SOUL.md — the file stays on disk as a historical reference. Retirement is not a stealth simplification — the operator (or the planner, per Part 13 Path A) must approve the proposal before the profile's status changes. Retirement is not a one-time decision — if the merged-into profile also fails, the operator can revert by un-archiving this profile.

**The default is to fix, not retire.** Drift in a profile is usually a symptom of a SOUL that has not been re-validated (stale) or a role that has shifted (overlap). Most maintenance passes end with a verdict of `revise`, not `retire`. The retirement conditions above are a guardrail against a profile that no longer earns its role, not an excuse to skip the 4-step scaffold process.

---

