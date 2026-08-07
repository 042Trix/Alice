---
id: alice-methodology-01b-decide-vault-content
created: 2026-08-04T12:30:00Z
title: "Methodology 01b — Decide your vault content management (frontmatter, tags, lifecycle, versions)"
type: methodology
status: draft
source: alice-framework
tags: [kind:methodology, kind:vault-content, project:alice]
confidence: 0.0
links: ["[[methodology/01-decide-vault-tier.md]]", "[[methodology/01a-decide-memory.md]]", "[[methodology/08-inbox-route.md]]", "[[methodology/09-inbox-from-external-sources.md]]", "[[templates/frontmatter-schema.md.template]]", "[[references/tier-routing-cheatsheet.md]]", "[[references/x-ingestion-pipeline.md]]"]
---

# Methodology 01b — Decide your vault content management

> **The vault tier doc tells you WHERE notes live. This doc tells you HOW to write them.** A vault with bad content management is a junk drawer: bloated frontmatter, conflicting tags, broken wikilinks, atomicity violations, missing MOCs (Maps of Content — topic-level indexes that cross folders). Without content-management discipline, the vault degrades within 100 notes.

This methodology covers **the design of vault content management** — frontmatter schema, tag namespacing, note IDs, wikilinks, atomicity rules, note lifecycle, version discipline, MOCs, embeddings, and backup/recovery. The methodology is tool-agnostic. The canonical implementation in the worked examples uses markdown + frontmatter + wikilinks. The tool-mapping guide (`references/tool-mapping-guide.md`) says how to implement this in your specific tool.

**Documentation convention:** Alice documents the **method**, not specific instances. This doc does not list any specific frontmatter fields or tag namespaces. The friend applies the method to their own context.

---

## Part 1: Why content management matters

### The decision

The operator (or the agent) decides:
- **What fields does every note have?** (the frontmatter schema)
- **What tags can a note have?** (the tag namespacing)
- **How is a note identified?** (the note ID)
- **How do notes link to each other?** (the wikilink convention)
- **When does a note split into two?** (the atomicity rule)
- **What happens when a note matures?** (the note lifecycle)
- **When does a note get revised?** (the version discipline)
- **When does a MOC get written?** (the MOC rule)
- **Does the vault need embeddings?** (the semantic-search decision)
- **What gets backed up?** (the backup discipline)

Without a content-management discipline, the answers are implicit. The operator writes whatever feels right, the agent guesses, and the vault degrades silently. The friend running Alice's methodology has no way to recover from degradation without a unified discipline.

### The 5 failure modes

A vault content-management system fails in 5 predictable ways:

1. **Frontmatter bloat.** Every note has 30 fields, half of them empty, none of them queryable. The operator can't tell what's important.
2. **Tag proliferation.** 200 tags, no namespacing, no consistency. The operator can't filter by tag.
3. **Wikilink rot.** `[[note-id]]` references that point to deleted notes. The vault has dangling links.
4. **Atomicity violations.** A note that's grown to 5000 words and covers 5 ideas. The operator can't grep it.
5. **MOC absence.** No map of the vault; the operator navigates by hierarchy only. The operator gets lost.

The content-management discipline is the design that prevents all 5.

---

## Part 2: Frontmatter schema design

### Why frontmatter matters

Every note has **structured metadata** that the agent and the operator can query. The frontmatter is the schema for that metadata. Without a schema, the metadata is arbitrary and unqueryable.

### The minimum frontmatter schema

Alice recommends this minimum schema:

```yaml
---
id: <note-identifier>
created: <ISO8601-UTC>
updated: <ISO8601-UTC>
type: <atomic | literature | inbox | moc | decision | archive | citation | ...>
status: <draft | active | stale | archived>
source: <who/where wrote this>
tags: [<namespace>:<value>, ...]
confidence: <0.0-1.0>
links: [<related-note-id>, ...]
---
```

Each field has a specific purpose:

- **`id`** — the unique identifier. Used in wikilinks and cross-references.
- **`created`** — when the note was first written.
- **`updated`** — when the note was last edited.
- **`type`** — what kind of note this is. Drives routing and tooling.
- **`status`** — what lifecycle stage the note is in. Drives archival.
- **`source`** — who or what wrote the note (operator, agent, external source).
- **`tags`** — namespaced tags for filtering.
- **`confidence`** — the operator's or agent's confidence in the note's content.
- **`links`** — wikilinks to related notes.

