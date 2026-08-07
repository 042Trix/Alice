---
id: alice-example-research-analyst-methodology
created: 2026-08-04T12:30:00Z
title: "Methodology notes — research analyst archetype (anonymized)"
type: worked-example
status: draft
source: alice-framework
tags: [kind:worked-example, kind:methodology-notes, project:alice, archetype:research-analyst]
confidence: 0.0
links: []
---

# Methodology notes — research analyst archetype

> **Use this example to see how a research analyst can apply Alice's methodology** when producing research, content, and 1-2 income-replacement workstreams. It shows the resulting decision pattern, not a snapshot of a specific operator's setup.

For each methodology decision covered by this example, you will see:
- **What the research analyst typically decides** (the typical answer)
- **What they typically skip** (the optional parts)
- **What they typically add** (the operator-specific extensions)

Treat these notes as **a starting structure, not a prescribed destination.** Apply the methodology to your own context; the example shows one decision pattern, while the implementation choices remain operator-specific.

---

## 00-decide-memory

**Typical decision:** 7-tier memory system (cold-start, procedural, identity, episodic, long-form, operator preferences, system) + embeddings tier (Tier 5b for the research analyst). See `methodology/01a-decide-memory.md` for the full design methodology.

**Why 7+ tiers:** the research analyst has enough literature to benefit from a Tier 5b (embeddings) for semantic search. The 7 base tiers + 1 extension tier.

**What they skip:** cascading-crons. The research analyst has too many cascading effects to automate; the operator reviews each cascade.

**What they add:** a **memory-budget template** per agent. The template defines per-tier size/count constraints, ownership, recall triggers, and lifecycle. Use `templates/memory-budget.md.template`. The research analyst's Tier 5b (embeddings) has its own budget (≤50K vectors per literature note).

**What they typically use the memory tier system for:**
- "Is my literature tier bloated?" — quarterly audit on Tier 5 (unlimited in storage, but indexed for fast query)
- "Is the citation graph healthy?" — episodic memory (Tier 4) audit on citation events
- "Did the agent remember to cite this source?" — Tier 5 (literature) vs. Tier 5b (embeddings) consistency check

## 00b-decide-vault-content

**Typical decision:** frontmatter schema (10-12 required/optional fields, including `citation:` tag for literature tracking) + 5-6 tag namespaces (domain, project, kind, source, citation, private) + 7-char note IDs + ID-based wikilinks + capture-triage-process-mature-stale-archive lifecycle + 7-day backups + monthly embedding regeneration.

See `methodology/01b-decide-vault-content.md` for the full design methodology.

**Why this:** the research analyst has more notes than the solo founder (50:1 literature:atomic ratio). Without a unified content-management discipline, the literature tier becomes a junk drawer.

**What they skip:** a single MOC (Map of Contents — a topic-level index that crosses folders) for the whole vault (the research analyst uses topic-specific MOCs instead).

**What they add:** a **citation-graph audit** (monthly). The audit checks that every atomic note citing a source has a valid `citation:` tag, and every literature-tier note has at least one atomic note citing it. Output: a citation-cleanup ticket.

**What they typically use the content-management discipline for:**
- "Is this literature note cited by any atomic note?" — citation-graph audit
- "Are these two literature notes duplicates?" — N:M wikilink query + duplicate detection
- "Is this MOC still useful?" — quarterly MOC audit
- "Are the embeddings stale?" — quarterly embedding audit (regen if note `updated` > embedding timestamp)

## 00c-decide-agent-vault-interaction

**Typical decision:** Read-only + Propose for the default and scout profiles; Edit added for researcher, writer, and verifier roles; Scaffold added for the researcher (for new citations and maps of content); Archive added for the orchestrator lane. Read-only on tier 4 (archive) for all agents. Higher read budgets (80 per session) for researcher and writer roles because their work is literature-heavy.

See `methodology/03a-decide-agent-vault-interaction.md` for the full design methodology. Use `templates/agent-vault-permissions.md.template` for the per-agent permission matrix.

**Why this:** the research analyst's agents handle large literature (50:1 literature:atomic ratio). The `researcher` agent loads citations; the `writer` agent loads drafts; the `verifier` agent validates citations. Each needs different read/edit ratios.

