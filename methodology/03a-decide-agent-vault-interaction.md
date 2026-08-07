---
id: alice-methodology-03a-decide-agent-vault-interaction
created: 2026-08-04T12:30:00Z
updated: 2026-08-05T18:55:00Z
title: "Methodology 03a — Decide how agents interact with the vault (read/propose/edit/scaffold/archive)"
type: methodology
status: draft
source: alice-framework
tags: [kind:methodology, kind:agent-vault, kind:scoped-import, project:alice]
confidence: 0.0
links: ["[[methodology/01-decide-vault-tier.md]]", "[[methodology/01a-decide-memory.md]]", "[[methodology/01b-decide-vault-content.md]]", "[[methodology/02-decide-skills.md]]", "[[methodology/03-decide-agents.md]]", "[[methodology/05-op-guards.md]]", "[[templates/agent-vault-permissions.md.template]]", "[[templates/agent-soul.md.template]]"]
---

# Methodology 03a — Decide how agents interact with the vault

> **The agent design doc tells you WHO the agent is. The vault content doc tells you HOW to write notes. This doc tells you HOW THE AGENT acts on the vault.** Every agent session involves vault interactions — reads, writes, edits, scaffolds, archives. Without a unified methodology, the agent's vault behavior is ad-hoc and the vault degrades.

This methodology covers **the design of agent-vault interaction** — the 5 interaction patterns, the read-then-write discipline, the tool-call budget, concurrent-write protection, the agent context budget, scaffolder/archivist guardrails, the reader pattern, the audit-line discipline, and the interaction anti-patterns. The methodology is tool-agnostic. The canonical implementation in the worked examples uses the kanban + vault + AGENTS.md pattern. The tool-mapping guide (`references/tool-mapping-guide.md`) says how to implement this in your specific tool.

**Documentation convention:** Alice documents the **method**, not specific instances. This doc does not list any specific agent or vault instance. The friend applies the method to their own context.

---

## Part 1: Why interaction design matters

### The decision

Every agent session has a **vault interaction footprint**. The operator (or the agent) decides:
- **What can the agent read?** (which tiers, which notes, which tools)
- **What can the agent write?** (new notes, edits, scaffolds)
- **What can the agent propose?** (draft for operator review)
- **What can the agent archive?** (which notes, which lifecycle stage)
- **How much vault can the agent load?** (cold-start budget, on-demand limits)
- **How often can the agent interact?** (tool-call budget per session)
- **What happens on concurrent writes?** (lock, conflict, last-write-wins)

Without an interaction design, the answers are implicit. The agent reads / writes / edits at will, the operator doesn't know what changed, and the vault degrades silently.

### The 5 failure modes

An agent-vault interaction system fails in 5 predictable ways:

1. **Silent reads.** The agent reads 100 notes without writing audit lines. The operator can't tell what the agent knows.
2. **Orphan writes.** The agent writes new notes without frontmatter, tags, or wikilinks. The notes are unqueryable.
3. **Concurrent corruption.** Two agents write the same note at the same time. The second write overwrites the first.
4. **Scaffolder sprawl.** The agent creates files / directories / indexes without operator review. The vault grows unbounded.
5. **Premature archive.** The agent archives a note because it "looks stale" without operator review. Useful content is lost.

The interaction design is the design that prevents all 5.

---

## Part 2: The 5 interaction patterns

Alice recommends a 5-pattern interaction model. Each pattern is a **distinct permission level**:

### Pattern 1: Read-only

**What:** the agent reads the vault but cannot modify it.

**Tools:** file reader, content search, index query, manifest lookup, wikilink resolver, semantic search.

**When to use:**
- The agent is loading context (e.g., "what does my AGENTS.md say about cron cadence?")
- The agent is researching (e.g., "what notes exist about X topic?")
- The agent is verifying (e.g., "is this note archived?")

**Permission:**
- All tiers: read
- All notes: read
- No writes, no edits, no scaffolds, no archives

**Audit line:** per major read (e.g., "loaded 12 notes about X"), not per file access. The audit line is brief.

### Pattern 2: Propose

**What:** the agent drafts a new note or an edit, and the operator reviews before committing.

**Tools:** read-only tools + `propose_note`, `propose_edit` (writes to a draft queue, not the vault).

**When to use:**
- The agent has research that should become a note
- The agent has identified an edit that should be made
- The agent is uncertain about the right tier or tags

**Permission:**
- All tiers: read
- Draft queue: write (the proposal goes here, not to the vault)
- Vault: read only

**Audit line:** per proposal (e.g., "proposed note-id: <title>"). The operator reviews and approves.