### When to add a field

Add a frontmatter field when:
1. **The field is queryable.** (The operator or agent can filter / sort / group by it.)
2. **The field is stable.** (The value doesn't change every edit.)
3. **The field has a clear source.** (The operator or agent knows who fills it.)

Don't add a field when:
1. **The field is one-off.** (Only 1-2 notes would have it.)
2. **The field is derived.** (It can be computed from other fields.)
3. **The field is opinion.** (No agreed-upon source of truth.)

### The frontmatter discipline

- **Every note has all required fields.** Missing fields are an editorial gap.
- **Required fields are validated.** The build / commit step fails if a required field is missing.
- **Optional fields are explicit.** The schema lists which fields are required vs optional.
- **Field values are normalized.** Date formats are ISO8601-UTC; tag values are lowercase; confidence is 0.0-1.0 (not "high" / "medium" / "low").

### When to revise the schema

Revise the frontmatter schema when:
1. **A new field is queryable** (e.g., the operator wants to filter by author)
2. **An existing field is unused** (e.g., no note has `confidence` set)
3. **A field's source is unclear** (e.g., `updated` is sometimes the operator, sometimes the agent)

Revise the schema **with a migration plan.** Adding a field requires updating all existing notes. Removing a field requires archiving the data somewhere.

---

## Part 3: Tag namespacing design

### Why namespacing matters

Tags are the operator's and agent's filter for "show me notes about X." Without namespacing, tags are arbitrary strings and the filter is unreliable.

### The minimum tag namespaces

Alice recommends these minimum namespaces:

- **`domain:`** — the topical area (e.g., `domain:aviation`, `domain:loop-design`)
- **`project:`** — the project the note belongs to (e.g., `project:alice`, `project:customer-research`)
- **`kind:`** — the kind of note (e.g., `kind:spec`, `kind:rule`, `kind:incident`)
- **`source:`** — where the note came from (e.g., `source:operator`, `source:x-post`, `source:literature`)
- **`private:`** — privacy marker (e.g., `private:operator`, `private:redact`)

Each namespace has a specific purpose:

- **`domain:`** drives topical filtering. The operator wants to find "all my aviation notes" — `domain:aviation`.
- **`project:`** drives project filtering. The operator wants to find "all notes in the Alice project" — `project:alice`.
- **`kind:`** drives kind filtering. The operator wants to find "all my rules" — `kind:rule`.
- **`source:`** drives provenance filtering. The operator wants to find "all notes I wrote myself" — `source:operator`.
- **`private:`** drives privacy filtering. The operator wants to find "all notes that contain private info" — `private:operator`.

### When to add a namespace

Add a tag namespace when:
1. **The namespace is queryable.** (The operator or agent can filter by it.)
2. **The namespace has stable values.** (Values don't change every week.)
3. **The namespace doesn't overlap with existing ones.** (Two namespaces covering the same ground is a refactor candidate.)

Don't add a namespace when:
1. **The namespace is one-off.** (Only 1-2 notes would use it.)
2. **The namespace is a duplicate.** (It covers what `domain:` or `kind:` already covers.)
3. **The namespace is ephemeral.** (The values disappear after a few weeks.)

### The tag discipline

- **Tags are namespaced.** A tag without a namespace is a bug.
- **Tags are lowercase.** `kind:Rule` and `kind:rule` are different tags; the operator wants them merged.
- **Tags are flat.** `domain:aviation:drone` is a hierarchy; the operator wants `domain:aviation` and `domain:drone` as separate tags.
- **Tag values are not arbitrary.** `domain:foo` is suspicious; `domain:aviation` is meaningful.
- **The tag namespace has a closed list.** The operator writes down what namespaces exist and what each value means.

### The tag audit

Run the tag audit quarterly:

1. **List all tags.** (Use the manifest or grep the frontmatter.)
2. **Identify orphans.** (Tags that appear on only 1-2 notes.)
3. **Identify duplicates.** (Tags that mean the same thing but are spelled differently.)
4. **Identify proliferation.** (Tags that should be split into multiple tags.)

The audit produces a tag-cleanup ticket. The ticket is operator-actionable (the agent can't decide to rename tags unilaterally).

---

## Part 4: Note IDs

### Why note IDs matter

Every note has a unique identifier. The identifier is used in:
- **Wikilinks** — `[[note-id]]` references the note by ID, not by path
- **Cross-references** — "see note-id" in the body
- **Audit lines** — "revised note-id at time T"
- **Queries** — "SELECT * FROM notes WHERE id = ?"

Without a stable ID, the note can't be reliably referenced. A note's path may change; its ID doesn't.

### The minimum ID convention

Alice recommends this minimum:

- **Format:** 7-character alphanumeric hash (e.g., `a1b2c3d`)
- **Generation:** hash of the note's first heading or content (deterministic)
- **Uniqueness:** guaranteed by the ID generator (collision check at generation time)

### When to use a longer ID

Use a longer ID (12+ chars) when:
1. **The vault has >10,000 notes.** (7 chars is collision-prone at this scale.)
2. **The notes are mission-critical.** (Mistyped ID = wrong reference.)

Use a shorter ID (4-5 chars) when:
1. **The vault has <100 notes.** (Collision is unlikely.)
2. **The notes are scratch / ephemeral.** (ID stability matters less.)

### When to revise the ID convention

Revise the ID convention when:
1. **The vault crosses a size threshold.** (1k, 10k, 100k notes.)
2. **A collision is detected.** (Two notes with the same ID.)
3. **A new tool requires a different format.** (Some tools need UUIDs.)

Revising the ID convention requires a migration: every note gets a new ID, every wikilink is updated, every audit line is updated. **This is expensive.** Don't revise unless necessary.

---

## Part 5: Wikilink convention

### Why wikilinks matter

Wikilinks are how notes reference each other. Without wikilinks, the vault is a flat list of files. With wikilinks, the vault is a graph.

### The minimum wikilink convention

Alice recommends:

- **`[[note-id]]`** — link by ID. Most stable; survives path changes.
- **`[[note-id|display text]]`** — link with custom display text. For readability.
- **`[[path/to/note]]`** — link by path. Less stable; breaks on path changes.
- **`[text](path)`** — markdown link. For external references.

### When to use which form

- **Use `[[note-id]]`** for internal references. The agent and the operator can resolve the ID.
- **Use `[[note-id|display text]]`** when the display text differs from the note's title.
- **Use `[[path/to/note]]`** rarely — only when the path is the canonical reference (e.g., a folder, not a note).
- **Use `[text](path)`** for external references (URLs, file paths outside the vault).

### The wikilink discipline

- **Wikilinks resolve at build time.** The agent builds a wikilink graph when it indexes the vault.
- **Wikilinks to deleted notes are errors.** The build fails or emits a warning.
- **Wikilinks to renamed notes auto-update.** The ID-based link survives renames.
- **Wikilink resolution is cached.** The agent doesn't re-resolve on every query.

### The wikilink audit

Run the wikilink audit quarterly:

1. **List all wikilinks.** (Grep `[[` in the body.)
2. **Resolve each.** (Check if the target exists.)
3. **Identify dangling links.** (Wikilinks to deleted notes.)
4. **Identify path-based links.** (Replace with ID-based where possible.)

---

## Part 6: Atomicity rules

### Why atomicity matters

A note is **atomic** when it captures one idea. A note that's grown to cover 5 ideas is hard to query, hard to maintain, and hard to link. The atomicity rule is the design that keeps notes queryable.

### The atomicity test

A note is atomic when **all four** are true:

1. **One idea.** The note has one main thesis or one main concept.
2. **Greppable.** The operator can grep the note for one keyword and find the relevant content.
3. **Linked.** The note links to (or is linked from) at most 5-10 other notes.
4. **Bounded.** The note is <2000 words; longer notes split.

### When to split a note

Split a note when:
1. **The note covers 2+ distinct ideas.** → split into 2 notes.
2. **The note is >2000 words.** → split into 2+ notes.
3. **The note has 2+ unrelated sections.** → split by section.
4. **The note has 5+ unrelated wikilinks.** → split by topic cluster.

Don't split a note when:
1. **The note is a single coherent argument.** → keep it.
2. **Splitting would orphan a paragraph.** → add a summary note that links to the parts.

### When to merge two notes

Merge two notes when:
1. **Both notes cover the same idea from different angles.** → merge into one.
2. **Both notes have the same wikilinks.** → merge.
3. **One note has a 1-paragraph section that's the whole of the other.** → merge.

Don't merge when:
1. **The notes are different versions of the same idea.** → keep both, supersede one.
2. **The notes are written at different times.** → keep both, link them.
3. **The merge would create a 5000-word note.** → don't merge.

---

## Part 7: Note lifecycle

### The 6 stages

Every note moves through 6 lifecycle stages:

```
1. Capture    →  2. Triage    →  3. Process    →  4. Mature    →  5. Stale    →  6. Archive
```

### Stage 1: Capture

The note is **captured** — written into the inbox tier (Tier 0). The note is raw; no edits yet.

**Owner:** operator or agent.

**Audit line:** `## [ISO8601-UTC] capture — <note-id>: <source>`

> Note: the audit-line schema above is the canonical shape. Your audit-line format may differ — the schema captures what to record (the verb, the note identifier, and the reason or source), not the exact bytes. Subsequent stages below reference this schema rather than repeating it verbatim.

### Stage 2: Triage

The note is **triaged** — reviewed and routed to its destination tier. The triage applies the 5-question routing decision (see `methodology/08-inbox-route.md`).

**Owner:** operator or agent.

**Audit line:** `triage — <note-id>: <from> → <to>: <reason>` (per the Stage 1 schema)

### Stage 3: Process

The note is **processed** — edited, structured, linked. The note gets full frontmatter, tags, wikilinks. The processing depends on the destination tier:
- **Atomic:** edit freely, link to related notes
- **Literature:** append-only after first write, link from citing notes
- **Decision:** add decision rationale and outcome
- **Council:** add seat positions and verdict

**Owner:** operator.

**Audit line:** `processed — <note-id>: <edits>` (per the Stage 1 schema)

### Stage 4: Mature

The note is **mature** — it's referenced by other notes, queried, used. The mature stage is the longest stage.

**Owner:** operator (occasional edits).

**Audit line:** (only on edit)

### Stage 5: Stale

The note is **stale** — outdated, superseded, or no longer relevant. The status field changes to `stale`. The note is still queryable but flagged.

**Owner:** operator.

**Audit line:** `stale — <note-id>: <reason>` (per the Stage 1 schema)

### Stage 6: Archive

The note is **archived** — moved to Tier 4 (archive). The note is no longer surfaced by default but is retrievable.

**Owner:** operator.

**Audit line:** `archive — <note-id>: <reason>` (per the Stage 1 schema)

### The lifecycle discipline

- **Capture is fast.** No edits at capture. Just write the raw content.
- **Triage is timely.** Inbox items are triaged within 30 days. Older items are archived without triage.
- **Process is bounded.** A note's processing time is <30 min. Longer processing = split the note.
- **Mature is long.** A mature note can stay mature for years.
- **Stale is honest.** A note is marked stale when it's actually stale, not because it's old.
- **Archive is reversible.** Archived notes can be un-archived if needed.

---

## Part 8: Version discipline

### Why version discipline matters

Notes change. The discipline determines **when to change them, when to leave them alone, and how to track changes.**

### The version discipline

- **Revise a note when:** the content is wrong, outdated, or incomplete.
- **Don't revise a note when:** the content is correct and current.
- **Don't supersede a note by editing it** — write a new note that supersedes.
- **Don't delete a note** — archive it.

### The audit trail

Every revision has an audit line:

```
## [ISO8601-UTC] revise — <note-id>: <reason>
```

The audit line is in the vault log, not in the note itself. The note's frontmatter has `updated:` to track the timestamp; the body doesn't track per-edit history.

### The version-control decision

Some tools have built-in version control (git, obsidian sync, etc.). Others don't.

**If your tool has version control:** use it. The audit trail in the log is the operator-readable summary; the version-control history is the full record.

**If your tool doesn't have version control:** rely on the audit trail. Each revision is logged; the operator can reconstruct the history from the log.

### When to revise vs leave alone

- **Revise when** the note is referenced by another note that's been updated. The operator should keep the references consistent.
- **Revise when** the operator's understanding has changed.
- **Revise when** the external source has changed (a paper is retracted, an API is deprecated).
- **Leave alone when** the note is correct, current, and not referenced.

---

## Part 9: MOCs (Maps of Content)

### Why MOCs matter

A MOC is a vault index for a topic. Without MOCs, the operator navigates by hierarchy (folder structure). With MOCs, the operator navigates by topic — useful when the topic crosses folders.

### When to write a MOC

Write a MOC when:
1. **The topic crosses 3+ folders.** (The topic is a meta-topic.)
2. **The topic has 10+ notes.** (A MOC is useful at this scale.)
3. **The operator needs a single entry point.** (The MOC is the entry point.)

Don't write a MOC when:
1. **The topic fits in one folder.** (The folder structure is the MOC.)
2. **The topic has <10 notes.** (Too few to need a MOC.)

### The MOC discipline

- **MOCs are in the indexes tier.** (For example, `3-indexes/` per the naming chosen in `methodology/01-decide-vault-tier.md`.)
- **MOCs are regenerable.** The MOC is a computed view; rebuilding is automatic.
- **MOCs link to notes, not summaries.** The MOC is an index, not a digest.
- **MOCs are short.** A MOC is <500 words; longer MOCs are split.

### The MOC audit

Run the MOC audit quarterly:

1. **List all MOCs.**
2. **Identify orphan MOCs.** (MOCs that link to <5 notes.)
3. **Identify missing MOCs.** (Topics with 10+ notes and no MOC.)

---

## Part 10: Embeddings / semantic search

### When to add embeddings

Add embeddings to the vault when:
1. **The vault has >500 notes.** (FTS5 may not be enough.)
2. **The operator wants semantic search** ("find notes like this one").
3. **The operator has budget for embedding generation.** (OpenAI embeddings are ~$0.0001 per note.)

Don't add embeddings when:
1. **The vault has <500 notes.** (FTS5 is sufficient.)
2. **The operator doesn't need semantic search.** (Tag and link navigation is enough.)
3. **The operator doesn't have budget.** (Embeddings cost tokens and storage.)

### The embedding discipline

- **Embeddings are regenerated when notes change.** Stale embeddings = wrong search results.
- **Embeddings are stored in a separate index.** (For example, `3-indexes/embeddings/` per the naming chosen in `methodology/01-decide-vault-tier.md`.)
- **Embeddings are budgeted.** (≤50K vectors per literature note is a reasonable budget.)
- **Embeddings are operator-reviewable.** The operator should be able to delete / re-embed notes.

### The embedding audit

Run the embedding audit quarterly:

1. **List all embeddings.** (Count by note.)
2. **Identify stale embeddings.** (Embedding timestamp < note's `updated` timestamp.)
3. **Identify duplicates.** (Multiple embeddings for the same note.)

---

## Part 11: Backup + recovery

### Why backups matter

Vaults fail. Disk crashes, accidental deletes, git corruption. Without backups, the vault is fragile.

### The minimum backup discipline

- **Backup frequency:** weekly (or after a major edit batch).
- **Backup storage:** separate location (cloud, external drive, second machine).
- **Backup contents:** the entire vault, including the indexes (rebuildable but expensive).
- **Backup verification:** restore a backup quarterly and verify it's complete.

### The recovery discipline

- **Recovery is rehearsed.** The operator has restored a backup at least once.
- **Recovery is documented.** The recovery procedure is in the AGENTS.md or a separate doc.
- **Recovery is fast.** <30 min to restore from backup.

### The backup audit

Run the backup audit quarterly:

1. **List all backups.** (Where, when, size.)
2. **Verify the most recent backup.** (Restore to a temp location.)
3. **Identify gaps.** (Periods with no backup.)

---

## Part 12: Content-management anti-patterns

### Anti-pattern 1: "I'll add a frontmatter field for every need."

No. The frontmatter schema is **minimal by default.** Add a field only when it's queryable, stable, and has a clear source.

### Anti-pattern 2: "I'll have a tag for every concept."

No. Tags are namespaced and limited. Add a tag only when the namespace is queryable, stable, and doesn't overlap.

### Anti-pattern 3: "I'll write atomic notes that are 5000 words long."

No. A note is atomic when it covers one idea. Long notes split. If splitting would orphan a paragraph, add a summary note.

### Anti-pattern 4: "I'll link by path."

No. Link by ID. The ID survives path changes; the path doesn't.

### Anti-pattern 5: "I'll edit a note in place to supersede it."

No. Write a new note that supersedes the old one. The old note is archived with a `superseded_by:` field pointing to the new note.

### Anti-pattern 6: "I'll delete notes."

No. Archive notes. The vault is append-only at the tier level; deletes are reversals.

### Anti-pattern 7: "I'll write MOCs for every topic."

No. MOCs are for topics that cross 3+ folders with 10+ notes. Most topics don't need a MOC.

### Anti-pattern 8: "I'll skip backups."

No. Backups are weekly. Recovery is rehearsed quarterly.

### Anti-pattern 9: "I'll add embeddings because they're fancy."

No. Embeddings are for >500 notes + semantic search need + budget. Otherwise FTS5 is enough.

### Anti-pattern 10: "I'll never revise a note."

No. Notes change. Revise when wrong, outdated, or incomplete. Don't revise when correct and current.

---

## Part 13: When to revise the content-management discipline

The content-management discipline is **not static.** Revise when:

1. **A new frontmatter field is needed.** (Add the field, migrate existing notes.)
2. **A new tag namespace is needed.** (Add the namespace, migrate existing tags.)
3. **A new tier is needed.** (See `methodology/01-decide-vault-tier.md`.)
4. **A note crosses 2000 words.** (Split per the atomicity rule.)
5. **The wikilink audit finds >10 dangling links.** (Refactor the broken links.)
6. **The MOC audit finds missing MOCs.** (Write the missing MOCs.)
7. **The backup audit finds a gap.** (Re-establish the backup cadence.)

The operator revises the discipline **when the discipline is the bottleneck, not before.**

---

## Worked example (skeleton)

The 2 worked examples (solo-founder, research-analyst) show the methodology applied to a specific instance. The examples are skeletons, not snapshots.

The friend reads the methodology, then designs their own content-management discipline for their own context. The friend does NOT copy the worked example's schema; they apply the method to derive their own.

---

## What's next

- `methodology/01-decide-vault-tier.md` — the tier doc tells you WHERE notes live; this doc tells you HOW to write them.
- `methodology/01a-decide-memory.md` — vault content is Tier 5 (long-form) memory.
- `methodology/08-inbox-route.md` — the inbox → atomic transition is the most common lifecycle path.
- `methodology/09-inbox-from-external-sources.md` — X post → literature is a specific lifecycle path.
- `references/tier-routing-cheatsheet.md` — quick reference for tier routing.
- `references/x-ingestion-pipeline.md` — the operating spec for X post ingestion.
- `templates/frontmatter-schema.md.template` — the fillable form for the frontmatter schema.

## Anti-patterns to watch for (summary)

1. **"I'll add a frontmatter field for every need."** → No. Schema is minimal.
2. **"I'll have a tag for every concept."** → No. Tags are namespaced and limited.
3. **"I'll write atomic notes that are 5000 words long."** → No. Long notes split.
4. **"I'll link by path."** → No. Link by ID.
5. **"I'll edit a note in place to supersede it."** → No. Write a new note.
6. **"I'll delete notes."** → No. Archive notes.
7. **"I'll write MOCs for every topic."** → No. MOCs are for cross-folder topics.
8. **"I'll skip backups."** → No. Backups are weekly.
9. **"I'll add embeddings because they're fancy."** → No. Embeddings are for >500 notes + need + budget.
10. **"I'll never revise a note."** → No. Notes change.

## Touch-points with other methodology docs

This doc touches:

- `methodology/01-decide-vault-tier.md` — tier routing is parallel to content management
- `methodology/01a-decide-memory.md` — vault content is Tier 5 (long-form) memory
- `methodology/08-inbox-route.md` — inbox → atomic transition is the most common lifecycle path
- `methodology/09-inbox-from-external-sources.md` — X post → literature is a specific lifecycle path
- `references/tier-routing-cheatsheet.md` — quick reference for tier routing
- `references/x-ingestion-pipeline.md` — X post ingestion

## Maintenance

Designing the content-management discipline completes the build phase; it does not end the lifecycle. The 6-stage note lifecycle (Part 7) moves notes from `Capture` through `Archive`, but a vault with the right schema and no audit drifts quietly: notes get stuck in `Triage`, the daily triage cron stops firing, and a stage that has zero notes either is over-deployed or never sees a mover. The maintenance loop below mirrors `methodology/01-decide-vault-tier.md` and `methodology/02-decide-skills.md` §Skill maintenance in shape so a friend reading any of the maintenance sections in `methodology/` sees the same rhythm: when to audit, what counts as healthy, what drift looks like, what to do when drift is found, and when the section itself should be retired.

### 1. Audit cadence

Audit the content-management discipline on a **fixed cadence** (default: every **90 days**). Note lifecycle is slow — a 90-day sweep is the right resolution for catching notes stuck in a single stage and for surfacing stages that are silently unused. The cadence is a calendar event, not "when I remember."

Two cadences are appropriate:

- **Routine triage scan:** daily (or per the agent's triage cron schedule). It collects; it does not decide. The scan moves inbox items forward, identifies notes with no `stage` field, and flags items that have sat past their stage's age threshold.
- **Full content-management audit:** every 90 days, with the operator or named reviewer profile confirming schema discipline, tag namespacing, wikilink health, atomicity, and lifecycle movement.

Off-cycle audits fire when a drift signal below trips before the next scheduled review. High-risk surfaces (e.g. a regulated vault where stale notes carry compliance exposure) may use a shorter cadence; record the override and the reason.

### 2. Quality threshold

A healthy content-management discipline passes the 90-day check when **all** of the following hold:

- **Every note has a stage.** The note's frontmatter (or its inferred state from location and `status`) maps to one of the 6 lifecycle stages: `Capture`, `Triage`, `Process`, `Mature`, `Stale`, `Archive`. A note with no identifiable stage is "stuck" — it has left the inbox without entering the next stage.
- **The agent's triage cycle is running daily.** The triage cron has fired at least once in the last 24 hours; the run log shows notes were moved (not just listed). A cron that is configured but has not fired in 7 days is a maintenance failure.
- **Stale notes are identified and proposed for archive.** Each 90-day audit produces a list of notes whose `Mature` age exceeds the discipline's `Stale` threshold (a methodology default; record overrides). The list is operator-actionable — the agent cannot unilaterally archive.
- **Schema discipline holds.** Frontmatter has all required fields, tags are namespaced, wikilinks resolve, atomic notes are under the size budget (default 2000 words). The 5 failure modes in Part 1 are not regressing.
- **The 5 conditions are explicit and binary.** Each condition is `pass` or `revise`; partial credit hides drift. A maintenance pass that ends in "looks fine" without per-condition verdicts is a maintenance pass that did not fix anything.

The thresholds are review gates, not formatting games. Collapsing two checks to satisfy a count, or adding a sixth to look rigorous, both fail the underlying test.

### 3. Drift signals

Drift is observable. Surface at least one of the following before the next 90-day audit:

- **A note stuck in `Triage` for 30+ days.** The note was captured, sat in the inbox-equivalent, and was never routed. The 30-day window per Part 7's lifecycle discipline is the cap; longer means triage is failing or the routing rule is wrong.
- **A note stuck in `Process` for 60+ days.** The note was routed but never reached `Mature` (full frontmatter, tags, wikilinks). Processing is bounded (Part 7: <30 min per note); a multi-month `Process` is either a write that grew past atomicity or a writer who stopped mid-edit.
- **No audit of the `Stale` stage in 90+ days.** The stage exists in the lifecycle but no one has looked at it. Stale notes pile up; the discipline collapses into "everything lives in `Mature`."
- **Triage cron has not fired in 7+ days.** The schedule exists; the runs do not. The cadence is broken at the execution layer, not the policy layer.
- **A new frontmatter field emerged in practice that the schema does not name.** Operators are writing fields ad-hoc. The schema's check list has grown stale; the practice has moved on.
- **Operators are skipping the audit because the cadence is wrong.** A 90-day cadence for a discipline that drifts weekly is itself drift.
- **A signal observed in adjacent methodology files (tier routing, skill maintenance) is not surfaced here.** Maintenance sections that ignore drift their siblings catch are incomplete.

A drift signal does not always mean the rule is wrong. Sometimes the rule is right and the writer is wrong; sometimes the tool cannot enforce it; occasionally the rule is obsolete and the signal is the prompt to revise. The audit names the signal and proposes a disposition; the owner confirms.

### 4. Fix actions

When drift is detected, the canonical response is:

1. **Run the triage cron manually** when the daily cadence has slipped. The cron is the first line of defense; restoring it surfaces what the routine pass should have caught. Confirm via the cron run log that notes moved.
2. **File a `stale-note-cleanup` ticket** on the operator's kanban board (default board: `hermes`) when notes are stuck. Name the drift signal, the stage(s) involved, the offending notes (if known), and the suspected cause.
3. **The vault curator reviews** the audit record, the schema discipline, and the corrective actions from prior audits. The curator does not unilaterally rewrite the methodology; the curator proposes.
4. **The curator re-issues the conditions or the corrective actions.** Two outcomes are valid: (a) the rule was right and the writer was wrong — fix the writer and re-apply the rule; (b) the rule is obsolete — open a methodology-revision ticket, then re-apply after the new rule lands.
5. **The disposition is recorded** on the ticket: cause, corrective action, next review date. A drift signal with no recorded disposition is unresolved.

Do not auto-fix drift by editing notes in place to make the audit green. A note stuck in `Triage` is a routing failure; the fix is to route it (or to revise the routing rule), not to mark it `Mature` to clear the alert.

### 5. Retirement conditions

Retire the content-management discipline (or this methodology) when **at least one** of the following is observable for 90 consecutive days:

- **A stage has zero notes for 90 consecutive days.** The stage exists in the lifecycle but no note has moved through it in a quarter. Either the stage is over-deployed (collapse it into an adjacent stage) or the routing rule never fires (the discipline is incomplete).
- **A stage is consistently skipped.** Operators file notes that match the lifecycle's scope but never invoke this stage; the stage has become documentation theater.
- **A simpler discipline has replaced it.** A different methodology in `methodology/` (e.g. a tier-only or skill-only discipline) covers the same ground with lower overhead; the older section is dead weight.
- **The owner explicitly retires it.** Methodology retirement is an operator call, not a self-acting rule; the section stays on disk as historical reference after retirement.

The retirement sequence:

1. Propose merging the discipline with an adjacent methodology that covers the same ground. Name the surviving methodology.
2. Move all in-flight instances (open tickets, audit records, schema definitions) to the surviving methodology, preserving references and adding a `migrated_from:` field so the move is auditable.
3. Update every MOC, catalog entry, agent assignment, and cross-reference that pointed at the retired section.
4. Keep the section present as an empty placeholder for one full audit cycle (90 days) so any late-arriving references surface. Remove the placeholder only after the cycle completes with no inbound references.
5. Record the retirement in the methodology changelog: date, surviving methodology, contents moved, references updated, owner.

### Maintenance parity check

This section defines a friend-portable method, not a claim that every platform supplies automated triage crons, schema validators, wikilink audits, or lifecycle-stage detectors. Before adopting it, map each function — daily triage scan, schema validation, drift surfacing, corrective routing, retirement archival — to mechanisms available in your own tool. The 90-day cadence and the 30/60-day stage thresholds are methodology defaults; adjust them when measured drift rate, note volume, or operator-stated risk provides better evidence, but record the exception so the audit trail remains intact.

> **Method-not-instance reminder.** Alice documents the method, not the specific tool or operator. Replace platform-specific commands and paths with your own tool's equivalents; do not paste instance-specific paths, commands, or platform names into the section body.
- `templates/frontmatter-schema.md.template` — the fillable form for the frontmatter schema