---
id: alice-methodology-03c-decide-agent-communication-channels
created: 2026-08-04T12:30:00Z
title: "Methodology 03c — Decide your agent communication channels (orchestrator-isolation pattern)"
type: methodology
status: draft
source: alice-framework
tags: [kind:methodology, kind:communication-channels, project:alice]
confidence: 0.0
links: ["[[methodology/03-decide-agents.md]]", "[[methodology/03a-decide-agent-vault-interaction.md]]", "[[methodology/03b-decide-operator-agent-interaction.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[methodology/04b-decide-board-routing.md]]"]
---

# Methodology 03c — Decide your agent communication channels

> **This is the most architecturally significant methodology in Alice.** The orchestrator-isolation pattern is the **structural reason** the system scales. Without it, every downstream agent communication leaks operator context, every task-specific detail pollutes the orchestrator's context, and the chat channel becomes the bottleneck. With it, the operator's context stays clean, the downstream agent contexts stay task-scoped, and the orchestrator is a clean router.

> **Terminology:** throughout this doc, "downstream agent" refers to a persistent worker profile (per `methodology/03-decide-agents.md` Part 12). It does NOT refer to "spawned sub-agents" (ephemeral task-scoped instances of the main agent). Both follow the channel discipline; the difference is in memory model and lifecycle.

This methodology covers **the design of agent communication channels** — the 3 channels (DM, comment, kanban event), the orchestrator-isolation pattern, the DM-isolation rule, the comment-as-downstream agent-channel rule, the context-window-isolation rationale, the task-specific-info rationale, the escalation path, the orchestrator-as-router pattern, and the channel anti-patterns. The methodology is tool-agnostic. The canonical implementation in the worked examples uses the AGENTS.md + kanban + DM pattern.

**Documentation convention:** Alice documents the **method**, not specific instances. This doc does not list any specific agent or tool. The friend applies the method to their own context.

---

## Part 1: Why communication channel design matters

### The decision

When work flows between agents (or between the operator and an agent), the message must travel through a **channel**. The choice of channel determines:
- **Who sees the message** (operator, orchestrator, downstream agent, system)
- **How long the message lives** (one-shot DM, persistent comment, immutable event)
- **What context it carries** (operator context, orchestrator context, downstream agent context)
- **How the receiver responds** (back-channel, comment, kanban transition)

Without a channel design, the answers are implicit. The operator DMs the coder directly. The coder DMs the operator back. The writer asks the operator a question in a DM. Every DM bypasses the orchestrator; the orchestrator's context becomes stale; the operator's context fills with downstream agent chatter.

### The 3-channel model

Alice recommends a **3-channel communication model**. Each channel has a distinct purpose:

| Channel | Audience | Persistence | Content | Use case |
|---|---|---|---|---|
| **DM** | operator ↔ orchestrator | ephemeral | operator-facing decision / notification | "Approve this" / "I need a decision" |
| **Comment** | orchestrator, downstream agents on the ticket, system | persistent | task-specific info | "The build failed at step 3; here's the log" |
| **Kanban event** | system (audit trail) | immutable | state transitions | "ticket t_X transitioned ready → running" |

**Each channel has one job.** The DM is for operator decisions. The comment is for task-specific info. The kanban event is for system state.

### The 5 failure modes

A communication-channel system fails in 5 predictable ways:

1. **DM flooding.** The operator DMs every downstream agent. The operator context fills.
2. **Comment drift.** The orchestrator doesn't see downstream agent comments. The orchestrator's view is stale.
3. **Event starvation.** No kanban events are emitted. The audit trail is incomplete.
4. **Context leak.** Downstream agent reads operator's DM thread. The downstream agent's context pollutes with operator context.
5. **Channel confusion.** The agent uses DMs for task info, comments for operator decisions. Every message lands in the wrong place.

The channel design is the design that prevents all 5.

---

## Part 2: The orchestrator-isolation pattern

### The rule

The operator talks to **ONE main agent** — the orchestrator. Downstream agents are isolated from the operator.

```
operator  ←DM→  orchestrator  ←comment+kanban→  downstream agent(s)
```

### Why this rule

**Three reasons** the operator talks to only the orchestrator:

1. **Context isolation.** The operator's context stays clean. The operator sees only what the orchestrator chooses to surface — the operator-action items, the summary of work, the asks. The orchestrator filters.

2. **Downstream agent focus.** The downstream agent's context is task-scoped. The downstream agent sees only the task description, the comment thread, the relevant vault notes. The downstream agent doesn't need the operator's chat history.

