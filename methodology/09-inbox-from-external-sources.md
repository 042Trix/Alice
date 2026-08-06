# Methodology 09 — Inbox from external sources (X, articles, podcasts)

> The ninth decision: how to handle inbox items from external sources — X posts, articles, podcasts, books, conversations. This is a specialization of `methodology/08-inbox-route.md`.

## Why external sources are special

External sources have 3 properties that make them harder to route than internal items:

1. **Volume** — X (Twitter) produces thousands of posts per day; RSS (Really Simple Syndication) feeds produce hundreds; podcasts produce hours of audio
2. **Signal-to-noise** — most external content is low-signal; you need a filter
3. **Half-life** — X posts decay in hours; articles in weeks; books in months; some knowledge in years

You can't route 1000 X posts a day by hand. You need an **ingestion pipeline** that filters, summarizes, and routes.

## The 4-stage X-ingestion pipeline

```
1. Capture  →  2. Filter  →  3. Summarize  →  4. Route
```

**1. Capture.** Get the content into a file in your inbox tier. X-posts become one-file-per-post (X is the canonical example; same pattern for articles / podcasts / books).

**2. Filter.** Decide: is this relevant to my context? Use the **5-question inbox-routing** from `methodology/08-inbox-route.md`. If the answer to Q1 is "no," archive immediately. If the answer to Q1 is "yes" but uncertain, defer.

**3. Summarize.** For items that pass the filter, summarize to 1-3 sentences. The summary is what gets routed. The full content stays in the inbox tier (or gets archived if not worth keeping).

**4. Route.** Apply the 5-question routing decision tree. The summary goes to the target tier (atomic / literature / skill-brief / strike-rule / etc.); the full content goes to the appropriate sub-tier (or archive).

## X post capture (the most common external source)

X posts are the highest-volume external source. The capture discipline:

- **One post per file** — even if the post is part of a thread, each post is its own file
- **Source URL** — every post file has the source URL in frontmatter
- **Date captured** — every post file has the capture date
- **Author** — every post file has the author handle
- **Tags** — minimum 1 tag, max 3 tags (3 to avoid tag proliferation)

Frontmatter template:

```yaml
---
id: <7-char hash>
created: <ISO8601-UTC>
source: x
url: https://x.com/<author>/status/<id>
author: <handle>
tags: [domain:<area>, kind:<category>]
links: []
---
```

## The "5-question filter" applied to X

For each X post, run the 5 questions:

1. **Actionable now?** — Most X posts are not. They're informational or commentary. The actionable ones are usually "X person said Y about Z, and I'm doing Z."
2. **Agent or me?** — Most X posts inform a future decision. That's operator-action, not agent-action.
3. **One-off or pattern?** — A single X post is a one-off. A pattern of X posts (3+ on the same topic) is a signal.
4. **Pattern documented or novel?** — If you're already observing a pattern, the X post either confirms or contradicts. If it's a new pattern, file a ticket to investigate.
5. **Existing rule / skill / cron cover it?** — If yes, the X post is a sanity check, not a new artifact. If no, file a ticket.

If the answer to all 5 is "no" or "uncertain," archive.

## X-post "adds" — when the X post brings a new pattern

If the X post introduces a **new pattern** (a new skill, a new strike rule, a new methodology, a new tool), the post itself goes to:
- **atomic tier** if it's a methodology / rule / decision
- **literature tier** if it's a reference (e.g., a paper cited in a tweet)
- **inbox → archived** if the post is consumed (no need to keep the full tweet)

The summary lives in the tier that uses it. The full post can be archived (with the URL preserved in frontmatter).

## Articles (RSS / blog posts)

Articles follow the same 4-stage pipeline but with different conventions:

- **Capture:** the article URL + a markdown copy of the content
- **Filter:** the article is more substantive than a tweet, but the same 5 questions apply
- **Summarize:** 1-2 paragraphs (more than a tweet)
- **Route:** same as X post

Articles are more likely to be **literature** tier (you're not making a decision from one article, but you're citing it as a source). Articles are less likely to be **atomic** (you don't make atomic decisions from articles; you make them from synthesis).

## Podcasts (audio)

Podcasts add a transcription step. The pipeline:

- **Capture:** the episode URL + audio file (or transcript if available)
- **Transcribe:** use a transcription tool (Whisper, Otter, etc.) to get text
- **Filter:** the 5 questions on the transcript (not the audio)
- **Summarize:** 1-page summary of key points + actionable items
- **Route:** same as article

Podcasts are high-cost (transcription time + listening time). Only process podcasts you've already listened to. Don't subscribe and let the inbox pile up.

## Books

Books are highest-effort. The pipeline:

- **Capture:** book title + author + your reading notes
- **Filter:** the 5 questions on the book's main thesis (not the whole book)
- **Summarize:** 2-3 pages of key passages + actionable items
- **Route:** literature tier (you reference it) or atomic (if a passage becomes a method)

Books are usually processed **after** reading, not as inbox items. Reading a book takes weeks; you don't route as you read.

## Conversations (chat / voice memos / calls)

Conversations are time-sensitive but unstructured. The pipeline:

- **Capture:** the transcript (auto-captured in chat, manual in voice / call)
- **Filter:** the 5 questions on the conversation's decision points (not the chit-chat)
- **Summarize:** the decisions made + action items
- **Route:** action items become kanban tickets; decisions become atomic notes

Conversations are usually **operator-action** because they involve your judgment.

## Worked example (skeleton)

See `worked-examples/01-solo-founder-skeleton/methodology-notes/09-external-inbox.md` when v0.0 ships for an anonymized example.

## See also

- `methodology/01b-decide-vault-content.md` — **X post → literature is a specific lifecycle path.** The capture → filter → summarize → route stages are documented in the vault content doc, with X posts as the special case.

- `references/x-ingestion-pipeline.md` — the operating spec for X post ingestion
- `templates/inbox-route.md.template` — the fillable form
- `methodology/06-iteration-loop.md` — how a cron can implement the filter step automatically

## Anti-patterns to watch for

1. **"I subscribe to 50 X accounts and read all of them daily."** → No. Subscribe to 5-10, read 1-2 daily. Unfollow the rest.
2. **"I'll save X posts for later."** → No. Save means triage now. "Later" doesn't exist.
3. **"The X post is from a smart person, so it's important."** → No. Smart ≠ relevant. Filter by your context, not by author.
4. **"I'll process the X post and add the idea to my system."** → No. Process first; if the post has a use case, the use case goes to atomic. The post goes to literature (or archive).
5. **"I'll listen to the podcast on 2x speed and process it."** → No. Listen at 1x, take notes, process after. The point of podcasts is depth, not throughput.
