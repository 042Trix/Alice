---
id: alice-methodology-01a-decide-memory
created: 2026-08-04T12:30:00Z
title: "Methodology 01a — Decide your memory tiers (the substrate of the methodology)"
type: methodology
status: draft
source: alice-framework
tags: [kind:methodology, kind:memory, project:alice]
confidence: 0.0
links: ["[[methodology/01-decide-vault-tier.md]]", "[[methodology/02-decide-skills.md]]", "[[methodology/03-decide-agents.md]]", "[[methodology/04-decide-crons.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[methodology/04b-decide-board-routing.md]]", "[[methodology/05-strike-rules.md]]", "[[methodology/06-iteration-loop.md]]", "[[methodology/07-council-methodology.md]]", "[[methodology/08-inbox-route.md]]", "[[templates/memory-budget.md.template]]"]
---

# Methodology 01a — Decide your memory tiers

> **Memory is the substrate of every other methodology doc.** The work-graph holds the work. The board-routing holds the file-this-ticket decisions. **Memory holds the operator's mental model of their own system.** Without a memory methodology, the friend has no way to design AGENTS.md (the cold-start file every agent reads on launch), the agent SOUL (the agent's identity and operating rules), the kanban event log, the vault atomic notes, or the operator preferences file.

This methodology covers **the design of a memory tier system** — what tiers exist, what goes in each, how memory is owned, recalled, audited, and budgeted. The methodology is tool-agnostic. The canonical implementation in the worked examples uses the kanban + vault + AGENTS.md pattern. The tool-mapping guide (`references/tool-mapping-guide.md`) says how to implement this in your specific tool.

**Documentation convention:** Alice documents the **method**, not specific instances. This doc does not list any specific memory files or paths. The friend applies the method to their own context.

---

## Part 1: Why memory matters

### The decision

