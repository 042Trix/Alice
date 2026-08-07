# Methodology 05 — Decide which operational guards to write

> The fifth decision: which operational guards to write. Operational guards are **constraints derived from observed failures** — they prevent the agent from repeating a known mistake.

## What an operational guard is (in Alice's terms)

An **operational guard** is a one-paragraph constraint that says "when X happens, do Y, NOT Z." The guard exists because **three failures of the same shape already happened.** Operational guards are not aspirational. They're reactive.

Each operational guard has 5 properties:
1. **Date** — when the guard was created
2. **Shape** — the failure pattern
3. **Why** — what the guard prevents
4. **What to do** — the correct behavior
5. **Counter-example** — the antipattern the guard is correcting

If any is missing, the guard isn't ready.

## The 3-failure rule

**Operational guards come from observed failures, three of the same shape.** Don't write a guard from a single failure. The third time is when you know the pattern is real, not a one-off.

The 3-failure rule prevents two failure modes:
- **Premature guards** — writing a guard from one failure locks in a one-off as a permanent constraint
- **Never-guards** — never writing guards because "this is just one case" lets patterns go uncorrected

The 3-failure threshold is a heuristic, not a law. Some patterns are obvious after 2. Some need 5. But 3 is the default.

## Properties

- **Naming convention:** `op-N-short-name` (e.g., `op-5-no-fabrication`).
- **Severity:** `block` / `suggest` / `note`.
- **Lifecycle:** see the operational guard lifecycle (expiration, deduplication, supersession, severity).
- **Origin:** a 3-failure trigger (the same failure shape 3+ times).

## How to write one

When a failure hits 3+ times:
1. Write the rule in the operational guards section of your vault.
2. Add a `severity` field.
3. Add a `review-by` date (default: 12 months).
4. Cross-reference any supersedes or duplicates.

## Lifecycle of an operational guard

```
1. Failure  →  2. Failure  →  3. Failure (same shape)
                                       ↓
4. Operational guard draft  →  5. Operational guard (v1)  →  6. Operational guard (revised)
```

**1-3: Failure observation.** When the same failure pattern happens 3 times, note each occurrence with date + shape. The 3rd occurrence triggers the guard-drafting process.

**4: Operational guard draft.** Write the guard using `templates/op-guard.md.template`. The draft is a single markdown file in the guards subdirectory of your atomic tier (e.g., `<atomic-tier>/rules/op-guard-N-short-name-YYYY-MM-DD.md` per the naming chosen in `methodology/01-decide-vault-tier.md`).

**5: Operational guard (v1).** Once the guard is stable (no edits for 2 weeks), promote to v1. Move to the guards section of your vault's operational-guards index.

**6: Operational guard (revised).** When the guard is wrong, revise it. **Don't** delete the original; archive it. The history of the guard is part of its value.

## Lifecycle properties: expiration, deduplication, supersession, severity

The six-stage lifecycle above is the **flow** of a guard. A guard also has four **properties** that govern whether it stays alive, gets merged with a sibling, replaces an older guard, or escalates to a hard gate. Without these properties, guards accumulate into a brittle mass of exceptions. With them, the guards section stays proportional to the operator's actual recurring failures.

### Expiration

Every operational guard has a **review-by date**. The review-by date is the trigger for a deliberate decision, not an automatic delete.

- **Default review-by:** 12 months from creation. The guard is "young" for the first 90 days; "mature" from 90 days to 12 months; "review candidate" on the 12-month anniversary.
- **Operator-specific fast-track:** guards tagged as operator-specific (see "The 'this is operator-specific' filter" below) use a 6-month review-by instead of the 12-month default. Operator context shifts faster than system context; the guards should be reviewed on a shorter cadence.
- **What the review does:** the operator (or the curator role) reads the guard and decides one of three outcomes — **keep as-is**, **revise**, or **archive**. The decision is recorded as an audit-line on the guard file.
- **What happens if the review is missed:** the guard stops applying. It is not deleted; it is moved to a `_stale/` subdirectory and excluded from agent context. Re-promoting a stale guard requires a fresh review. This prevents guards from becoming invisible-but-binding dead weight.
- **Why this matters:** operational guards are reactive. The operator's system, tools, and agents change. A guard that was correct in 2026 may be obsolete in 2027. Without an expiration trigger, the guard stays "active" forever and produces friction in contexts it was never written for.

