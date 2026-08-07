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

## Maintenance

Designing the tier model completes the build phase; it does not end the lifecycle. A vault with the right tiers but no audit, drift detection, or retirement rule degrades into "everything lives in 2-ATOMIC" within months. The maintenance cycle below mirrors `methodology/02-decide-skills.md` §Skill maintenance in shape so a friend reading both sees the same rhythm.

### 1. Audit cadence

Use two cadences:

- **Routine inventory scan:** weekly or monthly, automated when the platform supports it.
- **Tier audit:** every 6 months, with a human or designated owner confirming tier definitions, routing rules, and access policy.

The routine scan collects; it does not decide. It may count notes per tier, flag access-rule violations, detect routing bypasses (a final-form note appearing directly in `2-ATOMIC/` with no triage record), and queue tiers due for review. The 6-month audit applies the five checks below and records a disposition per tier. High-risk tiers (e.g. an append-only atomic tier in a regulated context) may use a shorter cadence; record the override and the reason.

### 2. Quality threshold

A healthy vault tier model satisfies all of:

- **Tier count ≥ 4.** Four is the floor (inbox / literature / atomic / archive, with indexes optional). Three collapses literature into atomic and erodes the source-vs-derivative distinction. Two is "active" and "everything else." One is a folder, not a tier model.
- **Access rule is explicit per tier.** Each tier names whether it is append-only, edit-freely, regenerate-only, or move-only. A tier with no documented rule has no rule.
- **No tier is empty for 90+ consecutive days.** An empty tier is either over-deployed (collapse it) or under-routed (fix the routing).
- **Migration path between tiers is documented.** Every move a note can make (e.g. `0-INBOX/` → `2-ATOMIC/`, `2-ATOMIC/` → `4-ARCHIVE/`) has a named trigger and a destination rule.
- **No side-channel writes.** Every note lands in the tier designated for the tool writing it.

The thresholds are review gates, not formatting games. Collapsing two tiers to satisfy the floor, or adding a sixth to look rigorous, both fail the underlying test.

### 3. Drift signals

Drift is observable. Surface at least one of the following before the next 6-month audit:

- **Notes "falling through" tiers.** A note appears in `2-ATOMIC/` with no prior `0-INBOX/` or `1-LITERATURE/` presence and no migration record. The most common drift signal — the routing rule was skipped because the writer found the destination more convenient than the inbox.
- **Tier empty for 30+ days.** The directory still exists but receives no writes. Either routing broke or the tier is obsolete.
- **Agent bypasses tier routing.** A final-form note written directly to the destination tier without going through the inbox, or an atomic note moved to archive without a deprecation record.
- **Edit on an append-only tier.** A note in `0-INBOX/` or `1-LITERATURE/` was rewritten after first write. The access rule has failed.

A drift signal does not always mean the rule is wrong. Sometimes the rule is right and the writer is wrong; sometimes the tool cannot enforce it; occasionally the rule is obsolete and the signal is the prompt to revise. The audit names the signal and proposes a disposition; the owner confirms.

### 4. Fix actions

When drift is detected, the canonical response is:

1. **File a `vault-route-audit` ticket** on the operator's kanban board. Name the drift signal, the tier(s) involved, the offending notes (if known), and the suspected cause.
2. **The vault curator reviews** the tier definitions, the access rules, and the routing triggers. The curator does not unilaterally rewrite the methodology; the curator proposes.
3. **The curator re-issues the tier definitions or the routing rule.** Two outcomes are valid: (a) the rule was right and the writer was wrong — fix the writer and re-route the offending notes; (b) the rule is obsolete — open a methodology-revision ticket, then re-route after the new rule lands.
4. **The disposition is recorded** on the ticket: cause, corrective action, next review date. A drift signal with no recorded disposition is unresolved.

Do not auto-fix drift by rewriting notes in place. A note that landed in the wrong tier is a routing failure; the fix is to move it to the right tier with an audit line, not to edit it where it sits.

### 5. Retirement conditions

Retire a tier when at least one of the following is observable for 90 consecutive days:

- **Zero writes to the tier.** Nothing has been filed, captured, or moved into it for three months. The tier is over-deployed (routing never fires) or obsolete.
- **Zero reads from the tier.** No agent, MOC, or audit script has read it in three months. Dead memory.
- **Routing can be folded into an adjacent tier without rule conflict.** The retired tier's access rule is a strict subset of a remaining tier's rule, and contents can move as a single batch.

The retirement sequence:

1. Propose merging the tier with an adjacent tier that shares the access rule. Name the surviving tier.
2. Move all notes to the surviving tier, preserving frontmatter and adding a `migrated_from:` field so the move is auditable.
3. Update every MOC, catalog entry, routing rule, and agent assignment that referenced the retired tier.
4. Keep the directory present as an empty placeholder for one full audit cycle (6 months) so any late-arriving references surface. Remove the placeholder only after the cycle completes with no inbound references.
5. Record the retirement in the methodology changelog: date, surviving tier, contents moved, references updated, owner.

A vault model whittled down to a single tier has failed. The tier model exists to separate access rules; one tier means one access rule for everything, which means the design has collapsed. If you reach one tier, restart from the 5 questions above — do not keep a one-tier system.

### Maintenance parity check

This section defines a friend-portable method, not a claim that every platform supplies automated tier scans, append-only enforcement, or migration audit fields. Before adopting it, map each function—routine scan, append-only enforcement, migration audit, and retirement archival—to mechanisms available in your own tool. The 6-month cycle is a methodology default; adjust it when measured drift rate, note volume, or risk provides better evidence, but record the exception so the audit trail remains intact.

## Anti-patterns to watch for

1. **"I'll just keep everything in one folder for now."** → You will lose the access-rule separation within 3 months. The whole point of tiers is the access rule.
2. **"My tool already has folders I can use."** → Folder structure without explicit access rules is not tier routing. Document the rules.
3. **"I'll just use the tool's defaults."** → Tools default to "all editable" or "all read-only." Tier routing needs both, not one or the other.
4. **"I have 4 tiers but only use 1."** → Collapse to 1-2 tiers until you actually need more. Don't maintain empty tiers.