**What they skip:** the scaffolder pattern (the research analyst doesn't create new sub-vaults often; scaffolds are mostly MOCs and citation indexes).

**What they add:** a **citation-update pattern** — when a literature note is updated (e.g., a new version of the cited paper), the `verifier` agent re-runs the citation audit for all atomic notes that cite it. This is a 3-step interaction pattern: read citing notes → verify citations → emit verification audit line.

**What they typically use the interaction design for:**
- "Did the writer cite the source correctly?" — citation-update pattern
- "Is the researcher within budget?" — tool-call budget per session (80 reads for heavy work)
- "Did the writer create a duplicate literature note?" — wikilink audit
- "Should the verifier archive a stale literature note?" — archive triggers

## 00d-decide-operator-agent-interaction

**Typical decision:** Launcher + Approver + Curator roles active for the operator (Governor is delegated; Fallback is active for editorial judgment). Higher chat budget (≤500 words/msg) for editorial explanations. Operator-action ticket cadence: ≤5 per day per agent (literature-heavy work has more asks). Approval format: editorial `Accept / Revise / Reject` (not just `Approve / Reject`). Escalation SLA: priority 1 (irreversible / public / citation integrity) = 1 hour, priority 2 (editorial judgment) = 4 hours, priority 3 (routine) = 2 days.

See `methodology/03b-decide-operator-agent-interaction.md` for the full design methodology. Use `templates/operator-interaction-patterns.md.template` for the role distribution.

**Why this:** the research analyst has editorial judgment at the center. The operator reviews drafts (the writer proposes, the operator accepts/revises); the operator reviews citations (the verifier proposes, the operator accepts/revises). The approver role is editorial, not just procedural.

**What they skip:** the governor role for op-guard drafting (the research analyst uses council verdicts, not operational guards).

**What they add:** a **citation-update protocol** — when a literature note is updated (new version of a paper), the operator gets an operator-action ticket asking whether to update the citing notes. The protocol is: read citing notes → ask operator → if approve, agent updates citations.

**What they typically use the operator-agent interaction design for:**
- "Should I accept this draft?" — editorial approval (the operator is the editor)
- "Should the verifier update citations?" — citation-update protocol
- "Did the writer cite the source correctly?" — citation-feedback format
- "When should I run an editorial review?" — curator role (weekly)

## 00e-decide-agent-communication-channels

**Typical decision:** 3 channels (DM, comment, kanban event) + orchestrator-isolation pattern. The orchestrator (the editorial assistant) is the only agent with DM access to the operator; downstream agents (researcher, writer, verifier) communicate via ticket comments only.

See `methodology/03c-decide-agent-communication-channels.md` for the full design methodology.

**Why this:** the research analyst has higher context-budget pressure (literature-heavy work). The orchestrator-isolation pattern keeps the operator's editorial-judgment context clean; downstream agents focus on their tasks.

**What they skip:** the comment-thread-as-shared-memory pattern for cross-task references. The research analyst uses MOCs (3-INDEXES) instead; ticket comments are task-scoped only.

**What they add:** a **citation-update protocol** — when a literature note is updated, the verifier (downstream agent) writes a comment on the ticket explaining the impact on citing notes. The orchestrator decides whether to surface this to the operator as an operator-action ask.

**What they typically use the channel design for:**
- "Did the writer bypass the orchestrator?" — DM-isolation check
- "Is the operator seeing only editorial decisions?" — orchestrator filter check
- "Did the verifier update citations correctly?" — comment-thread review
- "Is the operator's editorial context clean?" — context-isolation audit

## 01-decide-vault-tier

**Typical decision:** 5-tier vault (0-inbox, 0-capture, 1-literature, 2-atomic, 3-indexes, 3-citations, 3-embeddings, 4-archive).

**Why 5+ tiers:** the research analyst has 5-15 daily captures. A separate `0-CAPTURE/` tier for raw captures (before triage) is useful because triage is a separate workflow from capture. The 3-INDEXES tier has sub-folders for MOCs, citations, and embeddings.

**What they skip:** the "ephemera" tier. Ephemera is captured into `0-CAPTURE/` and routed out within 7 days, not 30.

**What they add:** the **3-CITATIONS/** index. The research analyst's citation graph is large enough (50+ external sources) that an index is essential. The citation index is regenerated when the citation graph changes.

**What they add:** the **3-EMBEDDINGS/** index. For semantic search across literature, embeddings are useful. The research analyst regenerates embeddings when literature-tier notes change.

## 01a-decide-work-graph

**Typical decision:** 4-state set (ready/running/done/archived) + 4-edge set (parent/child, blocks/blocked-by) + 2 optional edges (`relates_to` and `duplicates`).

**Why the optional edges:** the research analyst has more cross-references than the solo founder. `relates_to` is used for "this draft is in the same area as that draft." `duplicates` is used for de-duping literature notes.

**What they skip:** cascading-crons. The research analyst has too many cascading effects to automate; the operator reviews each cascade.

**What they add:** a **citation-blocker** relationship. Literature notes can block atomic notes (you can't write about X until you've read Y). This is a soft block (not a hard `blocks`), but it's a real relationship that the research analyst tracks.

**What they typically use the work graph for:**
- "What literature do I need to read before writing this draft?" — reverse-traversal of citation-blocker
- "What drafts cite this literature note?" — forward-traversal of citation-blocker
- "What is the citation graph for paper X?" — sub-graph traversal
- "Are there duplicate literature notes?" — N:M traversal of `duplicates`

## 01b-decide-board-routing

**Typical decision:** 4-6 boards, plus a default. See `methodology/04b-decide-board-routing.md` for the full design methodology.

**Why the small number:** the research analyst has enough cross-cutting work to need domain-routing but not enough to need 10+ boards. The 3-INDEXES tier has sub-folders (MOCs, citations, embeddings) instead of separate boards for each.

**What they skip:** cascading-crons. The research analyst has too many cascading effects to automate; the operator reviews each cascade.

**What they add:** a **citation-blocker** relationship (the `blocks` edge) — literature notes can block atomic notes (you can't write about X until you've read Y). This is a soft block. See `methodology/04b-decide-board-routing.md` for the routing pattern.

**What they typically use the board-routing system for:**
- "Where should I file this ticket?" — the keyword-routing table answers this
- "Why is this on the wrong board?" — the cross-board move procedure answers this
- "Should I add a new board?" — the 2-of-5 test (own profile, cron, operator gate, dashboard, >50 tickets)

## 02-decide-skills

**Typical decision:** 5-8 skills in the first 6 months. The 4 core skills:
1. **Vault routing** (skill for moving items between tiers)
2. **Citation lookup** (skill for finding a citation by source, year, or topic)
3. **Council-chair** (skill for running a multi-seat council)
4. **Draft-generation** (skill for generating prose drafts from atomic notes)
5. **X-posts ingestion** (skill for filtering X posts by relevance)

**Why 5-8:** the research analyst has more skill needs than the solo founder. Content generation, citation lookup, and prose drafts are research-analyst-specific.

**What they skip:** the "agent-bootstrap" skill (the research analyst doesn't scaffold new agents often).

**What they add:** the **"draft-generation"** skill. This skill takes atomic notes (a thesis, supporting evidence, counter-evidence) and generates a prose draft. The operator reviews the draft; the agent revises based on feedback.

## 03-decide-agents

**Typical decision:** 1 default profile + 3-4 named profiles.

**The 4 named profiles for a research analyst:**
- **Researcher** — investigates a topic, drafts atomic notes, summarizes literature
- **Writer** — generates prose drafts from atomic notes, applies style guide
- **Verifier** — verifies citations, runs RED→GREEN checks, audits drafts
- **Scout** — generates new research leads, surfaces new sources

**Why 4:** the research analyst has 4 distinct roles that recur 5+ times/week. Researcher is the most-active; writer is per-project; verifier is per-draft; scout is weekly.

**What they skip:** the "coder" agent. The research analyst doesn't write code (or writes very little). When code is needed, dispatch to `default`.

**What they add:** the **"writer"** agent with a style guide. The writer's soul includes the operator's style preferences (e.g., "academic prose," "active voice," "no jargon").

## 04-decide-crons

**Typical decision:** 7-12 crons, mostly `*/5` heartbeats + daily triage + weekly content production.

**The 7-9 standard crons:**
1. **Dispatcher heartbeat** — every 1 min, silent on no-action
2. **Rescue watcher** — every 5 min, surfaces stuck items
3. **Operator-action-dm** — every 30 min, surfaces real-ask items
4. **X-ingestion** — daily 06:00, captures and filters X posts
5. **Citation-graph regen** — daily 04:00, rebuilds 3-CITATIONS/
6. **Embedding regen** — daily 04:00, rebuilds 3-EMBEDDINGS/
7. **Vault-triage** — weekly Sunday 04:00, archives old items
8. **Skill-curator** — weekly Saturday 04:00, audits skills
9. **Content scout** — weekly Monday 09:00, generates new research leads

**Why 7-12:** the research analyst has more workflow than the solo founder. Citation regen + embedding regen are research-analyst-specific.

**What they skip:** real-time crons. Research is async, not real-time.

**What they add:** the **"embedding regen"** cron. Embeddings need periodic regeneration as literature-tier content changes.

## 05-op-guards

**Typical decision:** 6-9 operational guards, mostly operator-specific.

**The 6-9 standard rules:**
1. **No-narrated-action** — agent does the action, doesn't narrate it
2. **No-shadow-close** — don't close tickets to reduce WIP
3. **No-direct-mutation** — file kanban changes, don't mutate system state in chat
4. **No-close-on-age** — close only on evidence, not age
5. **No-auto-handoff** — irreversible actions need operator review
6. **Verify-operator-action** — strict gate on what surfaces to operator
7. **Must-call-terminal** — workers must call kanban_complete or kanban_block before exit
8. **No-citation-without-source** — every atomic note citing an external source must have a `citation:` tag
9. **No-draft-without-citation** — every prose draft citing a claim must cite the source

**Why 6-9:** the research analyst has the same 5-7 generic rules plus 1-2 research-specific rules (citation discipline).

**What they skip:** rules about code review (the research analyst doesn't review code).

**What they add:** the **"no-citation-without-source"** and **"no-draft-without-citation"** rules. These are research-analyst-specific constraints that prevent citation drift.

## 06-iteration-loop

**Typical decision:** 3-4 monitor loops, 2-3 auto-recover loops, 2-3 trigger loops.

**The 7-10 standard loops:**
- 2-3 heartbeats (dispatcher, rescue-watcher, dispatcher-supervisor)
- 2-3 monitors (operator-action-dm, claim-queue-health, citation-graph-health)
- 1-2 auto-recoverers (orphan-rescue, profile-bootstrap)
- 2-3 triggers (vault-triage, citation-graph-regen, embedding-regen, content-scout)

**Why 7-10:** the research analyst has more loops than the solo founder. Citation-graph-health + content-scout are research-analyst-specific.

**What they skip:** chat-lane impersonation loops. Research is async; chat is for decisions, not for loop impersonation.

**What they add:** the **"citation-graph-health"** loop. This loop checks that every atomic note citing a source has a valid citation tag, and every literature-tier note has at least one atomic note citing it. Catches citation drift.

## 07-council-methodology

**Typical decision:** 4 seats (Strategist, Engineer, Operator, Skeptic). Run councils for:
- Skill-brief validation
- Major scope changes (new research vertical, new operational guard)
- Failure pattern reviews (3+ incidents of the same shape)
- **Editorial decisions** (is this draft ready to ship?)

**Why 4 seats:** the minimum viable. 5-6 is fine if the operator has a specific need; 7+ is over-deliberation.

**What they skip:** per-decision councils. Most decisions are not council-worthy.

**What they add:** a **"verifier + operator" mini-council** for editorial decisions. Two seats only (no Strategist, no Engineer — editorial decisions don't need them). 1-2 paragraphs each.

## 08-inbox-route

**Typical decision:** route 5-15 inbox items per week. 5-question filter applied to each.

**What they typically route:**
- Articles (RSS / blog) — most common source
- X posts — second most common
- Books (read at leisure, route after)
- Voice memos (transcribed first, then routed as text)
- Screenshots (visual patterns)

**What they skip:** routine emails (most are operator-actionable, no routing needed).

**What they add:** a **"book → 5-page summary → route"** pipeline. Books are processed after reading, not as inbox items. The summary is 2-3 pages; the routing is the 5-question filter.

## 09-inbox-from-external-sources

**Typical decision:** 4-stage pipeline (capture → filter → summarize → route) for X posts and articles. Daily capture + filter; weekly summarize + route.

**What they typically capture:** 5-15 X posts per day, 3-5 articles per day.

**What they typically filter:** most fail the filter. The filter is tuned to the operator's research interests.

**What they typically route:** most filtered items are archived. The few that survive are routed to citation (literature), atomic (idea), skill-brief, op-guard, or operator-action.

**What they add:** a **"RSS-into-vault"** cron that captures articles from the operator's RSS subscriptions. Daily cadence, automatic capture; manual summarize + route.

---

## What the research analyst archetype optimizes for

- **Source quality over throughput.** A research analyst cares about citing the right source, not about citing many sources.
- **Citation discipline.** Every claim has a source. Every source is traceable.
- **Editorial quality.** Drafts are reviewed by the operator before publication.
- **Long-term memory.** The research analyst's vault is a research database, not a workflow tool.

## What the research analyst archetype does NOT optimize for

- **Real-time.** Research is async, not real-time.
- **Code-heavy workflows.** The research analyst writes prose, not code.
- **Multi-operator collaboration.** The research analyst is the only operator.
- **Public-facing automation.** Editorial judgment is operator's, not agent's.
