---
id: alice-example-research-analyst-AGENTS
created: 2026-08-04T12:30:00Z
title: "AGENTS.md — research analyst archetype (anonymized example)"
type: worked-example
status: draft
source: alice-framework
tags: [kind:worked-example, kind:agents-md, project:alice, archetype:research-analyst]
confidence: 0.0
links: []
---

# AGENTS.md — research analyst archetype

> **Use this worked example to see how a research analyst can configure Alice for research, content production, and income-replacement work.** It is not a snapshot: all names, projects, and operator details are anonymized, and the destinations are illustrative rather than prescriptive.

---

# Research Analyst Operating System

> A small agent fleet for a writer-practitioner or analyst running research projects, content production, and 1-2 income-replacement workstreams.

This file is the **stable operating manual** a cold-start agent reads on launch when the working directory is the vault root. It is **navigational on purpose**: each section points to the authoritative skill or file, rather than duplicating its procedures.

---

## 1. Source of truth

- **Markdown files are the source of truth.** Every `.md` in the vault is canonical. Derived state — manifest.db (a derived metadata index over your notes), FTS5 (full-text search index, e.g. SQLite FTS5), the citations index — **mirrors**, never replaces.
- **No side-channel writes.** Never write to a vault other than the designated one. Never edit `0-inbox/` or `4-archive/` in place — move, then edit at the destination.
- **One idea per atomic note.** Collapses into junk drawer without atomicity. Split before write.

## 2. Routing tiers

| Tier | Folder | Edit policy |
|---|---|---|
| 0 — Inbox | `0-inbox/`, `0-capture/` | Append-only. Never edit. |
| 1 — Literature | `1-literature/` | Append-only after first write. |
| 2 — Atomic | `2-atomic/` | Edit freely. |
| 3 — Indexes | `3-indexes/`, `3-citations/`, `3-embeddings/` | Regenerate. |
| 4 — Archive | `4-archive/` | Move, never delete. |

`_scratch/`, `_templates/`, `_index/` are helpers, not in the four-tier pipeline.

**Note:** the research-analyst archetype has a `0-capture/` sub-folder (raw captures before triage) and `3-citations/` (citation index) that the solo-founder archetype doesn't. **The methodology says "design your tiers to match your operator's flow."** This is one such design.

## 3. Required frontmatter and namespaced tags

```yaml
---
id: <7-char hash>
created: <ISO8601-UTC>
updated: <ISO8601-UTC>
type: atomic | literature | inbox | moc | decision | archive | citation
status: draft | active | stale | archived
source: <who/where>
tags: [domain:<area>, project:<slug>, kind:<category>, citation:<source>]
confidence: 0.0-1.0
links: [[related-note-id]]
---
```

**Tags are namespaced.** `domain:`, `project:`, `kind:`, `source:`, `citation:`. The `citation:` tag is specific to research-analyst and is used for literature-tier citation tracking.

## 4. Retrieval

```bash
python3 _index/reindex.py
python3 _index/fts5_query.py "<query>" --limit 5
sqlite3 _index/manifest.db "SELECT path, title FROM notes WHERE tags LIKE '%project:<slug>%' ORDER BY updated DESC LIMIT 10"
```

Specialized retrievals:

```bash
# Citation lookup (specific to research-analyst)
sqlite3 _index/manifest.db "SELECT path, title FROM notes WHERE tags LIKE '%citation:%' ORDER BY updated DESC LIMIT 20"

# Topic clusters (for writing)
sqlite3 _index/manifest.db "SELECT tags, COUNT(*) FROM notes GROUP BY tags HAVING tags LIKE '%topic:%'"
```

## 5. End-to-end verification

After any build:

1. `python3 _index/reindex.py` → `0 failed`
2. `sqlite3 _index/manifest.db "SELECT type, COUNT(*) FROM notes GROUP BY type"` → rows for every type
3. `python3 _index/fts5_query.py "<known-term>" --limit 5` → at least one highlighted snippet
4. Wikilink graph query → at least one row if the build referenced other notes
5. **Citation graph query** (research-analyst specific) → at least one row if the build referenced external sources

