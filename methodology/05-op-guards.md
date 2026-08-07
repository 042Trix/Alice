# Methodology 05 — Decide which strike rules to write

> The fifth decision: which strike rules to write. Strike rules are **constraints derived from observed failures** — they prevent the agent from repeating a known mistake.

## What a strike rule is (in Alice's terms)

A **strike rule** is a one-paragraph constraint that says "when X happens, do Y, NOT Z." The rule exists because **three failures of the same shape already happened.** Strike rules are not aspirational. They're reactive.

Each strike rule has 5 properties:
1. **Date** — when the rule was created
2. **Shape** — the failure pattern
3. **Why** — what the rule prevents
4. **What to do** — the correct behavior
5. **Counter-example** — the antipattern the rule is correcting

If any is missing, the rule isn't ready.

## The 3-failure rule

**Strike rules come from observed failures, three of the same shape.** Don't write a rule from a single failure. The third time is when you know the pattern is real, not a one-off.

The 3-failure rule prevents two failure modes:
- **Premature rules** — writing a rule from one failure locks in a one-off as a permanent constraint
- **Never-rules** — never writing rules because "this is just one case" lets patterns go uncorrected

The 3-failure threshold is a heuristic, not a law. Some patterns are obvious after 2. Some need 5. But 3 is the default.

## Lifecycle of a strike rule

```
1. Failure  →  2. Failure  →  3. Failure (same shape)
                                       ↓
4. Strike rule draft  →  5. Strike rule (v1)  →  6. Strike rule (revised)
```

**1-3: Failure observation.** When the same failure pattern happens 3 times, note each occurrence with date + shape. The 3rd occurrence triggers the rule-drafting process.

**4: Strike rule draft.** Write the rule using `templates/strike-rule.md.template`. The draft is a single markdown file in the rules subdirectory of your atomic tier (e.g., `<atomic-tier>/rules/strike-N-short-name-YYYY-MM-DD.md` per the naming chosen in `methodology/01-decide-vault-tier.md`).

**5: Strike rule (v1).** Once the rule is stable (no edits for 2 weeks), promote to v1. Move to the rules section of your vault's strike-rules index.

**6: Strike rule (revised).** When the rule is wrong, revise it. **Don't** delete the original; archive it. The history of the rule is part of its value.

## Strike rule vs skill vs SOP

| Concept | What | When to use |
|---|---|---|
| **Strike rule** | Constraint that prevents a known failure | Failure pattern observed 3+ times |
| **Skill** | Procedure the agent invokes | Task recurs weekly, repeatable, verifiable, bounded |
| **SOP** | Manual checklist you follow | Task recurs but the agent shouldn't do it (you do it manually) |

A strike rule is a **constraint on agent behavior**. A skill is a **procedure**. An SOP is a **manual checklist**. Don't conflate.

## What goes in a strike rule

The 5 properties:

1. **Date** — when the rule was created (e.g., "2026-07-21")
2. **Shape** — the failure pattern, in one sentence (e.g., "agent narrated actions in chat instead of doing them silently")
3. **Why** — what the rule prevents (e.g., "operator context window fills with narrative instead of action")
4. **What to do** — the correct behavior (e.g., "agent does the action; chat only confirms with one sentence")
5. **Counter-example** — the antipattern (e.g., "in the previous session, the agent narrated 5 tool calls in chat; operator context ran out of room")

A good rule is one paragraph. If it's longer, you're writing a skill, not a rule. If it's shorter, you haven't explained the shape.

## Strike rule pitfalls

1. **"I'll write rules proactively for things that might go wrong."** → No. Strike rules are reactive. Write SOPs or skills for proactive concerns. Strike rules come from observed failures.

2. **"I have 14 strike rules."** → Probably 5-6 of them are real. The other 8-9 are operator-specific incidents, not rules. Audit and demote or archive.

3. **"I'll write a rule that says 'always do X.'"** → That's a procedural preference, not a constraint. Promote to a skill or a workflow. Strike rules are about prevention, not preference.

4. **"The rule should be a wall of text."** → No. A strike rule is one paragraph. If it's longer, you're writing a skill.

5. **"I'll never break this rule."** → You'll break it. The rule is a pattern, not a law. The point is to recognize the shape, not to enforce it.

6. **"I'll write a rule that the agent already follows."** → No. The rule exists because the agent didn't follow it (3+ times). If the agent already follows it, the rule is dead weight.

## The "this is operator-specific" filter

Strike rules are **operator-context** — they're about your system, your preferences, your agent's quirks. A strike rule written for one operator is not portable to another operator. (This is the opposite of skills, which are designed to be portable.)

When you write a strike rule, mark it with:
- **Operator** — who does the rule apply to? (Usually: "all agents in this vault")
- **Context** — what's the operator's context? (e.g., "high-frequency small-team operator with 8 boards")
- **Reusability** — is this rule for any operator with this pattern, or only this one?

If a rule is operator-specific, **archive it after 6 months of no incidents.** The rule is a snapshot of a moment. Move on.

## Strike rule examples (skeleton)

Three example rules (these are templates, not real rules):

- **"Strike against narrating tool calls in chat."** Date 2026-07-21. Shape: agent narrates "I will now use the file tool to read X." Why: fills operator context. What to do: agent does the action; chat confirms with one sentence.
- **"Strike against shadow-closing tickets."** Date 2026-07-21. Shape: agent marks a ticket as done to reduce WIP, but the work isn't done. Why: hides the backlog. What to do: use `kanban block` + comment instead of `kanban complete`.
- **"Strike against auto-handoff on irreversible-but-routine actions."** Date 2026-07-21. Shape: agent commits an irreversible action without operator confirmation. Why: irreversible-but-routine is a category of "I should not be doing this." What to do: file a ticket, wait for operator review.

These are skeletons. Your strike rules will be different. The point is the **shape**, not the content.

## See also

- `methodology/01a-decide-memory.md` — **strike rules are rule-based memory.** They live in the vault atomic tier (long-form memory) but are loaded into the agent's context when relevant. Strike rules are the only "memory that constrains" — they're applied as overrides to the agent's natural behavior.
- `methodology/03a-decide-agent-vault-interaction.md` — strike rules like "no direct mutation" depend on the interaction patterns. The interaction doc defines the 5 patterns (read-only, propose, edit, scaffold, archive) and the read-then-write discipline that strike rules like "no direct mutation" build on.
- `methodology/03b-decide-operator-agent-interaction.md` — strike rules are operator-governance. The governor role writes strike rules; the curator role maintains them. The operator-agent doc covers the 5 operator roles.

- `methodology/06-iteration-loop.md` — the engine that uses strike rules
- `methodology/07-council-methodology.md` — when to convene a council to revise strike rules
- `templates/strike-rule.md.template` — the fillable form

## Anti-patterns to watch for

1. **"I'll write a rule for every failure I see."** → No. 3+ of the same shape, not 3 different shapes.
2. **"The rule should prevent the agent from doing X."** → That's a constraint, but if X is rare, the rule is overkill. Strike rules are for patterns, not for one-off.
3. **"The rule should be enforced by the system."** → No. The rule is a pattern, not a guard. The system may have other guards; the rule is the human-readable version.
4. **"I'll write the rule and forget it."** → No. The rule has a lifecycle. If it's never triggered, archive it. If it's triggered too often, the rule isn't preventing the failure; the failure is structural.