### Pattern 3: Edit

**What:** the agent writes to the vault without operator review, subject to the read-then-write discipline and the audit-line rule.

**Tools:** read-only tools + `write_note`, `edit_note`, `wikilink_create`, `tag_apply`.

**When to use:**
- The agent is implementing a clear instruction (e.g., "create a note about X")
- The agent has operator's prior approval for this kind of edit (e.g., "auto-tag every new note with kind:spec")
- The agent is filling in a template the operator specified

**Permission:**
- All tiers: read
- Atomic, inbox, indexes: write (per the tier policy)
- Literature, archive: read only (these are append-only / move-only)

**Audit line:** per edit (e.g., "edited note-id: <what changed>"). The audit line is specific.

### Pattern 4: Scaffold

**What:** the agent creates new files, directories, or indexes in the vault.

**Tools:** edit tools + `create_directory`, `create_index`, `create_template`, `init_subvault`.

**When to use:**
- The agent is bootstrapping a new tier or sub-vault
- The agent is creating an index file (MOC, FTS5 manifest)
- The agent is creating a template

**Permission:**
- All tiers: read
- Vault structure: write (new files / dirs / indexes)
- Existing notes: edit per the edit pattern

**Audit line:** per scaffold (e.g., "scaffolded <path>: <purpose>"). The audit line is specific.

**The scaffolder guardrail:** scaffolds require operator review unless the scaffold is part of an existing scaffold pattern. A new scaffold pattern (new directory, new template) is operator-actionable.

### Pattern 5: Archive

**What:** the agent moves a note from its current tier to the **archive tier** (e.g., `4-archive/` per the naming chosen in `methodology/01-decide-vault-tier.md`).

**Tools:** edit tools + `archive_note`, `unarchive_note`.

**When to use:**
- The agent is implementing an explicit operator instruction (e.g., "archive all notes older than 1 year with status=stale")
- The agent has run the lifecycle audit and identified stale notes
- The agent is freeing up the operator's inbox by archiving old items

**Permission:**
- All tiers: read
- Atomic, literature: read + archive-move
- Archive tier: write (the move lands here)

**Audit line:** per archive (e.g., "archived note-id: <reason>"). The audit line is specific.

**The archivist guardrail:** archives require either (a) operator instruction, (b) lifecycle audit output, or (c) explicit status=stale. The agent cannot archive a note because it "looks stale" — that's a judgment call, not an audit output.

### The 5-pattern matrix

| Pattern | Read | Propose | Edit | Scaffold | Archive |
|---|---|---|---|---|---|
| Read-only | ✅ | ❌ | ❌ | ❌ | ❌ |
| Propose | ✅ | ✅ (draft queue) | ❌ | ❌ | ❌ |
| Edit | ✅ | ✅ | ✅ (atomic/inbox) | ❌ | ❌ |
| Scaffold | ✅ | ✅ | ✅ | ✅ (vault structure) | ❌ |
| Archive | ✅ | ✅ | ✅ | ✅ | ✅ (atomic/literature → archive) |

The agent's permission level is determined by which patterns are active for the current session.

---

## Part 3: The read-then-write discipline

### The rule

**Always read before write.** The agent reads the existing note (or the tier) before writing a new note or editing an existing one.

**Why:** writing without reading produces:
- Duplicates (the agent writes a note that already exists)
- Conflicts (the agent writes something that contradicts an existing note)
- Lost context (the agent misses related notes that should be wikilinked)

### The 5-step read-then-write

1. **Read the tier.** The agent lists the existing notes in the tier (or in the relevant topic area).
2. **Read the related notes.** The agent reads the notes that the new note will relate to.
3. **Read the templates.** The agent reads the templates for the destination tier.
4. **Write the new note / edit.** The agent writes the note, applying the templates and wikilinking the related notes.
5. **Audit.** The agent writes an audit line for the read (step 1-2) and the write (step 4).

### When the read-then-write is skipped

The read-then-write can be skipped when:
- The operator gives an explicit "don't read, just write" instruction
- The agent is filling in a fixed template (no semantic decisions)
- The write is a mechanical update (e.g., timestamp refresh)

The skip is documented in the audit line.

---

## Part 4: The tool-call budget per session

### The rule

Every agent session has a **tool-call budget**. The budget is the maximum number of tool calls per session.

**Default budget:**
- Read-only pattern: ≤50 tool calls
- Propose pattern: ≤30 tool calls
- Edit pattern: ≤30 tool calls
- Scaffold pattern: ≤20 tool calls
- Archive pattern: ≤20 tool calls

### Why a budget

