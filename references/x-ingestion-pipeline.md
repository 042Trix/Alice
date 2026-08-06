---
id: alice-reference-x-ingestion
created: 2026-08-04T12:30:00Z
title: "X ingestion pipeline — operating spec"
type: reference
status: draft
source: alice-framework
tags: [kind:reference, kind:x-ingestion, project:alice]
confidence: 0.0
links: []
---

# X ingestion pipeline

> The operating spec for ingesting X (Twitter) posts (or any external source) into your vault. This is a specialization of `methodology/09-inbox-from-external-sources.md`.

## When to use this pipeline

Use this pipeline when you want to systematically ingest X posts. The pipeline is for **systematic ingestion, not ad-hoc capture.** If you have 1 X post to triage, use the inbox-route template directly.

## The 4-stage pipeline

```
1. Capture  →  2. Filter  →  3. Summarize  →  4. Route
```

The 4 stages are **automatic** for stages 1-2 (capture + filter) and **manual** for stages 3-4 (summarize + route). The reasoning:

- Capture and filter are mechanical — they don't need your judgment
- Summarize and route are judgment calls — they need your context

## Stage 1: Capture (automatic)

**Goal:** get the X post into a file in your inbox tier, with metadata.

**Input:** the X post URL or ID
**Output:** a markdown file in your inbox tier with frontmatter

Frontmatter template:

```yaml
---
id: <7-char hash of the post content>
created: <ISO8601-UTC when captured>
source: x
url: https://x.com/<author>/status/<id>
author: <handle>
tags: [domain:<area>, kind:<category>]
links: []
---
```

**Body:** the post content (verbatim if public, summarized if behind a paywall).

**Tools:** the capture can be done via:
- A browser-based capture tool (e.g., a "save to inbox" bookmarklet)
- A cron that monitors subscribed accounts
- A paste-into-file manual action

## Stage 2: Filter (automatic)

**Goal:** identify which posts are relevant to your context.

**Input:** the captured post + your vault content (atomic tier)
**Output:** a relevance score (0-1) + a routing suggestion

The filter is **mechanical** but the criteria are not. You define the criteria based on your context:

- **Keyword match:** the post contains keywords you've defined as relevant
- **Author match:** the post is from an author you've subscribed to
- **Topic match:** the post is on a topic you've flagged as relevant
- **Engagement match:** the post has high engagement (likes, retweets) — proxy for "important to many people"
- **Vault-link match:** the post links to or references content already in your vault

You can combine these criteria. A common setup:

```
score = 0.4 * keyword_match + 0.2 * author_match + 0.2 * topic_match + 0.1 * engagement_match + 0.1 * vault_link_match
```

Posts with score >= 0.6 are routed to stage 3. Posts with score < 0.6 are archived.

**Caveat:** the criteria are operator-specific; a solo founder's relevant posts are different from a researcher's. **Tune the criteria for your context.**

## Stage 3: Summarize (manual)

**Goal:** produce a 1-3 sentence summary of the post.

**Input:** the filtered post + your context
**Output:** a summary written to the post file (or a separate file)

The summary should:
- Capture the post's main point
- Include any actionable items
- Surface any links to other content
- Note any context the operator might miss

If you can't summarize in 3 sentences, the post is too complex for the inbox tier. Promote to atomic or literature instead.

## Stage 4: Route (manual)

**Goal:** decide what to do with the post.

**Input:** the summary + your context
**Output:** the post routed to a destination

Use the 5-question routing decision from `methodology/08-inbox-route.md`:

1. Actionable now?
2. Agent or me?
3. One-off or pattern?
4. Pattern documented or novel?
5. Existing rule / skill / cron cover it?

The destination is one of:
- **Promote to atomic** (one idea per file, in your own words)
- **Promote to literature** (external source you're referencing)
- **File as skill-brief ticket** (recurring pattern, build a procedure)
- **File as strike-rule ticket** (failure pattern, observe 3+ times)
- **File as cron-spec ticket** (detection + surface + act pattern)
- **File as council ticket** (multi-perspective review needed)
- **File as operator-action ticket** (decision needs operator judgment)
- **Archive** (informational, no use case)
- **Delete** (noise, no value)

## Pipeline cadence

- **Capture:** daily (cron or manual)
- **Filter:** daily (cron)
- **Summarize:** weekly (manual, batched)
- **Route:** weekly (manual, batched)

If you can't do daily capture, use a manual paste. If you can't do weekly summarize, you have too many posts; reduce the source subscriptions.

## What NOT to do

- **Don't capture every X post you see.** Capture only the posts you'd cite in 6 months.
- **Don't filter with a too-broad keyword list.** Tune the criteria for your context.
- **Don't summarize in real-time.** Batch the weekly summarize; the operator's context window is not a place for real-time X processing.
- **Don't route as you go.** Batch the weekly route. The 5-question routing decision is a 1-2 hour session, not a per-post decision.

## Worked example (skeleton)

See `worked-examples/01-solo-founder-skeleton/references/x-ingestion-example.md` when v0.0 ships for an anonymized example.

## See also

- `methodology/01b-decide-vault-content.md` — X-post ingestion is one specific lifecycle path. The general capture → filter → summarize → route pattern is documented in the vault content doc; X-post is a specialization.

## What's next

- `tier-routing-cheatsheet.md` — quick reference for 4-tier routing
- `kanban-lite-disciplines.md` — minimal kanban rules
- `tool-mapping-guide.md` — how the abstract concepts map to your tool