### Deduplication

Before writing a new operational guard, **check the existing guards section for the same failure shape**. If a matching guard exists, do not write a new one — revise or reuse the existing guard.

The check is three questions:

1. **Shape match:** does the new failure pattern reduce to an existing guard's shape (the abstract pattern, not the specific incident)?
2. **Why match:** does the existing guard prevent the same consequence?
3. **What-to-do match:** would the existing guard's correct behavior also fix the new failure?

If all three are "yes," the new guard is a duplicate. Reuse the existing guard; do not file a new one. If only one or two are "yes," the existing guard needs revision (see Supersession) rather than replacement.

**Why this matters:** duplicate guards inflate the guards section, dilute agent attention, and create version drift (one guard says X, another says X'). Dedup at write-time, not at audit-time. The 3-failure rule already filters for repeated patterns; deduplication is the second filter that says "the same pattern only gets one guard."

### Supersession

When a new guard replaces an old one, the **replacement relationship is documented as a forward link on the new guard** (`supersedes: <old-guard-id>`) and a back-reference on the old guard (`superseded_by: <new-guard-id>`).

- **Full supersession:** the new guard covers every failure the old guard covered. The old guard is archived (not deleted) with a link to its successor. The 3-failure rule's history is preserved in the audit chain.
- **Partial supersession:** the new guard covers a subset of the old guard's failures. The old guard stays active for the cases the new guard does not cover; the new guard's `supersedes` link names the partial overlap explicitly.
- **No silent supersession:** if the new guard's author does not record the link, the supersession is not valid. Both guards coexist, and the agent sees conflicting guidance. The operator catches this at audit time and forces the link to be filled in.

**Why this matters:** without supersession links, guard history is invisible. Future operators reading a guard's audit trail cannot tell whether the guard is "still alive because nothing replaced it" or "still alive but overlapping with a newer guard." The link is one line of frontmatter; the audit clarity is permanent.

### Severity

Every operational guard has a **severity level** that determines how the gate handles it. Three levels:

| Severity | Gate behavior | When to use |
|---|---|---|
| `block` | The guard hard-fails the gate. The action cannot proceed without operator override. | Failure has irreversible consequences (lost data, paid spend, security exposure, irreversible external action). |
| `suggest` | The guard warns the gate. The action can proceed; the warning is logged and surfaced to the operator. | Failure is recoverable but expensive (context burn, time loss, audit noise). |
| `note` | The guard documents the pattern and continues. No gate interaction. | Failure is informational only; the agent should know the pattern but the consequence does not warrant a gate interaction. |

**Default severity is `suggest`.** A new guard starts as `suggest`; after 90 days of stable use without revision, the operator may escalate to `block` (if the failure's consequences warrant) or leave at `suggest` (if the guard is more "watch out for this" than "this will hurt you"). Demoting from `block` to `suggest` is allowed; demoting from `block` to `note` requires a written reason in the guard's audit-line.

**Severity is not a substitute for the guard itself.** A `note`-severity guard is still a real guard; it just doesn't gate the action. The three failure modes Alice warns about — premature guards, never-guards, and operator-specific guards — apply equally at all severity levels.

### Companion template

The four properties above are recorded as frontmatter on each operational guard file. The companion template (`templates/op-guard.md.template`) should grow four new fields (`expires_on`, `severity`, `supersedes`, `dedup_check`) in a follow-up ticket; this methodology doc defines the **method**, and the template defines the **fillable form**. Adding the template fields without the methodology definition would be field-without-rule.

## Worked example — a guard's full lifecycle

The example below uses an anonymous structural pattern (call it **Guard G**) so the method-not-instance rule is preserved. The shape, dates, and decision chain illustrate the full lifecycle: **creation → maturation → supersession → review**.

### Setup

Guard G prevents an agent from taking an **irreversible external action without operator confirmation**. Examples of irreversible external actions in any system: paid spend, irreversible API calls, published content, sent messages, deleted files, model swaps.

| Property | Value |
|---|---|
| **Date created** | 2026-07-29 |
| **Failure shape** | "When the agent is about to perform an irreversible external action, the agent proceeds without waiting for operator confirmation." |
| **Why** | "Irreversible actions are by definition unrecoverable. The operator must approve them explicitly." |
| **What to do** | "File a ticket; wait for operator review; only then perform the action." |
| **Counter-example** | (A real prior incident where the agent committed an irreversible action and the operator had to manually reverse.) |
| **Severity** | `block` |
| **Expires on** | 2027-07-29 (12-month default) |
| **Supersedes** | none |
| **Dedup check** | none of the existing guards cover irreversible-external-action; the closest guard was about a different shape (recoverable noise). |

### Year 0 — creation (2026-07-29)

Guard G is drafted using the template. The 3-failure rule was satisfied: three incidents of the same shape happened within a single week. The operator approves the guard and files it under `<atomic-tier>/rules/`.

### Year 0, day 30 — first revision

A fourth incident happens. The shape matches G exactly. The guard fires; the agent files a ticket and waits. The operator confirms. No guard change needed. Audit-line: `[2026-08-28] Guard G — fired on incident #4; behavior correct; no revision.`

### Year 0, day 90 — maturity check

The guard has fired twice more (incidents #5 and #6) with no agent misbehavior. Severity is still `suggest` (default). The operator reads the guard, decides the failures have been **mildly expensive but recoverable** (no irreversible action was actually taken because the guard fired), and keeps severity at `suggest`. Audit-line: `[2026-10-27] Guard G — maturity check; severity stays at suggest; keep.`

### Year 0, day 180 — escalation

A new failure: the agent attempts an irreversible action in a context G did not cover (different action category, same shape). The agent pauses; the operator is paged. After review, the operator **escalates G to `block`**: the failure's consequence is now understood to be severe. Audit-line: `[2027-01-26] Guard G — escalated from suggest to block; failure consequence reclassified as severe.`

### Year 1, day 30 — supersession

A new guard is needed for a **related but distinct shape**: "When the agent is about to publish a draft externally, the agent uses a draft-mode confirmation step before sending." This new guard (call it **Guard H**) covers a subset of G's failures. The author of H writes `supersedes: G (partial — covers external-publish subset only)`. G stays active for the non-publish irreversible actions; H covers the publish subset. Audit-lines: `[2027-02-25] Guard G — superseded (partial) by Guard H; G stays active for non-publish irreversible actions.`

### Year 1, day 365 — review-by anniversary

G is now 12 months old. The operator reads G, checks the audit-line history, and sees:
- Guard fired 4 times in year 1.
- 3 of those firings were caught by G (guard worked).
- 1 was caught by H (the partial-supersession subset).
- 0 incidents slipped through either guard.

The operator decides: **revise**. The `expires_on` is bumped to 2028-07-29 and the `supersedes` field is updated to reflect that H now covers the publish subset. Audit-line: `[2027-07-29] Guard G — 12-month review; revised; expires_on bumped to 2028-07-29; supersedes link to H confirmed.`

### Year 2, day 365 — second review

G has fired twice in year 2. Both firings were non-publish (H covered the publish subset cleanly). The operator decides: **keep as-is**. The guard is alive and active; the lifecycle continues.

### Year 3 — hypothetical future

If G ever stops firing for 12 consecutive months AND no related guard has fired either, the operator may decide to **archive** G. The audit-chain shows the guard's full life: 2026-07-29 creation, 2027-01-26 escalation to `block`, 2027-02-25 partial supersession by H, three 12-month reviews (revise, keep, keep). The history is preserved even though the guard is archived.

This is the lifecycle: guards are not immortal; they are not disposable. They are working memory with a heartbeat.

## Operational guard vs skill vs SOP

| Concept | What | When to use |
|---|---|---|
| **Operational guard** | Constraint that prevents a known failure | Failure pattern observed 3+ times |
| **Skill** | Procedure the agent invokes | Task recurs weekly, repeatable, verifiable, bounded |
| **SOP** | Manual checklist you follow | Task recurs but the agent shouldn't do it (you do it manually) |

An operational guard is a **constraint on agent behavior**. A skill is a **procedure**. An SOP is a **manual checklist**. Don't conflate.

## What goes in an operational guard

The 5 properties:

1. **Date** — when the guard was created (e.g., "2026-07-21")
2. **Shape** — the failure pattern, in one sentence (e.g., "agent narrated actions in chat instead of doing them silently")
3. **Why** — what the guard prevents (e.g., "operator context window fills with narrative instead of action")
4. **What to do** — the correct behavior (e.g., "agent does the action; chat only confirms with one sentence")
5. **Counter-example** — the antipattern (e.g., "in the previous session, the agent narrated 5 tool calls in chat; operator context ran out of room")

A good guard is one paragraph. If it's longer, you're writing a skill, not a guard. If it's shorter, you haven't explained the shape.

## Operational guard pitfalls

1. **"I'll write guards proactively for things that might go wrong."** → No. Operational guards are reactive. Write SOPs or skills for proactive concerns. Operational guards come from observed failures.

2. **"I have 14 operational guards."** → Probably 5-6 of them are real. The other 8-9 are operator-specific incidents, not guards. Audit and demote or archive.

3. **"I'll write a guard that says 'always do X.'"** → That's a procedural preference, not a constraint. Promote to a skill or a workflow. Operational guards are about prevention, not preference.

4. **"The guard should be a wall of text."** → No. An operational guard is one paragraph. If it's longer, you're writing a skill.

5. **"I'll never break this guard."** → You'll break it. The guard is a pattern, not a law. The point is to recognize the shape, not to enforce it.

6. **"I'll write a guard that the agent already follows."** → No. The guard exists because the agent didn't follow it (3+ times). If the agent already follows it, the guard is dead weight.

## The "this is operator-specific" filter

Operational guards are **operator-context** — they're about your system, your preferences, your agent's quirks. An operational guard written for one operator is not portable to another operator. (This is the opposite of skills, which are designed to be portable.)

When you write an operational guard, mark it with:
- **Operator** — who does the guard apply to? (Usually: "all agents in this vault")
- **Context** — what's the operator's context? (e.g., "high-frequency small-team operator with 8 boards")
- **Reusability** — is this guard for any operator with this pattern, or only this one?

If a guard is operator-specific, **its review-by date is 6 months instead of the default 12 months** (see "Lifecycle properties → Expiration" above). The guard is a snapshot of a moment. Move on.

## Operational guard examples (skeleton)

Three example guards (these are templates, not real guards):

- **"Guard against narrating tool calls in chat."** Date 2026-07-21. Shape: agent narrates "I will now use the file tool to read X." Why: fills operator context. What to do: agent does the action; chat confirms with one sentence.
- **"Guard against shadow-closing tickets."** Date 2026-07-21. Shape: agent marks a ticket as done to reduce WIP, but the work isn't done. Why: hides the backlog. What to do: use `kanban block` + comment instead of `kanban complete`.
- **"Guard against auto-handoff on irreversible-but-routine actions."** Date 2026-07-21. Shape: agent commits an irreversible action without operator confirmation. Why: irreversible-but-routine is a category of "I should not be doing this." What to do: file a ticket, wait for operator review.

These are skeletons. Your operational guards will be different. The point is the **shape**, not the content.

## See also

- `methodology/01a-decide-memory.md` — **operational guards are rule-based memory.** They live in the vault atomic tier (long-form memory) but are loaded into the agent's context when relevant. Operational guards are the only "memory that constrains" — they're applied as overrides to the agent's natural behavior.
- `methodology/03a-decide-agent-vault-interaction.md` — operational guards like "no direct mutation" depend on the interaction patterns. The interaction doc defines the 5 patterns (read-only, propose, edit, scaffold, archive) and the read-then-write discipline that operational guards like "no direct mutation" build on.
- `methodology/03b-decide-operator-agent-interaction.md` — operational guards are operator-governance. The governor role writes operational guards; the curator role maintains them. The operator-agent doc covers the 5 operator roles.

- `methodology/06-iteration-loop.md` — the engine that uses operational guards
- `methodology/07-council-methodology.md` — when to convene a council to revise operational guards
- `templates/op-guard.md.template` — the fillable form

## Anti-patterns to watch for

1. **"I'll write a guard for every failure I see."** → No. 3+ of the same shape, not 3 different shapes.
2. **"The guard should prevent the agent from doing X."** → That's a constraint, but if X is rare, the guard is overkill. Operational guards are for patterns, not for one-off.
3. **"The guard should be enforced by the system."** → No. The guard is a pattern, not a guard. The system may have other guards; the guard is the human-readable version.
4. **"I'll write the guard and forget it."** → No. The guard has a lifecycle. If it's never triggered, archive it. If it's triggered too often, the guard isn't preventing the failure; the failure is structural.
