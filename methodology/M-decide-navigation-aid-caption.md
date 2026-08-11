---
id: alice-methodology-navigation-aid-caption
created: 2026-08-08T15:30:00Z
updated: 2026-08-08T15:30:00Z
title: "Methodology — Decide navigation-aid caption: diagrams labeled as aids must carry a 3-part caption"
type: methodology
status: active
source: alice-framework
version: 0.1.0
tags: [kind:methodology, kind:navigation-aid, kind:caption, kind:convention, kind:rendering, kind:council-output, project:alice]
confidence: 1.0
links: ["[[methodology/00-decide-ticket-naming.md]]", "[[methodology/07-council-methodology.md]]", "[[methodology/M-decide-council-sizing.md]]", "[[methodology/M-decide-x-article-review-flow.md]]", "[[templates/caption-convention-rule.md.template]]", "[[concepts/council-log-framework-retro.md]]"]
---

# Methodology — Decide navigation-aid caption

> A diagram labeled as a "navigation aid" must carry a 3-part caption: (1) name the artifact a navigation aid, (2) state the date or context of the framing, (3) name Alice's canonical schema so the reader cannot mistake the aid for the schema. The caption is honest about the diagram's role; without it, the diagram over-promises.

## Part 1: Why this rule exists

Alice renders some of its methodology as visual aids — diagrams, stack charts, flow sketches, layered-architecture illustrations — to help newcomers navigate the framework. These renders are useful. They are also **fragile in a specific way**: an unlabeled diagram risks being mistaken for the canonical schema it was drawn to illustrate. A reader looking at a 3-layer stack diagram of Alice's conceptual layers might (wrongly) conclude that Alice's taxonomy is the 3-layer taxonomy, when in fact the canonical schema is something else (the AREA taxonomy for tickets; the 7-tier taxonomy for memory; the 5-decision-cluster taxonomy for methodology guides).

The risk was first surfaced in the 5-seat council re-run on the X article-1 review (2026-08-08, ticket `t_73ea1fff`). The Product seat verdict: **"Without the caption, the diagram over-promises. With the caption, the diagram is honest about its role."** The caption is not nice-to-have; it is the load-bearing surface that distinguishes a navigation aid from a canonical schema.

The 5-seat verdict also identified the inverse failure: a caption-only convention has no enforcement, so the rule would still drift. The durable fix is **three-way**: a methodology entry that defines the rule (this doc), a fillable template that documents the caption text per navigation aid (the `caption-convention-rule.md.template`), and a council log template that captures the caption string when the council proposes a navigation aid as a delta.

## Part 2: When the rule fires

The rule fires on any diagram or visual aid that is **labeled as a navigation aid** in Alice's methodology. Triggers:

1. **New diagram added to a methodology doc** — if the author writes "navigation aid," "for newcomers," "visual overview," "stack chart," "layered diagram," or similar framing language, the diagram is a navigation aid under this rule.
2. **A diagram retrofitted with a navigation-aid label** — if an existing diagram is later described as a navigation aid (in a council log, a PR description, or a methodology note), the rule applies retroactively to that diagram's caption.
3. **A council proposes a navigation aid as a delta** — the council log framework-retro template captures the proposed caption string in its `caption` field. The caption is part of the council's recommendation; the downstream artifact inherits it.
4. **A navigation aid is updated to a new framing context** — e.g., a 2026-mid-year framing becomes a 2027 framing. The caption's date/context component must be updated; the rule fires on every context-refresh, not only on initial creation.

The rule does **NOT** fire on:

- **Canonical schemas** — the AREA taxonomy, the 7-tier memory taxonomy, the 5-decision-cluster methodology index, the 5-role operator taxonomy. These are not navigation aids; they are load-bearing for builders and don't carry captions.
- **Decision-ordered taxonomies** — anything that prescribes an order or sequence of decisions (the iteration loop, the spec-first-flow phases, the work-graph states). The order is the schema; a diagram that adds nothing beyond the ordered list is decorative, not a navigation aid.
- **Bill-of-rights lists** — the agent design principles, the operator role boundaries, the doc-writer role contract. These are explicit guarantees; a visual aid that re-states them is a redundant render, not a navigation aid.

The test: **does the diagram add a "how to read Alice" framing that isn't already in the prose?** If yes, it's a navigation aid. If no, it's decorative or canonical — no caption required.

## Part 3: The 3-part caption format

Every navigation aid carries a caption with exactly three parts, in this order:

1. **Name the artifact a "navigation aid."** The first words of the caption are the literal phrase: `Navigation aid`. This is the honest label. The reader knows immediately that the artifact is not the canonical schema.
2. **State the date or context of the framing.** A short parenthetical that pins the diagram to a specific framing moment. Examples: `(2026-mid-year public framing)`, `(Q3 2026 retro framing)`, `(2026 v0.1.x family framing)`. The date/context tells the reader when the framing was valid and signals that the aid may need refresh.
3. **Name Alice's canonical schema.** A second sentence that explicitly names the authoritative schema this aid is rendering. Examples: `Alice's canonical schema is the AREA taxonomy.`, `Alice's canonical schema is the 7-tier memory taxonomy.`, `Alice's canonical schema is the methodology 5-decision-cluster taxonomy.`

### Format template

```
Navigation aid (<date-or-context>). Alice's canonical schema is <canonical-schema-name>.
```

### Worked example — Delta-1 3-layer stack diagram

The 3-layer stack diagram proposed in the X article-1 council verdict (Q1 partial adoption) carries this caption:

```
Navigation aid (2026-mid-year public framing). Alice's canonical schema is the AREA taxonomy.
```

The caption says three things the diagram cannot say by itself:
- The diagram is a navigation aid, not a canonical schema.
- The diagram was framed in mid-2026; readers should not assume it captures Alice's state-of-the-art in later years.
- Alice's canonical schema for ticket grouping is the AREA taxonomy, not the 3-layer stack shown in the diagram.

### Worked example — a hypothetical second navigation aid

A future diagram of Alice's memory tiers might carry this caption:

```
Navigation aid (2026 v0.1.x family framing). Alice's canonical schema is the 7-tier memory taxonomy (see methodology/01a-decide-memory.md).
```

Same three-part structure: name the aid, pin the framing context, name the canonical schema and its canonical doc path.

### What the caption must NOT contain

- **No promotional language** — "Alice's powerful 3-layer model" or "the next-generation taxonomy" violates the rule. The caption is descriptive, not persuasive.
- **No schema-equivalence claims** — "equivalent to the AREA taxonomy" or "an alternative view of the canonical schema" violates the rule. The aid is not equivalent to the schema; it is a render of the schema from a specific framing context.
- **No shelf-life claims** — "this aid is valid until 2028" violates the rule. Shelf life is a maintenance-burden judgment (Operator seat's contribution), not a caption claim. The maintenance-burden field in the council log template carries that information.
- **No operator-action claims** — "use this aid when..." violates the rule. The aid is descriptive; the operator decides when to use it.

## Part 4: Verification gate

No navigation aid ships without the 3-part caption. The gate is enforced at three surfaces:

1. **Methodology doc self-review** — when a methodology doc is written or updated, the author scans for diagrams and verifies each diagram is either (a) labeled as a navigation aid AND carries the caption, or (b) is a canonical schema / decision-ordered taxonomy / bill-of-rights list (no caption required). The verification is part of the doc's `wiki_lint` pass.
2. **Council log template** — the framework-retro template (at `~/.hermes/skills/autonomous-ai-agents/hermes-council/templates/council-log-framework-retro.md`) carries a `caption` field that is **required when the council proposes a navigation aid as a delta**. The field captures the caption string verbatim. A council log without a caption field populated, where a navigation aid is proposed, fails the verifier gate.
3. **Captions on Delta-1 and the 3-layer diagram in `methodology/00-decide-ticket-naming.md`** — both diagrams receive their captions as part of the Delta-1 stack-diagram ticket (`t_aa72881c`). The caption is verbatim the Q1 example above.

## Part 5: Maintenance burden

~0.5 doc edits/year. The rule itself is shelf-stable — a 3-part caption format doesn't change as Alice evolves. The work is reviewing new navigation aids as they are added (verifying each carries the caption) and updating captions when a navigation aid's framing context shifts (e.g., "2026-mid-year public framing" → "2027 v0.2.x family framing"). Both reviews are 5-minute edits and run on a per-add / per-refresh cadence, not on a calendar.

## Cross-references

- `methodology/00-decide-ticket-naming.md` — Delta-1 stack diagram lives here; carries the worked-example caption.
- `methodology/07-council-methodology.md` — the council methodology that produces the `caption` field.
- `methodology/M-decide-council-sizing.md` — the Product seat's contribution originated in the 5-seat council re-run.
- `methodology/M-decide-x-article-review-flow.md` — the flow that surfaced the caption requirement.
- `templates/caption-convention-rule.md.template` — the fillable template that documents the convention.
- `~/.hermes/skills/autonomous-ai-agents/hermes-council/templates/council-log-framework-retro.md` — the council log template with the `caption` field.
- t_6314966f — the ticket this methodology doc was filed under.
- t_aa72881c — Delta-1 stack-diagram ticket (the first artifact to receive the caption).
- t_73ea1fff — the 5-seat council verdict that originated the rule.
- t_6f0383f0 — the triage ticket that filed this doc-writer ticket.
- t_62a99460 — the master ticket (x-article-review article 1) under which this rule was an execution child.

## Audit-line

`## [2026-08-08T15:30Z] doc-writer-ship — methodology/M-decide-navigation-aid-caption.md v0.1.0 shipped; codifies the 3-part caption convention for navigation aids (navigation aid + date/context + canonical schema name); Product seat contribution from the 5-seat council re-run on t_73ea1fff. source=t_6314966f.`