Every agent session has a **memory footprint**. The operator (or the agent) decides:
- **What does the agent know on cold-start?** (the AGENTS.md content)
- **What does the agent know how to do?** (the skill set)
- **Who is the agent?** (the SOUL)
- **What has happened?** (the audit lines, kanban events, council verdicts)
- **What does the operator know?** (the vault atomic notes)
- **What does the agent know about the operator?** (the user.md / MEMORY.md)
- **What has the system learned?** (the cron's seen-state, kanban indexes)

Without a memory tier system, the answer is implicit. The agent loads whatever is convenient, the operator writes whatever comes to mind, and the system state is whatever happens to be in the database. The system can't reason about "what does the agent know?"

### The 4 failure modes

A memory system fails in 4 predictable ways:

1. **Context window bloat.** Loads too much on cold-start, exceeding context budget.
2. **Stale memory.** Recalls superseded corrections, reintroducing resolved failures.
3. **Conflicting memory.** Multiple sources disagree, forcing operator-side disambiguation.
4. **Infinite accumulation.** Unbounded growth, no archival cadence, no trim.

The memory tier system is the design that prevents all 4.

---

## Part 2: The 7 memory tiers

Alice recommends a 7-tier memory system. Each tier has a specific purpose, a specific owner, a specific lifecycle, and a specific budget.

### Tier 1: Cold-start memory

**What:** the AGENTS.md. What the agent reads on launch when the working directory is the vault root.

**Owner:** operator.

**Lifecycle:** stable. Edited rarely (when the system changes). Each edit is a deliberate decision.

**Budget:** ≤20K characters total. ≤2K characters per section. The cold-start is read on EVERY launch; bloating it bloats every session.

**Storage:** a single file at the vault root or workspace root (depending on the tool).

**Recall trigger:** cold-start (every agent launch).

**When to add:** when a new operating rule emerges that the agent must know on every launch.

**When to remove:** when the rule is no longer operative, or when it's been moved to a different tier.

### Tier 2: Procedural memory (skills)

**What:** skills. What the agent knows how to do.

**Owner:** agent (each agent loads its own skills based on the soul).

**Lifecycle:** SOP first → promote to skill → ship v1 → revise as needed → archive when obsolete.

**Budget:** ≤30 skills loaded per session. ≤50 skills total in the system (across all agents).

**Storage:** per-skill file in your tool's skills directory (path varies by tool; see `references/tool-mapping-guide.md` for per-tool paths).

**Recall trigger:** the skill's trigger conditions (defined in the skill-brief).

**When to add:** when a procedure recurs weekly AND is bounded AND verifiable.

**When to remove:** when the procedure is no longer needed, or when it's been absorbed into another skill.

### Tier 3: Identity memory (SOUL)

**What:** agent SOUL. Who the agent is — name, role, boundaries, tools, audit-line format.

**Owner:** operator (the SOUL is operator-authored; the agent reads but doesn't write).

**Lifecycle:** stable. Edited rarely (when the agent's role changes). The SOUL is the agent's contract with the operator.

**Budget:** ≤1-2 pages total. ≤500 characters per section.

**Storage:** per-agent identity file in your tool's agent-profile directory (path varies by tool; see `references/tool-mapping-guide.md`).

**Recall trigger:** cold-start (loaded with the agent's session).

**When to add:** when a new agent is scaffolded. The SOUL is the first artifact; the agent isn't real without it.

**When to remove:** when the agent is archived. The SOUL is the agent's identity.

### Tier 4: Episodic memory (events)

**What:** audit lines, kanban events, council verdicts. What happened.

**Owner:** system (cron-filed) + agent (audit lines) + operator (council verdicts).

**Lifecycle:** append-only. Audit lines never edited; kanban events immutable; council verdicts are versioned but never deleted.

**Budget:** unlimited in storage. ≤3-5 audit lines per session surface (don't flood the operator). ≤1000 kanban events per task lifecycle.

**Storage:** your tool's audit log + your work-graph event store + cross-agent council output directory.

**Recall trigger:** on-demand (operator queries the log; the cron queries the events; the agent queries prior verdicts).

**When to add:** when something happens that needs to be auditable (every tool call, every kanban transition, every council verdict).

**When to remove:** never. Episodic memory is the audit trail. Archive after 1+ years but never delete.

### Tier 5: Long-form memory (vault atomic notes)

**What:** the vault atomic notes. What the operator knows.

**Owner:** operator.

**Lifecycle:** edit freely while drafting. Append-only after first write in the literature tier. Edit freely in the atomic tier.

**Budget:** unlimited. Vault grows to thousands of notes.

**Storage:** the vault directory (e.g., `2-atomic/` for atomic notes, `1-literature/` for external sources per the naming chosen in `methodology/01-decide-vault-tier.md`).

**Recall trigger:** on-demand (FTS5 full-text search query, manifest lookup, wikilink traversal).

**When to add:** when the operator has a new idea, a new rule, a new decision, a new external source to reference.

**When to remove:** archive, never delete. Move to your **archive tier** (e.g., `4-archive/` per the naming chosen in `methodology/01-decide-vault-tier.md`) after the note is no longer active.

### Tier 6: Operator preferences

**What:** user.md / MEMORY.md. Environment facts, recurring corrections, durable preferences.

**Owner:** operator.

**Lifecycle:** stable. Edited when the operator's preferences change or when the agent learns a recurring correction.

**Budget:** ≤2-3K characters total. Operator preferences are persistent and high-signal; bloating them dilutes the signal.

**Storage:** a single file at the vault root or operator-home root (depending on the tool).

**Recall trigger:** cold-start (loaded with the AGENTS.md).

**When to add:** when the operator states a preference, corrects a recurring mistake, or surfaces a stable environment fact.

**When to remove:** when the preference is no longer operative, or when it's been moved to a strike rule (Tier 8 hybrid).

### Tier 7: System memory (seen-state, indexes)

**What:** cron's seen-state, kanban indexes, FTS5 manifest, embeddings. What the system has learned.

**Owner:** system (cron-filed) + agent (indexer).

**Lifecycle:** regenerated on schedule. The seen-state is updated on each cron run; the indexes are rebuilt when the source changes.

**Budget:** unlimited in storage; size constraint is the query/index, not the agent.

**Storage:** your tool's system-state directory (path varies by tool; see `references/tool-mapping-guide.md` for seen-state and per-tool conventions). Manifest / index directories such as `_index/` may sit beside the vault.

**Recall trigger:** on every cron run (for seen-state) or on every query (for indexes).

**When to add:** automatically, when the system needs to track state.

**When to remove:** when the state is no longer relevant (cron rotates its seen-state file periodically).

---

## Part 3: The memory budget rule

Every agent has a **per-session memory budget**. The budget is the sum of:

| Tier | Per-session budget |
|---|---|
| Cold-start | ≤20K characters (read on every launch) |
| Procedural | ≤30 skills loaded per session |
| Identity | ≤1-2 pages (the SOUL) |
| Episodic | ≤3-5 audit lines per session surface |
| Long-form | queryable on-demand; not loaded all at once |
| Operator preferences | ≤2-3K characters (read on launch) |
| System | unlimited in storage; size constraint is query/index |

**Exceeding the memory budget is a strike-rule violation.** The agent must offload to a smaller tier or split the memory footprint across more tiers.

### The 5-question memory audit

Run the 5 questions on each tier quarterly (or when the agent is feeling slow):

1. **Is this tier bloated?** (Cold-start > 20K? Skills > 30 per session? Operator prefs > 3K?) → trim.
2. **Is this tier stale?** (SOUL > 1 year without revision? Strike rule > 6 months without incident?) → review or archive.
3. **Is this tier conflicting?** (AGENTS.md says X, SOUL says Y?) → reconcile.
4. **Is this tier being used?** (Skill loaded 0 times in 90 days?) → archive.
5. **Is this tier the right home?** (Episodic event should be a long-form note?) → migrate.

---

## Part 4: Memory ownership and recall

### The ownership rule

Each tier has one owner:

| Tier | Owner |
|---|---|
| Cold-start | operator (operator writes, agent reads) |
| Procedural | agent (operator scaffolds, agent loads) |
| Identity | operator (operator writes the SOUL) |
| Episodic | system (cron/agent writes, all read) |
| Long-form | operator (operator writes, all read) |
| Operator preferences | operator (operator writes, agent reads) |
| System | system (cron writes, all read) |

A tier with **multiple owners** is a tier with conflicting writes. The AGENTS.md has one owner (operator); the SOUL has one owner (operator); the audit lines have one owner (the system). **A tier that anyone can write to is a tier no one owns.**

### The recall rule

Each tier has explicit recall triggers:

| Tier | Recall trigger |
|---|---|
| Cold-start | cold-start (every launch) |
| Procedural | skill trigger conditions |
| Identity | cold-start (loaded with session) |
| Episodic | on-demand query |
| Long-form | on-demand query |
| Operator preferences | cold-start (loaded with AGENTS.md) |
| System | on-demand query or cron run |

A tier without a recall trigger is a tier the agent never reads. Cold-start is read on every launch (trivially). Skills are read when the trigger conditions match. Episodic and long-form are read on-demand. **If the agent never reads the tier, the tier is dead memory.**

---

## Part 5: Memory lifecycle

Each tier has a lifecycle:

### Cold-start lifecycle

1. **Bootstrap:** the operator writes the AGENTS.md from a template (see `templates/AGENTS.md.template`).
2. **Stable:** the AGENTS.md is edited rarely. Each edit is a deliberate decision.
3. **Revise:** when the system changes (new tier, new rule, new board), update the AGENTS.md.
4. **Archive:** never. The AGENTS.md is the operating manual.

### Procedural lifecycle

1. **SOP first:** write a 1-page SOP. Follow it manually 3+ times.
2. **Promote:** when the SOP stabilizes, write a skill-brief (see `templates/skill-brief.md.template`).
3. **Ship:** the agent loads the skill when the trigger conditions match.
4. **Revise:** when the procedure changes, update the skill.
5. **Archive:** when the procedure is no longer needed, move the skill to a `_archive/` directory.

### Identity lifecycle

1. **Bootstrap:** the operator writes the SOUL from a template (see `templates/agent-soul.md.template`).
2. **Stable:** the SOUL is edited rarely.
3. **Revise:** when the agent's role changes, update the SOUL.
4. **Archive:** when the agent is archived, the SOUL is archived with it.

### Episodic lifecycle

1. **Append:** when something happens, append an audit line / kanban event / council verdict.
2. **Query:** the operator or agent queries on-demand.
3. **Rotate:** after 1+ years, archive old events to a long-term store.
4. **Delete:** never. Episodic memory is the audit trail.

### Long-form lifecycle

1. **Draft:** the operator writes a note in atomic tier.
2. **Promote:** when the note is stable, promote to literature (if it's an external source) or keep in atomic (if it's the operator's own idea).
3. **Edit:** edit freely while drafting; append-only after first write in literature.
4. **Archive:** when the note is no longer active, move to your **archive tier** (e.g., `4-archive/` per the naming chosen in `methodology/01-decide-vault-tier.md`).

### Operator preferences lifecycle

1. **Bootstrap:** the operator writes user.md / MEMORY.md from scratch or from a template.
2. **Update:** when the operator states a preference, corrects a recurring mistake, or surfaces a stable environment fact.
3. **Review:** quarterly, run the 5-question memory audit.
4. **Archive:** when the preference is no longer operative, remove from the file (don't archive).

### System lifecycle

1. **Bootstrap:** the system creates the seen-state / index file on first run.
2. **Update:** on every cron run (seen-state) or on every rebuild (index).
3. **Rotate:** periodically (cron rotates its seen-state file; indexer rebuilds from source).
4. **Archive:** when the system is retired, the state is archived with it.

---

## Part 6: Memory anti-patterns

### Anti-pattern 1: "I'll put everything in the AGENTS.md."

No. The AGENTS.md is cold-start memory. It bloats the context window on every launch. Keep it ≤20K chars. Move detail to skills or long-form notes.

### Anti-pattern 2: "I'll write a skill for every procedure."

No. Skills are for procedures that recur weekly, are bounded, and verifiable. Most procedures are not skills; they're SOPs or one-offs.

### Anti-pattern 3: "I'll write a long SOUL."

No. The SOUL is the agent's contract. Keep it ≤1-2 pages. Long SOULs are a sign the agent's role is unclear.

### Anti-pattern 4: "I'll let audit lines accumulate indefinitely."

Audit lines have a budget (≤3-5 per session surface). Unlimited audit lines dilute the signal. Rotate after 1+ years.

### Anti-pattern 5: "I'll keep all operator preferences in the agent's prompt."

Operator preferences are ≤2-3K characters. They are cold-start memory, not running memory. Keep them in user.md / MEMORY.md.

### Anti-pattern 6: "I'll have multiple owners for a memory tier."

No. Each tier has one owner. Multiple owners produce conflicting writes.

### Anti-pattern 7: "I'll never archive memory."

No. Memory has a lifecycle. Stale memory is dead memory. Archive or remove as needed.

### Anti-pattern 8: "I'll never query episodic memory."

No. Episodic memory is the audit trail. If no one queries it, it's dead memory.

### Anti-pattern 9: "I'll have a skill for every one-off."

No. One-offs are not skills. They're inline prompts or operator-action tickets.

### Anti-pattern 10: "I'll write the operator preferences in the AGENTS.md."

No. The AGENTS.md is the operating manual. Operator preferences are a separate file. Keep them apart.

---

## Part 7: The memory-budget template

Use `templates/memory-budget.md.template` to define the per-tier memory budget for each agent. The template has 7 sections, one per tier. Each section specifies:

- The tier's purpose (1 sentence)
- The tier's owner (operator/agent/system)
- The tier's budget (size or count)
- The tier's recall trigger
- The tier's lifecycle (when to add/remove)

The friend fills in this template for each agent in their fleet.

---

## Part 8: Memory and the work graph

The work graph (methodology 04a) lives in **episodic memory** (kanban events) and **long-form memory** (vault atomic notes about the graph).

The agent reads the kanban event log to reconstruct the graph. The agent reads the vault atomic notes to understand the graph's history. The graph itself is the sum of these memory tiers.

### The graph + memory integration

- **State machine:** the state is in the kanban DB (system memory)
- **Parent/child:** the parent_id is in the kanban DB (system memory)
- **Cascade history:** the cascade events are in the kanban event log (episodic memory)
- **Graph rationale:** the agent's rationale for the graph decisions is in long-form memory (vault atomic notes)

The agent's view of the graph is the union of these memory tiers. A missing tier is a missing view.

---

## Part 9: Memory and the board-routing system

The board-routing system (methodology 04b) lives in **cold-start memory** (AGENTS.md board-routing section) and **system memory** (the kanban board DB).

The agent reads the AGENTS.md to recall the routing rules. The agent queries the kanban DB to find the right board. The agent writes audit lines (episodic memory) for routing decisions.

### The routing + memory integration

- **Routing rules:** the keyword-routing table is in cold-start memory (AGENTS.md)
- **Board domains:** the board-catalog is in long-form memory (vault atomic note)
- **Routing events:** each routing decision is in episodic memory (audit line + kanban event)

The agent's view of the routing system is the union of these memory tiers.

---

## Part 10: When to revise the memory tier system

The memory tier system is **not static.** It changes when:

1. **A new memory tier emerges** (e.g., the operator adds embeddings for semantic search) → add a new tier
2. **An existing tier is over-used** (cold-start > 20K) → trim or move to a different tier
3. **An existing tier is under-used** (skill loaded 0 times in 90 days) → archive
4. **A tier's owner changes** (the operator gives the agent more autonomy) → re-assign ownership
5. **A new failure pattern emerges** (stale memory, conflicting memory) → add a strike rule

The operator revises the system **when the system is the bottleneck, not before.** A system that works for 6 months doesn't need a revision.

---

## Part 11: Memory vs context

This Part is a clarification, not a redesign. Parts 1–10 already cover the seven tiers individually. What was missing was an explicit distinction between two **roles** memory plays: the role of "remembering the operator" and the role of "remembering the work." Conflating these two roles produces memory systems where business context gets leaked into session memory, or where session memory is asked to hold state the file system should hold.

### The distinction

**Memory** is what the agent remembers about **prior work** — past decisions, council verdicts, audit lines, prior corrections, prior tool errors, prior conversation turns. Memory is **history-bound**: it answers "what happened?" and "what did we decide?" It lives in Tier 4 (episodic) and Tier 5 (long-form). Memory grows chronologically and is owned by either the system (events) or the operator (atomic notes).

**Context** is what the agent knows about **the operator's present state of being** — who the operator is, what the operator's projects are, what the operator's preferences are, what the system looks like today, what the operating rules are. Context is **state-of-the-world-bound**: it answers "where am I?" and "what is this?" It lives in Tier 1 (cold-start AGENTS.md), Tier 6 (operator preferences), and Tier 7 (system memory, when the system itself is the loadable context, e.g. an index). Context is loaded on cold-start, refreshed rarely, and owned by the operator (or the system for system-context).

The two roles are orthogonal. The same agent needs both. But they have **opposite defaults** on where the canonical copy lives:

- **Context** has its canonical copy in **versioned files**. The agent reads those files; it does not write the canonical copy. The agent may write *about* context (an audit line noting "operator mentioned they don't use X"), but that writing goes to memory (Tier 4), not back to context.
- **Memory** has its canonical copy **wherever the event happened**. A kanban event lives in the kanban DB. A council verdict lives in the council directory. A vault note lives in the vault. The agent's role is to append, query, and rehydrate — not to maintain a parallel "summarized memory" that drifts from the source.

The mistake is treating memory as a substitute for context, or context as a substitute for memory. An agent that only has memory (no cold-start file) re-learns who the operator is every conversation. An agent that only has context (no memory) re-decides the same trade-offs every session. Both fail the same way: they cannot tell what is durable (context) from what is historical (memory).

### The 5-question routing test

When you are about to write something, ask these 5 questions in order. Stop at the first yes.

1. **"Is this state-of-the-world, or is this a past event?"** If it is the operator's present (who they are, what they own, how they like to work), it is **context**. If it is something that *happened* (a decision, a correction, a tool outcome), it is **memory**.
2. **"Will this be true in 90 days even if nothing changes?"** If yes, it is **context** (it is durable). If no, it is **memory** (it is historical).
3. **"Does this need to be loaded on cold-start, or only when I query?"** If cold-start, it is **context** (Tier 1 or Tier 6). If on-demand, it is **memory** (Tier 4 or Tier 5).
4. **"Is this about the operator's identity, or about a specific incident?"** Identity facts (name, role, projects, preferences, environment) are **context**. Incident facts (the time the cron fired at 03:00, the time a council voted option B) are **memory**.
5. **"If I rewrote this fact from scratch today, would the rewrite be the same?"** If yes, it is **context** (it is the state of the world). If no (different time, different people, different outcome), it is **memory** (it is a record of a specific past state).

If all 5 answers are "I don't know," the default is **memory** — write it to an audit line or a vault note and let it be queryable. Memory is recoverable; context that was never written is not.

### What belongs in memory (NOT context)

- **Audit lines.** Every meaningful event — a tool outcome, a kanban transition, a council verdict, an operator correction, a system-state change.
- **Council verdicts.** Versioned, append-only, never deleted. Each verdict is a single decision recorded at a single moment.
- **Kanban events.** Every transition (claim, spawn, heartbeat, complete, block, resume) is an event. The agent rehydrates state by replaying events, not by trusting session memory.
- **Conversation history.** Prior turns are memory. A session transcript is memory. The agent should never assume the prior turn's facts are still in context.
- **Operator corrections and stated preferences.** *As historical record* (e.g., "operator corrected me on 2026-08-01 to use the canonical path"). The *current* preference is context; the fact that the operator has corrected the agent before is memory.
- **Decision rationale.** Why a choice was made, what was considered, what was rejected. This is not in any cold-start file; it lives in vault atomic notes (Tier 5) or council files.
- **Tool outcomes.** What tools returned, what failed, what was retried. Episodic.
- **Prior conversation summaries.** Even if compressed, they remain memory (history), not context (state).

### What belongs in context (NOT memory)

- **The operator's identity.** Who the operator is, their role, their family, their company (or lack thereof), their time-zone — these are stable for months or years.
- **The operator's project list.** What they are building, what their current priorities are, what they own. This is **stable state**, not a history.
- **The operator's preferences.** Output tone, formatting conventions, the tools they prefer, the paths they prefer. These belong in Tier 6.
- **The system's current architecture.** What agents exist, what boards exist, what skills are loaded, what the current memory budget is. This is the operating manual — Tier 1.
- **Operating rules.** Strike rules, channel discipline, the standing operating rules. These are durable constraints, not historical events. They belong in Tier 1 or in a strike-rule file the agent loads on demand.
- **Environment facts.** Host paths, tool versions, current date/time (within a session — not across sessions). Within session: context. Across session: memory.
- **Vault layout.** What tiers exist, what naming convention is used. Stable; belongs in Tier 1 cold-start or a freshly-loaded skill.

### Anti-pattern: session memory is fall-back, not source

The agent's session memory (the running context window) is a **fall-back**, not a **source of truth**. Treat it like a cache: useful for the current task, but never authoritative.

The rule: **if you are about to write to session memory, ask whether the file system can hold it instead.** If the fact is durable, write it to a file (AGENTS.md, vault note, audit line, kanban event). If the fact is ephemeral to this turn, write it to a kanban_comment so the next worker has it.

Symptoms of over-reliance on session memory:

- **"I'll remember that for next time."** No. Write it to a file. Sessions end.
- **"That's already in my context."** A fact being in context this turn does not mean it will be in context next turn. Refresh from the source file.
- **"I'll just keep that operator preference in mind."** No. Write it to user.md / MEMORY.md so the next session loads it.
- **"The next agent will figure it out from my summary."** No. The next agent has no summary. Write the audit line to the kanban DB or the vault log so the next agent rehydrates from there.

The corollary: **never trust a fact that lives only in session memory.** Treat every fact as either **durable (in a file)** or **transient (in context only, expires at session end)**. There is no in-between. A fact that is "important enough to remember" but is not in a file is a fact the next session will not have.

### How this Part relates to the tier taxonomy

This Part does not change Parts 1–10 or add an 8th tier. It maps the seven tiers to two roles:

- **Context role:** Tier 1 (cold-start AGENTS.md), Tier 6 (operator preferences), and Tier 7 (system memory, when the system is the loadable context).
- **Memory role:** Tier 4 (episodic — events, audit lines, kanban transitions) and Tier 5 (long-form — vault atomic notes about operator decisions, prior corrections, prior trade-offs).

Tier 2 (procedural skills) and Tier 3 (SOUL) are **identity-shaped**: they describe what the agent knows how to do and who the agent is. They straddle both roles — SOUL is loaded on cold-start (context role for the agent-as-entity) but is also an identity declaration (memory role for the agent-as-history). Treat them as their own category: not context, not memory, but **identity**. This is why the SOUL is loaded every session (it is identity, not state).

The practical takeaway: when deciding where a new fact belongs, do not ask "which tier?" — ask "context or memory?" first. The tier follows from the role:

- Context → Tier 1 (AGENTS.md), Tier 6 (user.md / MEMORY.md), or Tier 7 (system file).
- Memory → Tier 4 (audit line / kanban event) or Tier 5 (vault atomic note).

### See also

- `methodology/01-decide-vault-tier.md` — the vault is one tier of long-form memory (Tier 5)
- `methodology/02-decide-skills.md` — skills are procedural memory (Tier 2)
- `methodology/03-decide-agents.md` — the SOUL is identity memory (Tier 3)
- `methodology/04-decide-crons.md` — cron's seen-state is system memory (Tier 7)
- `methodology/04a-decide-work-graph.md` — kanban events are episodic memory (Tier 4)
- `methodology/04b-decide-board-routing.md` — board-catalog is long-form memory (Tier 5)
- `methodology/05-strike-rules.md` — strike rules are rule-based memory (Tier 1, loaded cold-start)
- `methodology/06-iteration-loop.md` — loop audit lines are episodic memory (Tier 4)
- `methodology/07-council-methodology.md` — council verdicts are episodic memory (Tier 4)
- `methodology/08-inbox-route.md` — inbox items are pre-routing memory (mixed: 0-INBOX is system memory, routing decisions are episodic)
- `templates/memory-budget.md.template` — the fillable per-agent budget; allocate cold-start rows carefully, since context lives there

---

## Worked example (skeleton)

The 2 worked examples (solo-founder, research-analyst) show the methodology applied to a specific instance. The examples are skeletons, not snapshots.

The friend reads the methodology, then designs their own memory tier system for their own context. The friend does NOT copy the worked example's tiers; they apply the method to derive their own.

---

## What's next

- `methodology/02-decide-skills.md` — skills are procedural memory; see `01a-decide-memory.md` for the tier
- `methodology/03-decide-agents.md` — agent SOUL is identity memory; expand the "memory footprint" bullet
- `methodology/04a-decide-work-graph.md` — kanban event log is episodic memory
- `methodology/04b-decide-board-routing.md` — board-catalog is long-form memory
- `methodology/05-strike-rules.md` — strike rules are rule-based memory
- `methodology/06-iteration-loop.md` — the loop's audit-line is episodic memory
- `methodology/07-council-methodology.md` — council verdicts are episodic memory
- `methodology/08-inbox-route.md` — inbox items are pre-routing memory
- `templates/memory-budget.md.template` — the fillable form for an agent's per-tier memory budget

## Anti-patterns to watch for (summary)

1. **"I'll put everything in the AGENTS.md."** → No. Keep cold-start ≤20K chars.
2. **"I'll write a skill for every procedure."** → No. Skills are for recurring procedures.
3. **"I'll write a long SOUL."** → No. Keep SOUL ≤1-2 pages.
4. **"I'll let audit lines accumulate indefinitely."** → No. Rotate after 1+ years.
5. **"I'll keep all operator preferences in the agent's prompt."** → No. Keep in user.md / MEMORY.md.
6. **"I'll have multiple owners for a memory tier."** → No. One owner per tier.
7. **"I'll never archive memory."** → No. Memory has a lifecycle.
8. **"I'll never query episodic memory."** → No. Episodic memory is the audit trail.
9. **"I'll have a skill for every one-off."** → No. One-offs are not skills.
10. **"I'll write the operator preferences in the AGENTS.md."** → No. Keep apart.

## Touch-points with other methodology docs

This doc touches every other methodology doc. The memory tier is the substrate that makes the methodology work. Without it, the friend has no way to design the artifacts the methodology requires.

- `methodology/01-decide-vault-tier.md` — vault is one tier (long-form)
- `methodology/02-decide-skills.md` — skills are procedural memory
- `methodology/03-decide-agents.md` — SOUL is identity memory
- `methodology/04-decide-crons.md` — cron's seen-state is system memory
- `methodology/04a-decide-work-graph.md` — kanban event log is episodic memory
- `methodology/04b-decide-board-routing.md` — board-catalog is one tier
- `methodology/05-strike-rules.md` — strike rules are rule-based memory
- `methodology/06-iteration-loop.md` — audit lines are episodic memory
- `methodology/07-council-methodology.md` — council verdicts are episodic memory
- `methodology/08-inbox-route.md` — inbox items are pre-routing memory
- `templates/memory-budget.md.template` — the fillable form for an agent's per-tier memory budget