## 6. Audit + log discipline

- **Append-only.** Never edit prior `log.md` entries. Schema: `## [ISO8601-UTC] event-kind — short summary` + 3-5 body lines max.
- **Where to log:** vault-resident events → `log.md`; per-task audit → workspace's local `audit/` folder; citation-tracking audit → `_citations.log.md`.

## 7. Kanban lifecycle

Vault work flows through the kanban board:

- **Pick the board by work-product domain.** Project-specific work (Alice repo work, OSS prep, polish, glossary) → your project-named board (e.g., the board named after your repo). Cross-cutting one-offs → `default`. Tool-infrastructure work (skills, crons, profiles, vault infrastructure) → your tool's infrastructure board (the board your tool reserves for internal infra work).
- **Use the dispatcher's default profile as the assignee for new chat-session work.** The dispatcher spawns the default profile; it does not spawn chat-lane identities.
- **Every `ready` task must move forward immediately** — dispatch or block. No stranded `ready` rows past 30 minutes.
- **Substantive completion record on done.** Not "done." Capture files created, decisions made, evidence cited, next actions — either in a structured field on the task record or in the summary text.
- **Revenue-critical tasks must ship a real artifact.** A completion without a real handle (file path, ticket id, URL) is a hard-fail on the completion gate; the agent must re-dispatch with the handle included.
- **Heartbeat on long ops.** >1h task → a heartbeat at least once an hour. Stale claims are reclaimed.

## 8. Citation discipline

External sources are first-class citizens in the research-analyst archetype.

- **Every atomic note that cites an external source must have a `citation:` tag** with the source identifier.
- **Every literature-tier note has a `citation:` tag** matching the atomic notes that cite it.
- **The `3-citations/` index is regenerated when the citation graph changes.**
- **Citation chains** (atomic → literature → external) are queryable via the citation graph query in section 4.

## 9. Operator priority

The operator (you) is the bottleneck. **Every workflow should ask: "does this need operator judgment, or can the agent do it?"** Most things can be agent-handled. The exceptions are: editorial judgment (does this draft capture the right point?), customer-facing communication, and policy changes.

---

## How this example applies the methodology

This AGENTS.md is the **output** of methodology `01-decide-vault-tier.md`. The actual tier decisions:

- **0-inbox + 0-capture:** chosen because the research-analyst has 5-15 daily captures (articles, X posts, screenshots, voice memos). 0-inbox is for triage; 0-capture is for raw captures before triage.
- **1-literature:** chosen because the research-analyst cites dozens of external sources. Literature is the citation reservoir.
- **2-atomic:** chosen because the research-analyst writes notes, drafts, decisions, and council verdicts.
- **3-indexes + 3-citations + 3-embeddings:** chosen because the research-analyst needs MOCs for navigation, citations for source-tracking, embeddings for semantic search.
- **4-archive:** chosen because the research-analyst wants to keep references for years.

The methodology doesn't dictate these choices. The methodology says "design your tiers to match your operator's flow." This AGENTS.md shows one such design.

## What's specific to this archetype

- **1 operator** + 1 default profile + 3-4 named profiles (researcher, writer, verifier, scout)
- **More literature than solo-founder** — the ratio of literature:atomic is 3:1 (vs. 1:5 for solo-founder)
- **Citation discipline** is a first-class concern (not present in solo-founder)
- **Embeddings** for semantic search across literature (not present in solo-founder)
- **Public-facing content** is a workflow (newsletter, blog, podcast) — operator reviews but agent drafts

## What's NOT in this example

- Multi-operator collaboration
- Code-heavy workflows (the research-analyst writes prose, not code)
- Real-time workflows (research is async, not real-time)
- Domain-specific boards (the example uses generic boards; in practice, the operator would have 1-2 domain boards)
