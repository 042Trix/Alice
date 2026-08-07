---
id: alice-example-solo-founder-methodology
created: 2026-08-04T12:30:00Z
title: "Methodology notes — solo founder archetype (anonymized)"
type: worked-example
status: draft
source: alice-framework
tags: [kind:worked-example, kind:methodology-notes, project:alice, archetype:solo-founder]
confidence: 0.0
links: []
---

# Methodology notes — solo founder archetype

> **Use this example to see how a solo founder can apply Alice's methodology** when managing 2-3 active projects, one income-replacement target, and a small agent fleet. It shows the resulting decision pattern, not a snapshot of a specific operator's setup.

For each methodology decision covered by this example, you will see:
- **What the solo founder typically decides** (the typical answer)
- **What they typically skip** (the optional parts)
- **What they typically add** (the operator-specific extensions)

Treat these notes as **a starting structure, not a prescribed destination.** Apply the methodology to your own context; the example shows one decision pattern, while the implementation choices remain operator-specific.

---

## 00-decide-memory

**Typical decision:** 7-tier memory system (cold-start, procedural, identity, episodic, long-form, operator preferences, system). See `methodology/01a-decide-memory.md` for the full design methodology.

**Why 7 tiers:** the solo founder has enough memory complexity to need a unified taxonomy. Without it, AGENTS.md (the cold-start file every agent reads on launch) bloat, stale SOULs (each agent's identity and operating rules), and conflicting operational guards accumulate silently.

**What they skip:** embeddings and semantic-search indices (the solo founder doesn't have enough literature to need them).

**What they add:** a **memory-budget template** per agent. The template defines per-tier size/count constraints, ownership, recall triggers, and lifecycle. Use `templates/memory-budget.md.template`.

**What they typically use the memory tier system for:**
- "Is my AGENTS.md bloated?" — quarterly audit on Tier 1 (≤20K chars)
- "Is this skill still useful?" — quarterly audit on Tier 2 (≤30 per session, ≥1 load/90 days)
- "Did the agent remember the operator's preference?" — Tier 6 (operator preferences, ≤2-3K chars)

## 00b-decide-vault-content

**Typical decision:** frontmatter schema (8-10 required/optional fields) + 4-5 tag namespaces (domain, project, kind, source, private) + 7-char note IDs + ID-based wikilinks + capture-triage-process-mature-stale-archive lifecycle + 7-day backups.

See `methodology/01b-decide-vault-content.md` for the full design methodology.

**Why this:** the solo founder has enough notes to need a unified content-management discipline. Without it, frontmatter bloat, tag proliferation, and broken wikilinks accumulate within 100+ notes.

**What they skip:** embeddings and MOCs (Maps of Contents — topic-level indexes that cross folders) (the solo founder doesn't have enough notes for either).

**What they add:** a **lifecycle audit** (quarterly). The audit runs the 5-question schema audit + the wikilink audit + the tag audit + the backup audit. Output: a content-cleanup ticket.

**What they typically use the content-management discipline for:**
- "Is this note atomic?" — split when >2000 words or 5+ ideas
- "Is this tag still valid?" — quarterly tag audit
- "Are there dangling wikilinks?" — quarterly wikilink audit
- "When did I last back up?" — weekly cadence + quarterly recovery test

## 00c-decide-agent-vault-interaction

**Typical decision:** Read-only + Propose for the default profile; Edit added for implementer and verifier roles; Archive added for the orchestrator lane. Read-only on tier 4 (archive) for all agents. Default tool-call budgets (50 reads / 30 edits / 20 scaffolds / 20 archives per session). Read-then-write discipline. Optimistic locking for collaborative edits, last-write-wins for independent audit lines.

See `methodology/03a-decide-agent-vault-interaction.md` for the full design methodology. Use `templates/agent-vault-permissions.md.template` for the per-agent permission matrix.

**Why this:** the solo founder's agents have distinct roles (default, coder, verifier, planner). Each role needs different vault permissions. Default is read+propose; coder adds edit; verifier adds read+verify but rarely edits; planner adds scaffold for ticket decomposition.

**What they skip:** explicit locking (the solo founder's fleet is small; last-write-wins and optimistic locking are sufficient).

**What they add:** a **read-then-write audit line** that the agent writes per session. The audit line summarizes what the agent read and what it wrote. The operator reviews the audit line to verify the agent's behavior.

**What they typically use the interaction design for:**
- "Can the coder edit tier 4?" — per-tier permission matrix
- "Is the agent within budget?" — tool-call budget per session
- "Did the agent read before writing?" — read-then-write audit line
- "Did the agent archive by judgment?" — archive trigger audit

## 00d-decide-operator-agent-interaction

**Typical decision:** Launcher + Approver + Governor roles active for the operator (Curator is delegated to the operator but runs quarterly; Fallback is active for the operator but rarely used). Chat budget: ≤200 words/msg, ≤3000 tokens/session. Operator-action ticket cadence: ≤2 per day per agent. Approval format: `Approve / Reject / Modify / Defer`. Escalation SLA: priority 1 = 30 min, priority 2 = 2 hours, priority 3 = 1 day.

See `methodology/03b-decide-operator-agent-interaction.md` for the full design methodology. Use `templates/operator-interaction-patterns.md.template` for the role distribution.

**Why this:** the solo founder is the bottleneck. The operator-action gate is strict; the agent surfaces only decisions the operator must make. Routine decisions are agent-handled.

**What they skip:** the curator role (the solo founder uses ad-hoc skill creation; not a formalized cycle).

**What they add:** a **read-then-write audit line** that the agent emits at session-end. The audit line summarizes what the agent read and what it wrote; the operator scans it for sanity.

**What they typically use the operator-agent interaction design for:**
- "Is the agent flooding me?" — operator-action ticket cadence
- "Did the agent ask the right question?" — escalation format
- "Why did the agent decide X without asking?" — feedback format (You're doing X. The right behavior is Y. Reason: Z.)
- "Should I approve the agent's proposal?" — approver role

## 00e-decide-agent-communication-channels

**Typical decision:** 3 channels (DM, comment, kanban event) + orchestrator-isolation pattern. The orchestrator is the only agent with DM access to the operator; downstream agents (coder, verifier, planner, etc.) communicate via ticket comments only.

See `methodology/03c-decide-agent-communication-channels.md` for the full design methodology.

**Why this:** the solo founder's context window is bounded. If downstream agents DM the operator directly, the context fills; the operator can't tell operator-action items from downstream agent chatter. The orchestrator-isolation pattern keeps the operator's context clean and the downstream agent's context task-scoped.

**What they skip:** a separate "broadcast" channel for system-wide announcements. The solo founder's fleet is small; the orchestrator's DM is sufficient.

**What they add:** a **filter rule** for what the orchestrator surfaces to the operator. The orchestrator DM is filtered to: (a) operator-action asks, (b) high-priority alerts (cron failures, irreversible actions), (c) daily summary. Everything else stays in ticket comments.

**What they typically use the channel design for:**
- "Did the coder bypass the orchestrator?" — DM-isolation check
- "Why is the operator context full?" — comment-vs-DM audit
- "Is the orchestrator doing the coder's work?" — orchestrator-as-router check
- "Are downstream agents seeing operator context?" — context-leak audit

## 01-decide-vault-tier

**Typical decision:** 4-tier vault (0-inbox, 1-literature, 2-atomic, 3-indexes, 4-archive).

**Why 4 tiers:** the solo founder has enough context to benefit from 4 but not enough to need 5+. The 5th tier (scratch / ephemera) would be empty in their context.

**What they skip:** the "scratch" tier (0-scratch as a separate concept). Ephemera goes in the inbox tier and is routed out within 30 days.

**What they add:** an inbox-tier subfolder for items they triaged and decided to archive. This is a sub-tier within tier 0, used to keep "no" answers visible for a few weeks in case they re-evaluate.

## 01a-decide-work-graph

**Typical decision:** 4-state set (ready/running/done/archived) + 4-edge set (parent/child, blocks/blocked-by). See `methodology/04a-decide-work-graph.md` for the full design.

**Why 4 states:** the solo founder has enough workflow to need state tracking, but not enough to need sub-states. Adding `blocked` and `needs_input` is fine when the operator needs them.

**Why 4 edges:** the solo founder has 2-3 active projects with parent/child decompositions. The 4-edge set covers that. They typically don't use `relates_to` (too soft) or `duplicates` (rare).

**What they skip:** sub-states. The 4-state minimum is enough; sub-states come when the simple system can't answer a real question.

**What they add:** a **cascading-cron** that surfaces "parent done → children ready" patterns and a parent-archive policy (children stay active when parent archives; children are auto-cancelled when parent is cancelled).

**What they typically use the work graph for:**
- "What's blocking ticket X?" — reverse-traversal of `blocks`
- "What decomposes from epic Y?" — forward-traversal of `parent_id`
- "If I cancel epic Z, what else cancels?" — recursive sub-graph traversal
- Critical-path queries on multi-step projects (e.g., "what's the bottleneck?")

## 01b-decide-board-routing

**Typical decision:** 4-7 boards, plus a default. See `methodology/04b-decide-board-routing.md` for the full design methodology.

**Why the small number:** the solo founder has enough workflow to need domain-routing but not enough to need 10+ boards. The default board is the catch-all for one-offs.

**What they skip:** optional edges (`relates_to`, `duplicates`, `blocks`). The solo founder's work is mostly top-level projects with sub-tasks. They use parent/child for decomposition but rarely block between independent tickets.

**What they add:** a **cascading-cron** that surfaces "parent done → children ready" patterns and a parent-archive policy (children stay active when parent archives; children are auto-cancelled when parent is cancelled). See `methodology/04b-decide-board-routing.md` for the cross-board move pattern.

**What they typically use the board-routing system for:**
- "Where should I file this ticket?" — the keyword-routing table answers this
- "Why is this on the wrong board?" — the cross-board move procedure answers this
- "Should I add a new board?" — the 2-of-5 test (own profile, cron, operator gate, dashboard, >50 tickets)

## 02-decide-skills

**Typical decision:** 3-5 skills in the first 6 months. The 3 core skills:
1. **Vault routing** (skill for moving items between tiers)
2. **Cron-monitor** (skill for designing the detect → surface → act loop)
3. **Council-chair** (skill for running a multi-seat council)

**Why 3-5:** the solo founder has a small agent fleet. More than 5 skills means the agents spend too much context loading skills. Less than 3 means the agents don't have enough structure.

**What they skip:** the "agent-bootstrap" skill (they don't scaffold new agents often; when they do, it's a special project). The "inbox-summary" skill (they have a cron for that).

**What they add:** a **"triage"** skill that combines inbox-routing with a 5-question filter. The cron uses this skill to process daily inbox items.

## 03-decide-agents

**Typical decision:** 1 default profile + 2-3 named profiles.

**The 3 named profiles for a solo founder:**
- **Coder** — implements specs, builds skills, builds crons
- **Verifier** — verifies outputs, runs RED→GREEN tests, audits cron runs
- **Planner** — plans multi-step work, decomposes tasks, manages parent-child ticket relationships

**Why 3:** the solo founder has 3 distinct roles that recur 5+ times/week. Coder is the most-active; verifier is weekly; planner is per-project.

**What they skip:** the "reviewer" or "approver" agent. The solo founder IS the reviewer and approver; the operator lane handles that.

**What they add:** a **"scout"** profile if they're running an income-replacement target. The scout generates new ideas; the operator reviews the ideas; the council makes the final verdict.

## 04-decide-crons

**Typical decision:** 5-10 crons, mostly `*/5` heartbeats + daily triage + weekly scout.

**The 5-7 standard crons:**
1. **Dispatcher heartbeat** — every 1 min, silent on no-action
2. **Rescue watcher** — every 5 min, surfaces stuck items
3. **Operator-action-dm** — every 30 min, surfaces real-ask items
4. **Vault-triage** — daily 04:00, archives old items
5. **Project-state-snapshot** — daily 06:00, updates project manifests
6. **Skill-curator** — weekly Sunday 04:00, audits skills
7. **MSaaS-scout** — weekly Monday 09:00, generates new ideas (if income-replacement target exists)

**Why 5-10:** the solo founder has enough workflow to need 5+ crons, but not enough to need 15+. More than 10 crons means over-croning.

**What they skip:** real-time crons (every second, every minute). Real-time is hook territory, not cron territory.

**What they add:** a **"x-ingestion"** cron if they're ingesting X posts systematically. Daily or weekly cadence.

## 05-op-guards

**Typical decision:** 5-7 operational guards, mostly operator-specific.

**The 5-7 standard rules:**
1. **No-narrated-action** — agent does the action, doesn't narrate it
2. **No-shadow-close** — don't close tickets to reduce WIP
3. **No-direct-mutation** — file kanban changes, don't mutate system state in chat
4. **No-close-on-age** — close only on evidence, not age
5. **No-auto-handoff** — irreversible actions need operator review
6. **Verify-operator-action** — strict gate on what surfaces to operator
7. **Must-call-terminal** — workers must call kanban_complete or kanban_block before exit

**Why 5-7:** the solo founder has 5-7 distinct failure patterns that have occurred 3+ times. More than 7 means over-documenting; less than 5 means under-defending.

**What they skip:** rules that are about a specific tool's quirks (those belong in the tool's docs, not in the operator's rules).

**What they add:** operator-specific rules about their context (e.g., "no public-facing communication without review"). These are archived after 6 months if the pattern doesn't recur.

## 06-iteration-loop

**Typical decision:** 2-3 monitor loops, 1-2 auto-recover loops, 1-2 trigger loops.

**The 5-7 standard loops:**
- 2-3 heartbeats (dispatcher, rescue-watcher, dispatcher-supervisor)
- 1-2 monitors (operator-action-dm, claim-queue-health)
- 1-2 auto-recoverers (orphan-rescue, profile-bootstrap)
- 1-2 triggers (vault-triage, project-state-snapshot)

**Why 5-7:** the solo founder has enough workflow to need 5+ loops, but not enough to need 15+.

**What they skip:** chat-lane impersonation loops. A loop that pretends to be a chat agent is over-engineering for a solo operator.

**What they add:** a **"decision-helper"** loop that watches for `kind=needs_input` items and surfaces the right context for the operator's decision.

## 07-council-methodology

**Typical decision:** 4 seats (Strategist, Engineer, Operator, Skeptic). Run councils for:
- Skill-brief validation
- Major scope changes (new focus vertical, new operational guard)
- Failure pattern reviews (3+ incidents of the same shape)

**Why 4 seats:** the minimum viable. 5-6 is fine if the operator has a specific need; 7+ is over-deliberation.

**What they skip:** per-decision councils. Most decisions are not council-worthy.

**What they add:** a **"verifier-only" mini-council** for skill-briefs that don't need the full 4 seats. Just the Verifier perspective, 1-2 paragraphs.

## 08-inbox-route

**Typical decision:** route 1-10 inbox items per week. 5-question filter applied to each.

**What they typically route:**
- X posts (most common source)
- Articles (RSS / blog)
- Screenshots (visual patterns)
- Conversations (chat / voice memos)

**What they skip:** books (read at leisure, route after, not as inbox items).

**What they add:** a **"voice memo → transcript → route"** pipeline. The voice memo is captured, transcribed by an LLM, then routed as a text item.

## 09-inbox-from-external-sources

**Typical decision:** 4-stage pipeline (capture → filter → summarize → route) for X posts. Daily capture + filter; weekly summarize + route.

**What they typically capture:** 1-5 X posts per day.

**What they typically filter:** most X posts fail the filter (low signal-to-noise). The filter is tuned to the operator's context.

**What they typically route:** most filtered posts are archived. The few that survive are routed to skill-brief, op-guard, atomic, or operator-action.

---

## What the solo founder archetype optimizes for

- **Throughput per operator-hour.** The solo founder is the bottleneck. Every workflow should reduce operator-time, not increase it.
- **Reversibility.** Most actions should be reversible. The irreversible ones get operator review.
- **Cost discipline.** Most crons should be free or near-free. LLM calls should be batched and rare.
- **Audit trail.** Every action should leave a trace. The operator should be able to ask "what did the system do?" and get an answer.

## What the solo founder archetype does NOT optimize for

- **Real-time.** No real-time workflows. Hooks, not crons, for real-time.
- **Multi-operator collaboration.** The solo founder is the only operator.
- **High-throughput pipelines.** Most workflows are 1-10 items per day, not 100+.
- **Public-facing automation.** The solo founder is the gate for anything public-facing.
