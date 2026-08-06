---
id: alice-reference-tier-routing
created: 2026-08-04T12:30:00Z
title: "Tier routing cheatsheet"
type: reference
status: draft
source: alice-framework
tags: [kind:reference, kind:tier-routing, project:alice]
confidence: 0.0
links: []
---

# Tier routing cheatsheet

> Quick reference for deciding which tier a note belongs in. For the full methodology, see `methodology/01-decide-vault-tier.md` and `methodology/08-inbox-route.md`.

## The 5-question routing decision

For each item, ask:

```
1. Is this actionable now? (yes → 2; no → tier 0 inbox then defer)
2. If yes, can the agent do it, or does it need me? (agent → 3; me → tier 2 atomic with operator-action tag)
3. If the agent, is this a one-off or a recurring pattern? (one-off → 4; pattern → 5)
4. If one-off, is it small (skill does it) or big (worker does it)? (small → skill; big → worker ticket)
5. If pattern, is the pattern documented or novel? (documented → verify; novel → file as new artifact)
```

## The 4 tier-decision rules

| Rule | Tier | When to use |
|---|---|---|
| **Append-only** | 0 (inbox), 1 (literature) | Items you're triaging or referencing, not editing |
| **Edit freely** | 2 (atomic) | Your own ideas, in their final form |
| **Regenerate** | 3 (indexes) | Maps of the vault, computed from atomic |
| **Move, never delete** | 4 (archive) | Things you might want later but don't want to see |

## Common routing decisions

| Item | Tier | Reason |
|---|---|---|
| X post you're processing | 0 (inbox) | Triage, then route |
| X post that's a reference | 1 (literature) | External source, not your idea |
| Skill-brief draft | 2 (atomic) | Your own idea, in progress |
| Skill (shipped) | 2 (atomic) | Your own idea, final form |
| Strike rule | 2 (atomic, in `rules/`) | Constraint derived from failures |
| Council output | 2 (atomic, in your cross-agent verdicts directory — e.g., `cross-agent/`, `council/`, etc.) | Multi-perspective review |
| Operator-action ticket | 2 (atomic, in `kanban/`) | Decision the operator makes |
| FTS5 index | 3 (indexes) | Map of the vault, computed |
| Map of Contents (MOC) | 3 (indexes) | Map of the vault, computed |
| Closed ticket (after 30 days) | 4 (archive) | Might want it later, don't want to see |
| Deprecated skill | 4 (archive) | Might want it later, don't want to see |

## The "3-action rule" for inbox

Every inbox item has 3 possible actions:

1. **Promote** to a tier (atomic / literature)
2. **Archive** (no use case, save for later)
3. **Delete** (noise, no value)

If you can't decide, **the item is in limbo.** Mark it "deferred" and come back later. **Don't leave it in the inbox for more than 30 days.**

## The "atomic vs literature" decision

| Atomic | Literature |
|---|---|
| Your own idea, in your own words | External source, in the source's words |
| One idea per file | One source per file |
| Edit freely | Append-only after first write |
| Used for decisions | Used for references |
| Has a verdict / decision | Has a citation |

The line between them: **if you're making a decision, it's atomic. If you're citing a source, it's literature.**

## The "rules vs atomic" decision

|| Rules (in `<atomic-tier>/rules/`) | Atomic (in `<atomic-tier>/`) |
|---|---|---|
| Constraint derived from a failure | Idea in your own words |
| One paragraph | One or more paragraphs |
| Has date and counter-example | Has links and tags |
| Filed when failure pattern observed 3+ times | Filed when you have a new idea |
| Promoted to operator-context | Promoted to operator-decision |

Strike rules are a **subclass of atomic** with a strict format and a strict trigger. The atomic tier is broader. The rule folder name (`<atomic-tier>/rules/`) is a method-not-instance placeholder; pick the name that fits your chosen atomic tier (e.g., `2-atomic/rules/` per methodology `01-decide-vault-tier.md`).

## See also

- `methodology/01b-decide-vault-content.md` — the vault content doc covers frontmatter, tag namespacing, atomicity, note lifecycle, version discipline, MOCs, embeddings, backups. The tier-routing cheatsheet is the quick-reference; the content doc is the design methodology.
- `methodology/01-decide-vault-tier.md` — tier routing is parallel to content management.
- `methodology/01a-decide-memory.md` — vault content is Tier 5 (long-form) memory.

## What's next

- `kanban-lite-disciplines.md` — minimal kanban rules
- `x-ingestion-pipeline.md` — X post ingestion
- `tool-mapping-guide.md` — how the abstract concepts map to your tool