3. **Orchestrator as router.** The orchestrator decides which downstream agent gets the task. The orchestrator dispatches via kanban. The orchestrator monitors via kanban events. The orchestrator is the system's brain.

### What the operator sees

The operator sees:
- DMs from the orchestrator (operator-action asks, summaries, alerts)
- Ticket comments the orchestrator routes to the operator (operator-action items)
- Kanban events filtered to operator-action items

The operator does NOT see:
- Downstream agent↔downstream agent communication
- Downstream agent's task-internal context (unless the orchestrator surfaces it)
- The orchestrator's internal reasoning (unless the orchestrator surfaces it)

### What the downstream agent sees

The downstream agent sees:
- The task description (from the kanban)
- The ticket comment thread (downstream agent + orchestrator + operator contributions)
- The relevant vault notes (per `methodology/03a-decide-agent-vault-interaction.md`)

The downstream agent does NOT see:
- The operator's chat with the orchestrator
- Other downstream agents' tasks
- The operator's other ongoing work

---

## Part 3: The DM-isolation rule

### The rule

**DMs happen ONLY between operator and orchestrator.** Downstream agents NEVER DM the operator directly.

```
ALLOWED:  operator ↔ orchestrator
FORBIDDEN: operator ↔ downstream agent
FORBIDDEN: downstream agent ↔ downstream agent
FORBIDDEN: orchestrator → downstream agent (use comment instead)
```

### Why this rule

If downstream agents DM the operator, three things break:

1. **Context leak.** The operator's DM thread fills with downstream agent chatter. The operator can't tell operator-action items from downstream agent chatter.
2. **Orchestrator bypass.** The downstream agent's DM bypasses the orchestrator. The orchestrator doesn't know what the downstream agent asked; the orchestrator can't route the response.
3. **Audit trail fragmentation.** Downstream agent↔operator DMs are in a different channel than ticket comments. The system can't reconstruct what happened.

### What the downstream agent does instead

When the downstream agent needs operator input:
1. **Files an operator-action ticket.** (Per the ticket lifecycle in `methodology/03b-decide-operator-agent-interaction.md` Part 3.)
2. **Adds a comment** to the ticket explaining what they need.
3. **The orchestrator notices** (via kanban event or comment thread) and DMs the operator.

The downstream agent does not DM the operator directly.

### The DM-isolation anti-patterns

- **Downstream agent DMs operator.** A downstream agent reads the operator's DM thread and sends an update. → forbidden.
- **Operator DMs downstream agent.** The operator wants to ask the coder a question directly. → forbidden; the operator asks the orchestrator, who routes.
- **Downstream agent DMs downstream agent.** Two downstream agents coordinate directly. → forbidden; the downstream agents use the ticket comment thread.

---

## Part 4: The comment-as-downstream agent-channel rule

### The rule

**Downstream agents communicate via task comments on the kanban ticket.** Downstream agents do not DM each other or the orchestrator.

### Why this rule

