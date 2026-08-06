# Methodology 01 — Decide your vault tier

> The first decision Alice asks you to make: where do your notes live, and what are the access rules? This is the substrate that everything else depends on.

## What a vault tier is

A **tier** is a directory (or data store) with one access rule. Alice uses a 4-tier model, but you can adapt the count and the rules to your context. The point is **separation of concerns**:

- Some notes you edit freely
- Some notes you never edit
- Some notes you never want to look at again
- Some notes are private to you

If you put all of these in one folder, you can't move fast on the editable notes and you can't find the archive (it's mixed in with the active). Tier routing solves both.

## The 4-tier model (Alice's default)

| Tier | Purpose | Access rule | Examples |
|---|---|---|---|
| **0 — Inbox** | Items that need routing | Append-only. Never edit. | New X posts, articles to triage, scratch notes from chat |
| **1 — Literature** | External sources you reference | Append-only after first write | X post that informed a decision, paper that informed a spec |
| **2 — Atomic** | Your own ideas, in their final form | Edit freely | Specs, designs, rules, methods, decisions |
| **3 — Indexes** | Maps of the vault | Regenerate | Map of Contents (MOC), FTS5 (full-text search index, e.g. SQLite FTS5), manifest.db |
| **4 — Archive** | Things you might want later but don't want to see | Move, never delete | Closed tickets, archived rules, deprecated specs |

**The 3 principles:**

1. **Markdown files are the source of truth.** Every `.md` in your vault is canonical. SQLite, FTS5, and any other derived state **mirror**, never replace.
2. **No side-channel writes.** Never write to a vault other than the one designated for your tool. Never edit the inbox or archive in place — move, then edit at the destination.
3. **One idea per atomic note.** The "permanent" tier (atomic) collapses into a junk drawer without atomicity. Split before write.

## How to adapt this to your context

The 4-tier model is a starting point. You may have:

- **3 tiers** if you don't have a separate "literature" bucket (literature goes into atomic)
- **5 tiers** if you want to separate "scratch" (chat-lane ephemera) from "inbox" (routing)
- **Different rules** for each tier based on your tool's permissions

The decision isn't "how many tiers." The decision is: **what are the access rules that will keep me honest in 6 months?** If the answer is "edit-freely-for-some, append-only-for-others," you have tiers. If the answer is "edit everything freely," you don't.

## The 5 questions to answer before designing tiers

1. **Where do my ideas live when they're not yet ready?** (Inbox-equivalent: scratch space, captures, etc.)
2. **Where do my external inputs go when I want to reference them?** (Literature: papers, posts, articles)
3. **Where do my own ideas live when they're ready?** (Atomic: specs, rules, decisions)
4. **How do I find things across the tiers?** (Indexes: MOCs, FTS5, manifest)
5. **Where do closed / archived things go?** (Archive: deprecated, completed, never-touched-again)

If you can answer all 5, you have a 4-5 tier system. If any answer is "I don't have one" or "everything is in one folder," that's a gap.

## What to put in each tier (decision rules)

- **Inbox:** anything that needs routing. Decision: "what tier does this belong in?" → if "atomic," promote; if "literature," promote; if "archive," promote; if "delete," delete.
- **Literature:** external sources that informed a decision. Decision: "is this still relevant?" → if yes, keep; if no, archive.
- **Atomic:** your own ideas in their final form. Decision: "is this 1 idea per file?" → if yes, keep; if no, split.
- **Index:** the map. Regenerate periodically (cron, or manual).
- **Archive:** closed, deprecated, never-touched-again. Decision: "will I look at this in 6 months?" → if no, archive; if yes, leave in current tier.

## What NOT to do

- **Don't tier by tool.** "Notes in Obsidian" + "Notes in Notion" + "Notes in plain text" is tool-routing, not idea-routing. Tier by access rule, not by tool.
- **Don't tier by domain.** "Work notes" + "Personal notes" is domain-routing. Tier by access rule, not by domain.
- **Don't tier by audience.** "Public docs" + "Private docs" is audience-routing. Tier by access rule.

If you find yourself wanting these, you probably want a **sub-tier** within a tier, or a separate **index** per domain. Talk to a friend, but the 4-tier model is the simple one.

## What you need to do in your tool

1. **Create the directory structure** for your tiers (4 or 5 directories in your tool's vault location)
2. **Define the access rules** for each tier (append-only, edit-freely, regenerate, etc.) — this is documentation, not code
3. **Pick a naming convention** for the 4-tier routing (e.g., `0-inbox/`, `1-literature/`, `2-atomic/`, `3-indexes/`, `4-archive/`)
4. **Document it in your AGENTS.md** so your agent knows the rules on cold-start

See `templates/AGENTS.md.template` for a fillable cold-start schema.

## Worked example

See `worked-examples/01-solo-founder-skeleton/methodology-notes/01-vault-tier.md` (when v0.0 ships) for an anonymized example.

## See also

- `methodology/01b-decide-vault-content.md` — the tier doc tells you WHERE notes live; the content doc tells you HOW to write them. Frontmatter schema, tag namespacing, atomicity rules, note lifecycle.
- `methodology/03a-decide-agent-vault-interaction.md` — the tier doc covers WHERE; this doc lists "different rules per tier based on your tool's permissions." The interaction doc covers the 5 patterns (read-only, propose, edit, scaffold, archive) and the per-tier permissions.

Once you have your vault tier design:
- `methodology/02-decide-skills.md` — design the skills the agent will use to write to / read from these tiers
- `methodology/03-decide-agents.md` — design the agents that own each tier

The vault tier is the **substrate**. Without it, the agent doesn't know where to write. With it, every methodology decision downstream has a home.

## Anti-patterns to watch for

1. **"I'll just keep everything in one folder for now."** → You will lose the access-rule separation within 3 months. The whole point of tiers is the access rule.
2. **"My tool already has folders I can use."** → Folder structure without explicit access rules is not tier routing. Document the rules.
3. **"I'll just use the tool's defaults."** → Tools default to "all editable" or "all read-only." Tier routing needs both, not one or the other.
4. **"I have 4 tiers but only use 1."** → Collapse to 1-2 tiers until you actually need more. Don't maintain empty tiers.
