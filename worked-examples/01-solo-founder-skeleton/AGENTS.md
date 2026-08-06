---
id: alice-example-solo-founder-AGENTS
created: 2026-08-04T12:30:00Z
title: "AGENTS.md — solo founder archetype (anonymized example)"
type: worked-example
status: draft
source: alice-framework
tags: [kind:worked-example, kind:agents-md, project:alice, archetype:solo-founder]
confidence: 0.0
links: []
---

# AGENTS.md — solo founder archetype

> **Use this worked example to see how a solo founder can configure Alice for a small project portfolio and one income-replacement target.** It is not a snapshot: all names, projects, and operator details are anonymized, and the destinations are illustrative rather than prescriptive.

---

# Solo Founder Operating System

> A small agent fleet for a solo founder running 2-3 active projects and 1 income-replacement target.

This file is the **stable operating manual** a cold-start agent reads on launch when the working directory is the vault root. It is **navigational on purpose**: each section points to the authoritative skill or file, rather than duplicating its procedures.

---

## 1. Source of truth

- **Markdown files are the source of truth.** Every `.md` in the vault is canonical. `_index/manifest.db` (a derived metadata index over your notes), FTS5 (full-text search index, e.g. SQLite FTS5), and any other derived state **mirror**, never replace.
- **No side-channel writes.** Never write to a vault other than the designated one. Never edit `0-inbox/` or `4-archive/` in place — move, then edit at the destination.
- **One idea per atomic note.** The "permanent" tier (`2-atomic/`) collapses into a junk drawer without atomicity. Split before write.

## 2. Routing tiers

| Tier | Folder | Edit policy |
|---|---|---|
| 0 — Inbox | `0-inbox/`, `0-curriculum/` | Append-only. Never edit. |
| 1 — Literature | `1-literature/` | Append-only after first write. |
| 2 — Atomic | `2-atomic/` | Edit freely. |
| 3 — Indexes | `3-indexes/`, `3-derived/`, `3-sources/` | Regenerate. |
| 4 — Archive | `4-archive/` | Move, never delete. |

`_scratch/`, `_templates/`, `_index/`, `.obsidian/` are helpers, not in the four-tier pipeline.

## 3. Required frontmatter and namespaced tags

```yaml
---
id: <7-char hash>
created: <ISO8601-UTC>
updated: <ISO8601-UTC>
type: atomic | literature | inbox | moc | decision | archive
status: draft | active | stale | archived
source: <who/where>
tags: [domain:<area>, project:<slug>, kind:<category>]
confidence: 0.0-1.0
links: [[related-note-id]]
---
```

**Tags are namespaced.** `domain:`, `project:`, `kind:`, `source:`, `private:`.

Templates live in `_templates/`. Schema is in the `note-taking` skill (see `references/`).

## 4. Retrieval

```bash
python3 _index/reindex.py
python3 _index/fts5_query.py "<query>" --limit 5
sqlite3 _index/manifest.db "SELECT path, title FROM notes WHERE tags LIKE '%project:<slug>%' ORDER BY updated DESC LIMIT 10"
```

Edge cases handled by the indexer (don't re-derive): id-vs-path collision, wikilink forward-reference race, FTS5 hyphen-tokenization, reindex upsert-by-path, external-content table gotcha. See `references/` for the full list.

## 5. End-to-end verification

After any build:

1. `python3 _index/reindex.py` → `0 failed`
2. `sqlite3 _index/manifest.db "SELECT type, COUNT(*) FROM notes GROUP BY type"` → rows for every type
3. `python3 _index/fts5_query.py "<known-term>" --limit 5` → at least one highlighted snippet
4. Wikilink graph query → at least one row if the build referenced other notes

## 6. Audit + log discipline

- **Append-only.** Never edit prior `log.md` entries. Schema: `## [ISO8601-UTC] event-kind — short summary` + 3-5 body lines max.
- **Where to log:** vault-resident events → `log.md`; per-task audit → workspace's local `audit/` folder.

## 7. Kanban lifecycle

Vault work flows through the kanban board:

- **Pick the board by work-product domain.** Project-specific work (Alice repo work, OSS prep, polish, glossary) → your project-named board (e.g., the board named after your repo). Cross-cutting one-offs → `default`. Tool-infrastructure work (skills, crons, profiles, vault infrastructure) → your tool's infrastructure board (the board your tool reserves for internal infra work).
- **Use the dispatcher's default profile as the assignee for new chat-session work.** The dispatcher spawns the default profile; it does not spawn chat-lane identities.
- **Every `ready` task must move forward immediately** — dispatch or block. No stranded `ready` rows past 30 minutes.
- **Substantive completion record on done.** Not "done." Capture files created, decisions made, evidence cited, next actions — either in a structured field on the task record or in the summary text.
- **Revenue-critical tasks must ship a real artifact.** A completion without a real handle (file path, ticket id, URL) is a hard-fail on the completion gate; the agent must re-dispatch with the handle included.
- **Heartbeat on long ops.** >1h task → a heartbeat at least once an hour. Stale claims are reclaimed.

## 8. Operator priority

The operator (you) is the bottleneck. **Every workflow should ask: "does this need operator judgment, or can the agent do it?"** Most things can be agent-handled. The exceptions are: irreversible decisions, customer-facing communication, and policy changes.

When a decision is irreversible or policy-changing, **the workflow should pause and wait for operator input.** A workflow that auto-commits irreversible changes is a workflow that will eventually do the wrong thing.

---

## How this example applies the methodology

This AGENTS.md is the **output** of methodology `01-decide-vault-tier.md`. The actual tier decisions:

- **0-inbox:** chosen because the operator has 1-2 daily inbox items (X posts, screenshots, articles). A smaller operator might use `0-scratch` instead.
- **1-literature:** chosen because the operator cites X posts and articles in their decisions.
- **2-atomic:** chosen because the operator writes specs, rules, decisions, and council verdicts.
- **3-indexes:** chosen because the operator uses MOCs and FTS5 to navigate.
- **4-archive:** chosen because the operator wants closed tickets and deprecated rules to be retrievable.

The methodology doesn't dictate these choices. The methodology says "design your tiers to match your operator's flow." This AGENTS.md shows one such design.

## What's specific to this archetype

- **1 operator** + 1 default profile + 2-3 named profiles (coder, verifier, planner)
- **5-10 crons**, mostly `*/5` heartbeats + daily triage + weekly scout
- **3-5 active projects** at any time, with 1 being the income-replacement target
- **No external-facing communication** except in narrow workflows
- **Daily triage** + **weekly review** + **monthly archive**

## What's NOT in this example

- Multi-operator scenarios (use a different archetype)
- Real-time workflows (X feed monitoring, customer chat) — see research-analyst archetype
- Public-facing content (newsletter, blog) — see research-analyst archetype
- Domain-specific boards (the example uses generic boards; in practice, the operator would have 1-2 domain boards)