Task comments are the **shared memory** between the orchestrator, the downstream agent, and the operator. They are:
- **Persistent** (the comment thread is part of the ticket history)
- **Discoverable** (the operator can read the thread when reviewing)
- **Audit-able** (the kanban event log records every comment)
- **Task-scoped** (the comment is bound to the ticket; it doesn't leak to other tasks)

### What goes in a task comment

- **Status updates.** "Build started." "Build failed at step 3." "Build succeeded; awaiting review."
- **Decisions made.** "I chose library X over Y because Z." (Downstream agents document their decisions.)
- **Asks for input.** "I need a decision on X; options A and B are listed."
- **Handoffs.** "I'm passing this off to the verifier; here's what to check."
- **Findings.** "Found a bug in Y; logging ticket t_Z for follow-up."

### What does NOT go in a task comment

- **Operator context.** The operator's DM thread with the orchestrator. (The orchestrator surfaces relevant items, not the raw DM.)
- **Downstream agent↔downstream agent chatter.** Two downstream agents talking past each other in the same ticket. (Use a separate ticket or escalate to orchestrator.)
- **Long-form code or configs.** Use the vault (per `methodology/01b-decide-vault-content.md`), not the comment.

### The comment discipline

- **Comments are persistent.** Don't delete comments; archive the ticket if the thread is obsolete.
- **Comments are scoped.** One comment per topic / decision / status update.
- **Comments are addressed.** Use `@orchestrator` or `@operator` to indicate who should respond.
- **Comments are short.** ≤200 words per comment.

---

## Part 5: The kanban-event channel

### The rule

**State transitions, decisions, and significant events are recorded as kanban events.** The kanban event log is the system's audit trail.

### What goes in a kanban event

- **Ticket transitions.** `ready → running`, `running → done`, etc.
- **Operator decisions.** "Approved t_X at T."
- **Significant downstream agent actions.** "Created note n_X." "Updated `SOUL.md` (the agent's identity and operating rules file)."
- **System actions.** "Cron X fired at T." "Archivist triggered."

### What does NOT go in a kanban event

- **Conversational content.** Use comments.
- **Operator context.** Use the operator's DM thread.
- **Downstream agent reasoning.** Use comments (or the vault).

### The kanban-event discipline

- **Events are immutable.** Once written, they don't change. Corrections are new events.
- **Events are timestamped.** ISO8601-UTC.
- **Events are queryable.** The operator or agent can grep the event log for patterns.

---

## Part 6: The context-window-isolation rationale

### Why context isolation matters

The agent's context window is finite. The operator's context window is finite. Every message that lands in either context takes tokens.

**Without context isolation:**
- The operator's context fills with downstream agent chatter (every downstream agent update is a DM)
- The downstream agent's context fills with operator chat history (the downstream agent sees the operator↔orchestrator DM thread)
- The orchestrator's context fills with downstream agent comments (the orchestrator is in every comment thread)
- Every agent's context is noisy, and signal-to-noise decreases

**With context isolation:**
- The operator's context has only DM with the orchestrator (filtered)
- The downstream agent's context has only the task description + the ticket comment thread
- The orchestrator's context has DM with operator + ticket comment threads (downstream agent visible) + kanban events

Each context is scoped; signal-to-noise is high.

### The context-isolation mechanism

The mechanism is **channel discipline**, not a tool feature:

- The agent's context-builder reads from channels, not from raw operator chat
- The orchestrator filters what surfaces to the operator (via DM)
- The downstream agent filters what it reads (via ticket comments only, not operator DM)
- The kanban event log is a queryable store, not a context-input

### The context-isolation discipline

- **Operator context budget:** ≤3,000 tokens per session (per `methodology/03b-decide-operator-agent-interaction.md` Part 4).
- **Downstream agent context budget:** scoped to the task + comment thread + relevant vault notes.
- **Orchestrator context budget:** larger, but still bounded (it's the system's brain).

---

## Part 7: The task-specific-info rationale

### Why task comments hold task-specific info

Task comments are the **task-scoped shared memory**. They're bound to the ticket; they don't leak across tasks.

**Without task-scoped shared memory:**
- The downstream agent's context pollutes with details from other tasks
- The orchestrator's context fills with cross-task chatter
- The operator's DM thread becomes a global scratchpad

**With task-scoped shared memory:**
- Each task has its own comment thread
- The downstream agent reads only its own task's thread
- The orchestrator sees all task threads but routes by relevance
- The operator sees only the threads the orchestrator surfaces

### The task-specific-info discipline

- **One ticket per task.** Don't combine multiple tasks in one ticket.
- **One comment thread per ticket.** All downstream agent communication happens in the thread.
- **Task-specific notes go in the vault.** If a downstream agent discovers something reusable, write a vault note (not a comment).
- **Operator context lives in DM, not comments.** The operator's DM is the operator's; the ticket comment is the task's.

---

## Part 8: The escalation path

### The rule

When a downstream agent needs operator input, the escalation goes through the orchestrator:

```
downstream agent  →  ticket comment (asks)  →  orchestrator  →  DM (asks)  →  operator
operator  →  DM (responds)  →  orchestrator  →  ticket comment (responds)  →  downstream agent
```

### Why this rule

If the downstream agent escalates directly to the operator, the orchestrator is bypassed. The orchestrator's context becomes stale; the operator's context fills with downstream agent chatter.

If the orchestrator routes the escalation, the orchestrator stays in the loop. The orchestrator decides what to surface to the operator (filter); the operator context stays clean.

### The escalation discipline

- **The downstream agent's ask is in the comment.** Specific, with alternatives, with recommendation.
- **The orchestrator's DM to the operator is filtered.** The orchestrator surfaces only what the operator must decide.
- **The operator's response is in DM.** The orchestrator routes the response back to the downstream agent via comment.
- **The whole path is logged.** Ticket comment + DM thread + kanban event.

### The escalation anti-patterns

- **Downstream agent escalates directly to operator.** The orchestrator is bypassed. The operator context fills.
- **Operator responds directly to downstream agent.** The orchestrator is bypassed. The orchestrator's view is stale.
- **The orchestrator surfaces everything.** The orchestrator should filter; surfacing all is no better than direct downstream agent-to-operator.

---

## Part 9: The orchestrator-as-router pattern

### The rule

The orchestrator is the system's **router**. The orchestrator:
- **Receives** the operator's DM
- **Decides** which downstream agent gets the task
- **Dispatches** the task via kanban
- **Monitors** the task via kanban events
- **Surfaces** downstream agent asks to the operator via DM
- **Routes** operator responses back to downstream agents via comment

### The orchestrator's role

The orchestrator is **one of the agents** (per `methodology/03-decide-agents.md`). The orchestrator has its own SOUL, its own skills, its own memory budget.

The orchestrator's role is **coordination**, not implementation. The orchestrator:
- Does NOT do the downstream agent's work (the coder codes, the verifier verifies)
- DOES dispatch, monitor, route, surface

### The orchestrator's tools

The orchestrator has:
- **DM tool** (operator ↔ orchestrator)
- **Kanban tool** (read tickets, write tickets, transition tickets, comment on tickets)
- **Vault tool** (read relevant context)
- **Filter tool** (decide what to surface to operator)

The orchestrator does NOT have:
- Direct downstream agent communication (no DM to downstream agents)
- Bypass of the kanban (no direct downstream agent dispatch)

### The orchestrator's anti-patterns

- **The orchestrator does the work.** The orchestrator shouldn't code or verify. The orchestrator routes.
- **The orchestrator doesn't surface.** The orchestrator must surface downstream agent asks. Otherwise the operator is starved.
- **The orchestrator surfaces everything.** The orchestrator filters. Otherwise the operator is flooded.

---

## Part 10: The "downstream agent never sees operator context" rule

### The rule

Downstream agents do NOT see the operator's chat history. Downstream agents see only:
- The task description (kanban)
- The ticket comment thread (kanban)
- The relevant vault notes (per agent-vault interaction patterns)

### Why this rule

The operator's chat history contains:
- Personal preferences
- Decisions about other tasks
- Cross-cutting context

If the downstream agent sees this, three things break:

1. **Context pollution.** The downstream agent's context fills with irrelevant info.
2. **Privacy leak.** The downstream agent sees the operator's private context.
3. **Decision contamination.** The downstream agent sees the operator's reasoning and may bias its own work toward the operator's prior decisions.

### The mechanism

The downstream agent's context-builder reads from:
- The kanban ticket (description + comments)
- The vault (per permissions in `methodology/03a-decide-agent-vault-interaction.md`)

The downstream agent's context-builder does NOT read from:
- The operator's DM thread
- The orchestrator's internal context

---

## Part 11: The "operator never sees downstream agent context" rule

### The rule

The operator does NOT see the downstream agent's full context. The operator sees:
- DM from the orchestrator (filtered)
- Ticket comments the orchestrator routes to the operator
- Kanban events filtered to operator-action items

### Why this rule

The downstream agent's context contains:
- Tool calls (verbose)
- File contents (potentially large)
- Intermediate reasoning (noisy)

If the operator sees this, three things break:

1. **Context pollution.** The operator's context fills with downstream agent noise.
2. **Signal loss.** The operator can't find the operator-action items in the noise.
3. **Decision fatigue.** The operator reviews everything, not just what matters.

### The mechanism

The orchestrator **filters** what surfaces to the operator. The orchestrator's DM is a summary, not a transcript. The orchestrator's choice of what to surface is the operator-action surface.

---

## Part 12: Channel anti-patterns

### Anti-pattern 1: "Downstream agent DMs the operator."

No. Downstream agents use ticket comments. The orchestrator surfaces to the operator via DM.

### Anti-pattern 2: "Operator DMs the downstream agent."

No. The operator DMs the orchestrator. The orchestrator routes via comment.

### Anti-pattern 3: "Downstream agent DMs another downstream agent."

No. Downstream agents coordinate via the ticket comment thread (if on the same ticket) or via separate tickets routed by the orchestrator.

### Anti-pattern 4: "The orchestrator surfaces everything to the operator."

No. The orchestrator filters. The operator sees operator-action items + summaries, not raw downstream agent chatter.

### Anti-pattern 5: "The orchestrator does the downstream agent's work."

No. The orchestrator routes. The downstream agent does.

### Anti-pattern 6: "Comments are used for operator decisions."

No. Comments are for task-specific info. Operator decisions are in DM (orchestrator ↔ operator).

### Anti-pattern 7: "DMs are used for task-specific info."

No. DMs are for operator decisions. Task-specific info is in comments.

### Anti-pattern 8: "The operator's DM thread is shared with downstream agents."

No. The operator's DM thread is the operator's. Downstream agents see only the task description + comment thread.

### Anti-pattern 9: "Downstream agent sees the orchestrator's reasoning."

No. Downstream agents see the orchestrator's dispatch decisions (via kanban events + comments), not the orchestrator's internal reasoning.

### Anti-pattern 10: "The orchestrator skips the kanban and dispatches directly."

No. The kanban is the source of truth for dispatch. The orchestrator files the ticket, not direct-DMs the downstream agent.

---

## Part 13: When to revise the channel design

The channel design is **not static.** Revise when:

1. **A new channel is needed.** (e.g., a "broadcast" channel for system-wide announcements.)
2. **A channel is consistently flooded.** (The operator-action surface is too noisy.)
3. **A channel is consistently starved.** (Downstream agents aren't commenting enough.)
4. **Context isolation breaks.** (Downstream agents see operator context.)
5. **A new tool changes the channels.** (e.g., a new chat client.)

The operator revises the design **when the design is the bottleneck, not before.**

---

## Worked example (skeleton)

The 2 worked examples (solo-founder, research-analyst) show the methodology applied to a specific instance. The examples are skeletons, not snapshots.

The friend reads the methodology, then designs their own channel design for their own context. The friend does NOT copy the worked example's channels; they apply the method to derive their own.

---

## What's next

- `methodology/03-decide-agents.md` — agent design (WHO) — the orchestrator is one of the agents
- `methodology/03a-decide-agent-vault-interaction.md` — agent-vault interaction (WHAT AGENT DOES TO VAULT) — downstream agents still follow the 5 patterns
- `methodology/03b-decide-operator-agent-interaction.md` — operator-agent interaction (HOW OPERATOR INTERACTS) — the DM/discussion channel is one of 3
- `methodology/04a-decide-work-graph.md` — work graph — task comments are part of the work graph
- `methodology/04b-decide-board-routing.md` — board-routing — task comments cite the routing decisions

## Anti-patterns to watch for (summary)

1. **"Downstream agent DMs the operator."** → No. Use ticket comments.
2. **"Operator DMs the downstream agent."** → No. Use orchestrator.
3. **"Downstream agent DMs another downstream agent."** → No. Use ticket comments.
4. **"The orchestrator surfaces everything."** → No. Filter.
5. **"The orchestrator does the work."** → No. Route.
6. **"Comments are for operator decisions."** → No. Comments are task-scoped.
7. **"DMs are for task-specific info."** → No. DMs are operator-facing.
8. **"Operator's DM thread is shared."** → No. Operator-only.
9. **"Downstream agent sees orchestrator's reasoning."** → No. Downstream agents see dispatch decisions.
10. **"Orchestrator skips the kanban."** → No. Kanban is the source of truth.

## See also

- `methodology/03-decide-agents.md` — agent design (WHO) — the orchestrator is one of the agents, with a specific role (coordination, not implementation)
- `methodology/03a-decide-agent-vault-interaction.md` — agent-vault interaction (WHAT AGENT DOES TO VAULT) — downstream agents still follow the 5 patterns but only against the ticket comment thread + vault, NOT against the operator's DM
- `methodology/03b-decide-operator-agent-interaction.md` — operator-agent interaction (HOW OPERATOR INTERACTS) — the DM/discussion channel is one of 3 channels; Part 11 (DM/DM-back discipline) was updated to point to this doc
- `methodology/04a-decide-work-graph.md` — work graph — task comments are part of the work graph (sub-state of running); this doc explains how task comments are the downstream agent↔orchestrator channel
- `methodology/04b-decide-board-routing.md` — board-routing — task comments cite the routing decisions; cross-board moves are documented in the comment thread

## Touch-points with other methodology docs

This doc touches:

- `methodology/03-decide-agents.md` — agent design (WHO) — the orchestrator is one of the agents
- `methodology/03a-decide-agent-vault-interaction.md` — agent-vault interaction (WHAT AGENT DOES TO VAULT) — downstream agents still follow the 5 patterns; the orchestrator's interaction patterns may be broader
- `methodology/03b-decide-operator-agent-interaction.md` — operator-agent interaction (HOW OPERATOR INTERACTS) — the DM/discussion channel is one of 3
- `methodology/04a-decide-work-graph.md` — work graph — task comments are part of the work graph (sub-state of running)
- `methodology/04b-decide-board-routing.md` — board-routing — task comments cite the routing decisions