# Methodology 08 — Inbox routing

> The eighth decision: how to route items from your external inbox. Inbox routing is the **input filter** — it decides what enters the system and where.

## What inbox routing is (in Alice's terms)

**Inbox routing** is the process of taking items from your external inbox (X posts, articles, conversations, screenshots, voice memos) and deciding:
- Does this become a vault note? (Promote to atomic or literature)
- Does this become a skill? (Build a procedure)
- Does this become an operational guard? (Observe 3 of the same shape)
- Does this become a cron? (Detect + surface + act on a pattern)
- Does this become a council question? (Multi-perspective review)
- Does this become nothing? (Delete or archive)

The inbox tier is **append-only**. You never edit inbox items; you route them.

## The 5 routing decisions

For each inbox item, ask 5 questions in order:

```
1. Is this actionable now?
   ↓
2. If yes, can the agent do it, or does it need me?
   ↓
3. If the agent, is this a one-off or a recurring pattern?
   ↓
4. If a pattern, is the pattern documented or novel?
   ↓
5. If documented, does the existing rule / skill / cron cover it?
```

**Decision tree:**

- **Not actionable** (informational, curiosity, decoration) → Archive or delete.
- **Actionable, needs me** (decision, judgment, approval) → File a kanban ticket with `assignee=operator`.
- **Actionable, agent can do, one-off** → Just do it inline.
- **Actionable, agent can do, recurring pattern, novel** → File a ticket to build a skill.
- **Actionable, agent can do, recurring pattern, documented** → File a ticket to verify the existing skill / rule / cron covers it.

If you can't answer the 5 questions, **the item is in limbo.** Don't force a routing decision. Leave it in the inbox and come back later.

## Inbox tier discipline

The inbox is **append-only**. Three rules:

1. **Never edit an inbox item.** You can route it, archive it, or delete it. You don't edit the content.
2. **Never leave an inbox item in place for more than 30 days.** Route, archive, or delete.
3. **One item per file.** Each inbox item is its own file. Don't combine items.

If you have 100+ items in the inbox, you're not routing. Time to either:
- Triage aggressively (archive anything older than 30 days)
- Reduce your inbox sources (stop subscribing to feeds that produce noise)

## Inbox-route template

Use `templates/inbox-route.md.template`. The route has 4 sections:

1. **Source** — where did the item come from? (X, RSS, conversation, etc.)
2. **Type** — what kind of item is it? (article, post, screenshot, idea, decision)
3. **Action** — what routing decision did you make? (Promote, archive, skill-ticket, op-guard-ticket, council-ticket, etc.)
4. **Notes** — why this routing? (1 sentence)

If any section is empty, the route isn't ready.

## Common inbox sources

Different sources have different routing patterns:

- **X posts** — see `methodology/09-inbox-from-external-sources.md`
- **Articles (RSS)** — usually literature tier; rarely skill-brief
- **Conversations (chat, voice memos)** — usually operator-action ticket
- **Screenshots** — usually op-guard or skill-brief input (visual patterns)
- **Books** — usually literature; very rarely atomic (if a passage becomes a method)
- **Emails** — usually operator-action or archive; rarely anything else
- **Voice memos** — transcribed first, then routed like any other text item

## The "5 minutes" rule

**Spend no more than 5 minutes routing any single inbox item.** If you can't decide in 5 minutes, the item is in limbo. Mark it "deferred" and come back later.

The 5-minute rule prevents:
- Over-routing (decisions that don't matter get hours of analysis)
- Under-routing (paralysis on items you should just archive)

If you're spending hours routing a single item, you have a different problem (probably: you don't have a clear vault, or the item is too vague to route).

## Inbox routing cadence

- **Daily:** Triage new items (5-10 items, 30-60 min)
- **Weekly:** Deep triage (revisit deferred items, archive old ones)
- **Monthly:** Audit the inbox tier for items older than 30 days

If you can't do daily triage, set up a cron that sends a reminder. If you can't do weekly triage, set up a cron that lists deferred items.

## What NOT to do

- **Don't read every item thoroughly.** Skim. Decide based on title, source, and first sentence.
- **Don't save items to atomic without a clear use case.** "I might need this someday" is not a use case.
- **Don't keep items in the inbox because "I haven't read them yet."** If you haven't read them in 30 days, archive.
- **Don't combine multiple items into one file.** Each item is its own file.

## Worked example (skeleton)

See `worked-examples/01-solo-founder-skeleton/methodology-notes/08-inbox-routing.md` when v0.0 ships for an anonymized example.

## See also

- `methodology/01b-decide-vault-content.md` — **inbox → atomic transition is the most common lifecycle path.** The capture → triage → process → mature → stale → archive stages are documented in the vault content doc.
- `methodology/04a-decide-work-graph.md` — the work graph is the substrate. **Inbox items that depend on existing tickets** become child tickets (`parent_id` set) or new relationships (`blocks` / `relates_to`). Inbox items that are independent of existing work become top-level tickets. The 5-question filter is the same, but the destination tier depends on the relationship:
  - **Child ticket:** the work is a sub-task of an existing project
  - **Top-level ticket:** the work is a new project (or a new root of a new sub-graph)
  - **No new ticket:** the inbox item is informational; archive to literature or atomic
- `methodology/04b-decide-board-routing.md` — every ticket file is a routing decision. Inbox items that become tickets are routed to the correct board via the keyword-routing table. **Alice documents the method, not specific instances.**

## What's next

- `methodology/09-inbox-from-external-sources.md` — X posts, articles, podcasts as a special case
- `references/x-ingestion-pipeline.md` — the operating spec for X post ingestion
- `templates/inbox-route.md.template` — the fillable form

## Anti-patterns to watch for

1. **"I have 200 items in my inbox and they're all 'important.'"** → No. 200 items means you're not routing. Triage or unsubscribe.
2. **"I should read every X post in full before deciding."** → No. Skim, decide, route. Don't read the full post unless you're promoting to atomic.
3. **"I'll save the article for later."** → No. Save means triage now. "Later" doesn't exist.
4. **"The inbox is for ideas."** → No. The inbox is for routing. Ideas go to atomic after triage.