Without a budget, the agent:
- Reads forever (filling context window)
- Writes everywhere (creating duplicates)
- Sprawls scaffolds (over-engineering)

The budget forces the agent to be **discriminating** about what it touches.

### When to revise the budget

Revise the budget when:
1. **The agent consistently hits the limit.** (Budget is too small for the task.)
2. **The agent consistently uses <50% of the budget.** (Budget is too large; tighten.)
3. **A new pattern is added.** (Default budget for the new pattern.)

### The budget is per-session, not per-task

The budget resets every session. A long-running task (multi-session) gets a fresh budget per session.

---

## Part 5: Concurrent-write protection

### The rule

When two agents (or two sessions) write to the same note at the same time, the system must detect the conflict and resolve it.

### The 3 conflict-resolution strategies

**1. Last-write-wins (default):**
- The second write overwrites the first
- Simple but loses the first write's changes
- Use when the writes are independent (e.g., both append audit lines)

**2. Optimistic locking:**
- Each write reads the note's `updated` timestamp first
- The write includes the `updated` timestamp in the write request
- The system rejects the write if the note's `updated` has changed since
- The agent retries with the new state

**3. Explicit locking:**
- The agent acquires a lock on the note before writing
- The lock is released when the write completes
- Other agents wait for the lock or fail

### When to use which strategy

- **Last-write-wins:** for independent writes (audit lines, metadata).
- **Optimistic locking:** for collaborative writes (two agents editing different sections).
- **Explicit locking:** for atomic edits (one agent doing a multi-step edit that requires no interruptions).

### The conflict-resolution audit line

Every conflict-resolution event produces an audit line:

```
## [ISO8601-UTC] concurrent-write-conflict — <note-id>: <strategy> <resolution>
```

The operator can review the audit line to see if conflicts are common.

---

## Part 6: The agent context budget

### The rule

When loading the vault into the agent's context, the agent must respect the context budget. The default budget is **≤20K characters per load**, ≤5 distinct loads per session.

### Why a context budget

The vault is large (potentially thousands of notes). Loading the whole vault blows the context window. Loading too much per request wastes tokens.

### The 4 load strategies

**1. Cold-start load:** the AGENTS.md, ≤20K chars. Read on every launch.

**2. Topic load:** a MOC + the linked notes, ≤50K chars. Read on-demand for a topic.

**3. Note load:** a single note + its wikilinks (1 hop), ≤30K chars. Read on-demand for a specific note.

**4. Query load:** an FTS5 query result, ≤10K chars. Read on-demand for a search.

### When to use which strategy

- **Cold-start:** every launch
- **Topic:** when the agent is working on a specific topic and needs all related notes
- **Note:** when the agent is editing a specific note and needs its context
- **Query:** when the agent is researching and needs to find notes

### The on-demand limit

The agent can do ≤5 topic loads per session, ≤10 note loads, ≤20 query loads. Cumulative budget: ≤100K characters of on-demand loads.

---

## Part 7: Agent-as-scaffolder guardrails

### The rule

The agent can scaffold new files / directories / indexes, but only within an **approved scaffold pattern**.

### What's an approved scaffold pattern

A scaffold pattern is approved when:
1. **The operator has explicitly approved it** (e.g., "create a MOC per topic in `3-indexes/`" per the naming chosen in `methodology/01-decide-vault-tier.md`)
2. **The pattern is in the AGENTS.md scaffold section** (e.g., "all new topics get a MOC automatically")
3. **The scaffold matches an existing template** (e.g., the agent creates a new note from the AGENTS.md template)

### What's NOT an approved scaffold pattern

