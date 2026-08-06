---
id: alice-reference-kanban-lite
created: 2026-08-04T12:30:00Z
title: "Kanban-lite disciplines — minimal rules for a small agent fleet"
type: reference
status: draft
source: alice-framework
tags: [kind:reference, kind:kanban, project:alice]
confidence: 0.0
links: []
---

# Kanban-lite disciplines

> A small agent fleet (1-3 agents) doesn't need a full kanban. But it does need **minimal disciplines** to avoid chaos. This is the lite version — for a solo operator or a small team. For a larger fleet, see the full methodology in `methodology/04-decide-crons.md` and `methodology/06-iteration-loop.md`.

## When you need kanban

You need a kanban (lite) when:
- You have 2+ agents with overlapping work
- You have 5+ open tasks at any time
- You lose track of who's working on what

You don't need a kanban (lite) when:
- You have 1 agent and 1 operator
- You have < 5 open tasks at any time
- The operator can hold all the context in their head

## The 3 minimal disciplines

### Discipline 1: One default assignee for one-offs

Every kanban system has a `default` profile. The `default` profile is the catch-all for one-off tasks. Without `default`, you'll be tempted to scaffold an agent for every recurring role, even one-offs.

The `default` profile should be:
- Loaded for any task that doesn't match a named profile
- Allowed to use any tool (or any read-only subset, depending on your tool)
- Cheap to spin up (no per-agent tooling)

### Discipline 2: Domain-routing heuristic for boards

If you have multiple boards, route by domain. A common 5-board setup:

- **Work** — operator-actionable items (decisions, approvals, reviews)
- **Build** — agent-actionable items (skills, crons, agents)
- **Operations** — system maintenance (cron failures, dispatcher issues)
- **Content** — writing, drafting, research
- **Archive** — closed items, ready to move out

**Don't** route by agent. Boards are for domain, not for which agent does the work.

### Discipline 3: Every task has a status

The minimum status set:

- `ready` — work not started
- `running` — work in progress
- `done` — work complete
- `archived` — work moved to long-term storage

Add `blocked` and `needs_input` as you need them, but don't start with them. The 4-status minimum is enough for a small fleet.

## The 5 anti-patterns to avoid

### Anti-pattern 1: "I'll add a board for every domain."

A solo operator with 5 boards will lose track of which board has what. **Start with 1-2 boards.** Add boards when you have a clear separation need.

### Anti-pattern 2: "I'll add an agent for every recurring role."

Same as boards. A solo operator with 5 agents is over-scaffolded. **Start with 1 default profile.** Add named agents as patterns emerge (5+ weekly tasks with stable domain).

### Anti-pattern 3: "I'll add a status for every state."

A kanban with 10 statuses is harder to use than one with 4. **Start with the minimum.** Add statuses when you have a clear separation need.

### Anti-pattern 4: "I'll move tickets between boards constantly."

Moving tickets is fine for transitions (Build → Work for review). **Constant moving means the routing heuristic is wrong.** Fix the routing, not the moving.

### Anti-pattern 5: "I'll keep the kanban in my head."

A kanban in your head is a single point of failure. **Write it down.** A markdown list is fine. A spreadsheet is fine. A real kanban tool is fine. Just don't keep it in your head.

## The 3-strike-system for kanban

A small fleet doesn't need a complex kanban. You need 3 things:

1. **A default profile** for one-offs
2. **A domain-routing heuristic** for boards
3. **A status set** for tasks

That's it. Anything beyond these 3 is over-engineering for a small fleet.

## See also

- `methodology/04a-decide-work-graph.md` — the work graph is the substrate. The kanban board is the canonical implementation of the work graph; the methodology says "the work graph lives in tickets; boards are the substrate that holds the tickets."
- `methodology/04b-decide-board-routing.md` — the work-type routing system. The kanban-lite disciplines (3-5 boards, domain-routing heuristic, default profile) is the kanban-specific implementation of the board-routing methodology. **Alice documents the method, not specific instances.**

## What's next

- `tier-routing-cheatsheet.md` — quick reference for 4-tier routing decisions
- `x-ingestion-pipeline.md` — the operating spec for X post ingestion
- `tool-mapping-guide.md` — how the abstract concepts map to your tool