A new scaffold pattern (one the operator hasn't approved) is **operator-actionable**. The agent must:
1. Propose the scaffold (Pattern 2: Propose)
2. Wait for operator review
3. Apply the scaffold only after approval

### The scaffolder anti-patterns

- **Scaffolding without a template.** A new file/directory with no template is operator-actionable.
- **Scaffolding to an unusual location.** A file/directory outside the canonical structure (e.g., `_scratch/`) is operator-actionable.
- **Scaffolding that creates a parallel structure.** A new tier or sub-vault that duplicates an existing one is operator-actionable.

---

## Part 8: Agent-as-archivist guardrails

### The rule

The agent can archive a note only when the archive is **evidence-based**, not judgment-based.

### What's evidence-based

An archive is evidence-based when:
1. **The note's status is `stale`** (operator or agent marked it stale with a reason)
2. **The note is older than 30 days** AND **has no wikilinks from active notes**
3. **The note is a duplicate** (confirmed by a wikilink audit)
4. **The operator has explicit approved the archive** ("archive all notes about X")

### What's NOT evidence-based

A judgment-based archive is **NOT allowed**. Examples:
- "This note looks stale" — judgment
- "This note is similar to that note, so probably a duplicate" — judgment without audit
- "This note hasn't been edited in 6 months, so archive" — age-based, not evidence-based

### The archive audit line

Every archive produces an audit line:

```
## [ISO8601-UTC] archive — <note-id>: <reason> (<strategy>)
```

The reason must be one of: `status=stale`, `age>30d-no-active-links`, `duplicate-confirmed`, `operator-instruction`.

---

## Part 9: The agent-as-reader pattern

### The rule

The agent reads the vault for **context-loading**, not for editing. Reads do not produce vault state changes.

### The reader discipline

- **Reads do not produce audit lines per file access.**
- **Reads produce audit lines per major query.** (E.g., "loaded 12 notes about X.")
- **Reads do not trigger writes.** (The agent reads; the operator or propose pattern decides what to do.)

### The reader pattern use cases

- **Cold-start:** the AGENTS.md is the reader pattern at launch
- **Topic research:** the agent reads 5-10 notes to understand a topic
- **Note editing context:** the agent reads the note + its wikilinks before editing
- **Verification:** the agent reads the note to verify the operator's claim

---

## Part 10: The audit-line discipline

### The rule

Every agent action that changes vault state produces an audit line. The audit line is **specific, brief, and append-only**.

### The audit-line format

```
## [ISO8601-UTC] <action-kind> — <subject>: <what changed> (<optional strategy>)
```

The action kinds are: `read`, `propose`, `edit`, `scaffold`, `archive`, `wikilink-create`, `tag-apply`, `move`, `delete`.

### When audit lines are written

- **Reads:** per major query, not per file access
- **Proposes:** per proposal
- **Edits:** per edit
- **Scaffolds:** per scaffold
- **Archives:** per archive
- **Wikilink creations:** per wikilink (or batched per note)
- **Tag applications:** per note (not per tag)
- **Moves:** per move
- **Deletes:** per delete (deletes are rare; archive instead)

### The audit-line discipline

- **Audit lines are append-only.** Never edited.
- **Audit lines are specific.** "Edited note-id" is bad. "Edited note-id: added section X, removed section Y" is good.
- **Audit lines are brief.** ≤100 chars per line.
- **Audit lines are timestamped.** ISO8601-UTC.
- **Audit lines are queryable.** The agent can grep the audit log for patterns.

---

## Part 11: Interaction anti-patterns

### Anti-pattern 1: "The agent reads everything."

No. The agent has a context budget (≤100K chars on-demand). Reading everything blows the budget and dilutes the signal.

### Anti-pattern 2: "The agent writes without reading."

No. The read-then-write discipline prevents duplicates, conflicts, and lost context. Always read before write.

### Anti-pattern 3: "The agent scaffolds without operator review."

No. New scaffold patterns are operator-actionable. The agent must propose, not create.

### Anti-pattern 4: "The agent archives by judgment."

No. Archives are evidence-based (status=stale, age+no-links, duplicate-confirmed, operator-instruction). Judgment is not evidence.

### Anti-pattern 5: "The agent uses last-write-wins for collaborative edits."

No. Concurrent-write protection has 3 strategies; the right one depends on the kind of write. Last-write-wins is for independent writes only.

### Anti-pattern 6: "The agent writes audit lines per file access."

No. Audit lines are per major action, not per file access. The audit log is for the operator, not for the agent's bookkeeping.

### Anti-pattern 7: "The agent reads the vault and forgets to audit."

No. The reader pattern requires audit lines per major query.

### Anti-pattern 8: "The agent has unlimited tool-call budget."

No. The default budget is ≤50 reads, ≤30 edits/proposes, ≤20 scaffolds/archives per session. The budget forces discrimination.

### Anti-pattern 9: "The agent creates parallel structures."

No. A new tier / sub-vault that duplicates an existing one is operator-actionable. The agent must propose, not create.

### Anti-pattern 10: "The agent writes to literature or archive tiers."

No. Literature is append-only after first write; archive is move-only. The agent can read these tiers; it cannot write to them.

### Anti-pattern 11: "The agent imports the whole vault (or unrelated skills) on cold-start."

No. The agent's working context is **scoped** — it imports only the cold-start schema, the skills assigned to its domain, and the context for its active work. See Part 13.

---

## Part 12: When to revise the interaction design

The interaction design is **not static.** Revise when:

1. **A new pattern is added.** (e.g., a "summarize" pattern that produces summaries without writing)
2. **A budget is consistently hit.** (Budget too small.)
3. **A budget is consistently underused.** (Budget too large.)
4. **A conflict-resolution strategy fails.** (Concurrent writes produce corruption.)
5. **A new failure mode emerges.** (e.g., the agent reads a sensitive note and leaks it.)

The operator revises the design **when the design is the bottleneck, not before.**

---

## Part 13: The scoped context import rule

### The rule

**An agent's working context is the union of three things — and nothing else.**

An agent's working context on a given task is the union of:

1. **The cold-start schema** — the AGENTS.md (Tier 1) and the agent SOUL (Tier 3). What every launch already loads.
2. **The skills assigned to the agent's domain** — the procedures the agent owns. Not "every skill in the system." Not "the skills the agent might need someday." The skills whose trigger conditions match the agent's domain and the current task.
3. **The context for the agent's active work** — the spec docs, operator notes, prior-ticket comments, and references that the active ticket (or operator instruction) explicitly names. Loaded on demand, per the load strategies in Part 6.

That's it. The agent does **not** import the entire vault on cold-start. The agent does **not** import skills outside its domain "just in case." The agent does **not** read every note in the tier "for context" before acting. Each load is a deliberate decision tied to the current task.

### Why scoping matters

The agent's context window is finite. Three failure modes follow from importing without scoping:

1. **Context window bloat.** Loading the whole vault on cold-start exceeds the context budget (Part 6). The agent's reasoning quality degrades as the signal dilutes.
2. **Cross-domain bleed.** An agent that imports skills outside its domain will, when faced with a task, pattern-match against the wrong procedure. A research agent that imports the `hermes-orchestrator-handoff` skill will produce handoff memos when it should produce findings.
3. **Untraceable behavior.** The operator cannot audit "what did the agent know?" if the agent imports the world. A scoped import gives the operator a closed surface: the AGENTS.md, the skill list, the active work's references. Three files to check, not three thousand.

The scoped import rule is the **principle** that Part 6 (the agent context budget) and the 4 load strategies (cold-start, topic, note, query) enforce in practice. The rule is what the budget is protecting.

### What counts as "relevant to the agent's domain"

The agent's domain is the **scope of its role** — the work the operator designed the agent to do. The relevance test for a load is one of:

- **Skill-load relevance:** the skill's trigger conditions match the current task. If the skill says "load when the operator asks about X," the agent loads it when the operator asks about X — not when the operator asks about Y.
- **Note-load relevance:** the active ticket (or operator instruction) names the note, OR the note is the wikilink-1-hop neighborhood of a note the active ticket names. The agent does not walk the wikilink graph on cold-start.
- **Topic-load relevance:** the operator (or the active ticket) names the topic. The agent loads the topic MOC + the linked notes. The agent does not pre-load topics "for future use."
- **Query-load relevance:** the agent has a specific question that requires the result. The agent does not run broad queries to "see what's there."

If the agent cannot justify a load against one of these four tests, the load is **out of scope** and the agent does not make it.

### The anti-pattern

**Anti-pattern A: Cold-start loads the entire vault.**

The agent's launch hook reads `root.read_text()` and concatenates the AGENTS.md with every note in the vault. The context window hits the model's ceiling by turn 3. The agent's reasoning quality drops. The operator cannot tell which note informed which decision.

**Anti-pattern B: The agent loads skills outside its domain.**

The agent's SOUL is "research analyst." The system has 50 skills, of which 12 are research-domain and 38 are not. The agent loads all 50 "in case." The agent now has skill names in its context that pattern-match against unrelated tasks — when the operator asks for a literature review, the `cronjob` skill's trigger fires because the word "review" appears in both, and the agent produces a cron schedule instead of a literature review.

**Anti-pattern C: The agent pre-loads context "for the next task."**

The agent finishes a triage task. Before exiting, it reads the next 10 notes in the inbox "so the next session has context." The agent's context window is now full of notes the triage task did not need. The next session's reasoning is shaped by notes it should not have seen.

**Anti-pattern D: The agent walks the wikilink graph from a note it just loaded.**

The agent loads a note for the active task. It then follows every wikilink to depth 2 "for context." The depth-2 neighborhood is 40 notes. The active task needed 1 note. The agent spent 39 of 40 loads on out-of-scope context.

### What the agent does instead

For each load, the agent asks: **"What task or trigger justifies this load?"** If the answer is "the active ticket named this note" or "the skill's trigger conditions match the current task," the load is in scope. If the answer is "I might need it" or "it seemed related," the load is out of scope and the agent does not make it.

When the agent is uncertain whether a load is in scope, the right response is to **ask the operator or file a `needs_input` ticket** — not to load the note and figure it out later. The scoped import rule says: the cost of an out-of-scope load is paid immediately (context window bloat, cross-domain bleed), and the cost of asking is paid by the operator (a clarification). The operator's time is cheaper than the agent's context window.

### How this rule fits with the rest of the methodology

- **Part 6 (the agent context budget)** is the **budget** the scoped import rule protects. The 4 load strategies (cold-start, topic, note, query) are the **load shapes** the rule permits.
- **Part 3 (the read-then-write discipline)** is the **per-write rule** for reads. The scoped import rule is the **per-session rule** for reads. Read-then-write prevents "write without reading the related notes." Scoped import prevents "load without a task justifying the load."
- **The 5 interaction patterns** (Part 2) define what the agent does with the loaded context. The scoped import rule defines what context the agent loads in the first place.
- **The anti-pattern in Part 11 (item 11)** is the agent-side symptom of violating the rule. The rule is the operator-side accounting for why the symptom is wrong.

### Audit evidence

A scoped-import violation is hard to detect after the fact, because the context window is opaque. The audit discipline is: **the agent's `--summary` for any task names the skills it loaded and the notes it read.** If the summary lists 30 skills and 50 notes, the agent has likely violated the rule. If the summary lists 3 skills and 5 notes, the load is scoped.

The audit-line shape for a scoped-import violation:

```
## [ISO8601-UTC] scoped-import-violation — <agent-id>: <which load was out of scope> — <justification that was missing>
```

The audit line is the operator's hook for catching the failure mode. The rule is what the agent follows to prevent the audit line from being written.

---

## Worked example (skeleton)

The 2 worked examples (solo-founder, research-analyst) show the methodology applied to a specific instance. The examples are skeletons, not snapshots.

The friend reads the methodology, then designs their own interaction patterns for their own agents. The friend does NOT copy the worked example's permissions; they apply the method to derive their own.

---

## Maintenance

> This section is the audit surface for **agent-vault interaction patterns** (read-only, propose, edit, scaffold, archive). It mirrors the canonical 5-subsection shape from `methodology/_templates/maintenance-scaffold.md.template` so a friend reading any maintenance section in `methodology/` sees the same rhythm: when to audit, what counts as healthy, what drift looks like, what to do when drift is found, and when the section itself should be retired. The cadence and quality bar are methodology defaults; map each to your tool before adopting.

### 1. Audit cadence

Audit **agent-vault interaction patterns** on a **fixed cadence** — every **90 days**. Agent-vault patterns are stable: the 5 interaction levels (read-only, propose, edit, scaffold, archive), the read-then-write discipline, the per-pattern tool-call budgets, and the context budget all change slowly. The cadence is a calendar event, not "when I remember." Off-cycle audits fire when a signal in §3 trips before the next scheduled review.

Record each audit with: date, auditor (operator or named reviewer profile), the per-agent interaction levels under review, verdict per check in §2, corrective action if any, next-audit date. Store the audit record on the relevant comment thread, master ticket, or the methodology's changelog so the trail survives across sessions.

### 2. Quality threshold

Agent-vault interaction patterns pass the maintenance check when **all** of the following hold:

- **Each agent has a documented interaction level.** For every agent in the system, the AGENTS.md / SOUL.md names which of the 5 patterns (read-only, propose, edit, scaffold, archive) the agent uses for each tier. An agent with no documented pattern is unaccountable.
- **The per-agent tool-call budget is recent.** The tool-call budgets in §Part 4 reflect the last 90 days of observed usage — not defaults copied from the methodology. A budget that has not been reviewed against actual session logs is a stale budget.
- **The agent context budget is enforced.** The 4 load strategies (cold-start ≤20K, topic ≤50K, note ≤30K, query ≤10K; cumulative ≤100K on-demand per session) are enforced by the platform, not by agent self-report. A budget that the agent can silently exceed is no budget.
- **The per-pattern guardrails are intact.** The archivist evidence-based rule (§Part 8) and the scaffolder approved-pattern rule (§Part 7) are observable in recent agent behavior, not just documented in the methodology.
- **Drift signals from §3 have a recorded disposition.** Every drift signal raised since the last audit has either been resolved (with a corrective action) or is in flight on a maintenance ticket with an owner. No orphan signals.

The thresholds are review gates, not formatting games. Marking a check "passes" because the agent "looks healthy" fails the underlying test. The check is whether the documented condition is observable in the system today, not whether the operator believes it is.

### 3. Drift signals

Drift is observable. Surface at least one of the following before the next scheduled audit:

- **An agent is using a pattern it should not.** A read-only agent is editing notes; a propose-only agent is committing changes without operator review; an archivist is making judgment-based moves without evidence. The pattern assignment in the AGENTS.md / SOUL.md has drifted from the observed behavior.
- **The context budget is regularly exceeded.** Recent agent sessions routinely exceed the 100K cumulative on-demand load cap, or routinely exceed the 20K cold-start cap. The budget is not protecting the context window as designed.
- **The agent is repeatedly loading too many notes.** An agent's `--summary` lists 30+ notes read for a single task, or walks the wikilink graph past 1 hop without a ticket-justified reason. The scoped context import rule (§Part 13) has failed in practice.
- **The tool-call budget is consistently hit or consistently ignored.** Agents either blow the per-pattern budget on every session (budget is too small or the work is unstructured) or use <50% on every session (budget is too large; tighten). Either signal means the budget is not calibrated to the work.
- **The per-pattern guardrail fires without a record.** A scaffold was created outside an approved scaffold pattern with no `propose` ticket; an archive happened without a `status=stale`, `age>30d-no-active-links`, or `duplicate-confirmed` reason. Guardrails are firing and being bypassed.

A drift signal does not always mean the rule is wrong. Sometimes the rule is right and the writer is wrong; sometimes the platform cannot enforce it; occasionally the rule is obsolete and the signal is the prompt to revise. The audit names the signal and proposes a disposition; the owner confirms.

### 4. Fix actions

When drift is detected, the canonical response is:

1. **File a maintenance ticket** on the operator's kanban board (default board: `hermes`). Name the drift signal, the offending agent(s), the recent session(s) where the signal appeared, and the suspected cause.
2. **The methodology owner reviews** the per-agent interaction levels, the per-pattern tool-call budgets, the platform enforcement of the context budget, and the corrective actions from prior audits. The owner does not unilaterally rewrite the methodology; the owner proposes.
3. **The owner re-issues the per-agent pattern assignment or the budget.** Two outcomes are valid: (a) the rule was right and the agent or writer was wrong — fix the agent's SOUL.md / AGENTS.md or the writer's process, then re-audit; (b) the rule is obsolete — open a methodology-revision ticket, then re-apply after the new rule lands.
4. **Run the recent-knowledge-state check** for any agent that has been exceeding the context budget: the operator reviews the last 10 sessions, counts the loaded notes per session, and identifies whether the loads were ticket-justified. An unjustified load count is the scoped-import violation that the budget was supposed to prevent.
5. **Audit the agent's recent task history** for any agent using a pattern it should not: the operator pulls the last 10 sessions, checks the audit lines against the documented pattern assignment, and names the first session where the drift began. The audit names drift, not blame.
6. **The disposition is recorded** on the ticket: cause, corrective action, next review date. A drift signal with no recorded disposition is unresolved.

Do not auto-fix drift by editing the agent's SOUL.md to match observed behavior. A condition that fails is a routing failure or a tooling failure; the fix is to address the underlying cause, not to rewrite the pattern assignment to match the drift.

### 5. Retirement conditions

Retire the maintenance section (or the agent itself) when **at least one** of the following is observable for 90 consecutive days:

- **An agent consistently exceeds the context budget.** Every session for three months runs over the 100K cumulative on-demand cap or the 20K cold-start cap, even after the budget has been tightened and the recent-knowledge-state check has been run. The agent's domain is too broad for the per-session context budget to be a workable constraint; the agent's scope must shrink or the agent must be retired.
- **An agent uses no interaction patterns.** The agent's audit lines for 90 consecutive days show no reads, no proposes, no edits, no scaffolds, no archives. The agent is not a useful agent — it has no vault footprint, no work product, and no measurable contribution to the system.
- **The methodology is no longer in use.** No new agent has been assigned an interaction level from this methodology in three months; the operator has shifted to a different interaction model (e.g., a fully read-only research agent set, or a fully propose-only draft pipeline) and the 5-pattern matrix no longer describes the system.
- **The methodology is consistently skipped.** New agents are onboarded with their interaction level set by intuition, not by this methodology, and the maintenance check is not invoked. The section has become documentation theater.
- **A simpler methodology has replaced it.** A different section in `methodology/` covers agent-vault interactions with lower overhead; the older section is dead weight.

The retirement sequence:

1. Propose retiring the agent (or merging the methodology with the adjacent section that covers the same ground). Name the surviving agent or methodology.
2. Move all in-flight tickets, audit records, and references from the retired agent or methodology to the surviving target, preserving references and adding a `migrated_from:` field so the move is auditable.
3. Update every MOC, catalog entry, agent assignment, and cross-reference that pointed at the retired entity.
4. Keep the section or agent profile present as an empty placeholder for one full audit cycle (90 days) so any late-arriving references surface. Remove the placeholder only after the cycle completes with no inbound references.
5. Record the retirement in the methodology changelog: date, surviving target, contents moved, references updated, owner.

### Maintenance parity check

This section defines a friend-portable method, not a claim that every platform supplies automated pattern-assignment audits, context-budget enforcement, or guardrail-firing logs. Before adopting it, map each function — per-agent pattern audit, budget enforcement review, drift surfacing, corrective routing, retirement archival — to mechanisms available in your own tool. The 90-day cadence is a methodology default (agent-vault patterns are stable); adjust when measured drift rate, instance volume, or operator-stated risk provides better evidence, but record the exception so the audit trail remains intact.

> **Method-not-instance reminder.** Alice documents the method, not the specific tool or operator. Replace per-agent specifics with the names of the friend's own agents; do not paste instance-specific paths, commands, or platform names into the section body.

---

## See also

- `methodology/03b-decide-operator-agent-interaction.md` — the agent-vault doc covers the 5 patterns (read-only, propose, edit, scaffold, archive). The operator-agent doc covers the **operator's perspective** on each pattern: approve for Pattern 2 (Propose), review for Pattern 4 (Scaffold), capture for Pattern 5 (Archive). The two docs are siblings.
- `methodology/03c-decide-agent-communication-channels.md` — in a multi-agent system, downstream agents still follow the 5 patterns (read-only, propose, edit, scaffold, archive) but only against the ticket comment thread + vault, NOT against the operator's DM. The communication-channels doc explains the channel isolation.

## What's next

- `methodology/01-decide-vault-tier.md` — the tier doc covers WHERE; this doc covers HOW THE AGENT ACTS
- `methodology/01a-decide-memory.md` — the memory doc covers tier budgets; this doc covers tool-call budgets
- `methodology/01b-decide-vault-content.md` — the content doc covers lifecycle stages; this doc covers the agent's role per stage
- `methodology/03-decide-agents.md` — the agent design doc covers identity; this doc covers action
- `methodology/05-op-guards.md` — operational guards like "no direct mutation" depend on the interaction pattern
- `templates/agent-vault-permissions.md.template` — the fillable form for an agent's per-tier permissions
- `templates/agent-soul.md.template` — the SOUL has a Tools section; expand with per-tier permissions

## Anti-patterns to watch for (summary)

1. **"The agent reads everything."** → No. Context budget.
2. **"The agent writes without reading."** → No. Read-then-write discipline.
3. **"The agent scaffolds without operator review."** → No. New patterns are operator-actionable.
4. **"The agent archives by judgment."** → No. Evidence-based only.
5. **"The agent uses last-write-wins for collaborative edits."** → No. Pick the right strategy.
6. **"The agent writes audit lines per file access."** → No. Per major action.
7. **"The agent reads the vault and forgets to audit."** → No. Audit per major query.
8. **"The agent has unlimited tool-call budget."** → No. Per-pattern budgets.
9. **"The agent creates parallel structures."** → No. Operator-actionable.
10. **"The agent writes to literature or archive tiers."** → No. Read-only on those tiers.
11. **"The agent imports the whole vault (or unrelated skills) on cold-start."** → No. Scoped context import. See Part 13.

## Touch-points with other methodology docs

This doc touches:

- `methodology/01-decide-vault-tier.md` — tier doc lists "different rules per tier"; expand with the 5-pattern framework
- `methodology/01a-decide-memory.md` — memory doc covers tier budgets; this doc covers tool-call budgets
- `methodology/01b-decide-vault-content.md` — content doc covers lifecycle stages; this doc covers the agent's role per stage
- `methodology/02-decide-skills.md` — skills are loaded on trigger conditions, not "all in case"; the scoped context import rule (Part 13) is the principle that the skill-trigger discipline enforces
- `methodology/03-decide-agents.md` — agent design covers identity; this doc covers action
- `methodology/05-op-guards.md` — operational guards like "no direct mutation" depend on the interaction pattern
- `templates/agent-vault-permissions.md.template` — the fillable form for an agent's per-tier permissions
- `templates/agent-soul.md.template` — the SOUL has a Tools section; expand with per-tier permissions
## What's next (Part 13: scoped context import)

Part 13 codifies the scoped context import rule: an agent's working context is the union of (a) the cold-start schema, (b) the skills assigned to its domain, (c) the context for its active work — and **not** the entire vault or unrelated skills. The rule is the principle that Part 6 (the agent context budget) and the 4 load strategies enforce in practice.
