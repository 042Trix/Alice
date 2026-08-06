# Methodology 02 — Decide which skills to build

> The second decision: which skills to build. Skills are procedures the agent can invoke. This is **what the agent can do** — separate from **who the agent is** (covered in 03-decide-agents).

## What a skill is (in Alice's terms)

A **skill** is a packaged procedure the agent can invoke by name. It typically includes:
- A name and one-line description
- Trigger conditions (when to load)
- Inputs the agent provides
- Outputs the agent receives
- The procedure itself (steps, decision points, edge cases)

**Skills are NOT:**
- A single chat message (that's a prompt, not a skill)
- A list of items (that's an index, not a skill)
- A todo or task (that's a ticket, not a skill)
- A person (that's an agent, not a skill)

## The decision: when to build a skill

A skill should be built when **all four** of these are true:

1. **Recurs** — the task happens weekly or more often
2. **Repeatable** — the agent can do it without your help
3. **Verifiable** — the agent can confirm success (artifact exists, query returns X, etc.)
4. **Bounded** — the inputs and outputs are well-defined

If any of the four fails, **don't build a skill yet.** Either:
- The task doesn't recur → it's a one-shot, just do it inline
- It's not repeatable → it requires your judgment, not the agent's
- It's not verifiable → the agent can't tell if it succeeded
- It's not bounded → the agent doesn't know when to stop

## Skill-brief template

Before building, write a skill-brief (use `templates/skill-brief.md.template`). The brief has 7 sections:

1. **Trigger conditions** — when does the agent load this skill?
2. **Inputs** — what does the agent provide?
3. **Outputs** — what does the agent receive?
4. **Procedure** — the steps (1-2 paragraphs, not a wall of text)
5. **Edge cases** — what can go wrong?
6. **Verification** — how does the agent know it succeeded?
7. **Cost** — how many tokens, how long, how many tool calls?

If you can't fill in 5 of 7 sections, you don't have a skill yet.

## Lifecycle of a skill

```
1. Single-file SOP      →  2. Skill-brief draft
                            ↓
4. Skill (v1)  ←  3. Validation (1-2 weeks in production)
```

**Single-file SOP first.** Don't build a skill for a task you haven't done manually 3+ times. Write a 1-page SOP (markdown, in atomic tier), follow it manually, and update it for 1-2 weeks. If the SOP stabilizes (no edits for 5+ runs), promote it to a skill.

**Why this order matters:** the SOP captures the **what** (what to do). The skill captures the **how the agent does it** (procedural). Building a skill before the SOP is stable is asking the agent to invent the procedure, which is the opposite of "skill."

## When NOT to build a skill

- **You haven't done the task yourself yet.** Build the SOP first.
- **The task depends on a single decision that's not generalizable.** That's a judgment, not a skill.
- **The skill would just wrap a single tool call.** That's not a skill, that's a shortcut. Shortcuts can be aliases, not skills.
- **The skill requires more than 5 tool calls to execute.** That's a workflow, not a skill. Workflows are scripts (cron or inline) or spawned sub-agents (ephemeral task-scoped instances of the main agent — see `methodology/03-decide-agents.md` Part 12 for the agent-typology glossary; downstream agents are persistent profiles, not spawned sub-agents).
- **The skill's trigger conditions overlap with another skill.** Merge them, or make the trigger conditions explicit and non-overlapping.

## The "test against 3 contexts" rule

Before promoting a skill-brief to a skill, run the agent with the skill against **3 distinct contexts** (3 different files, 3 different scenarios). If the skill succeeds on 2 of 3, ship as v0.1. If it fails on 1 of 3, fix and retest. If it fails on 2+, the skill is not yet ready.

The 3 contexts are not "test cases" in the QA sense. They're **real scenarios** that would have triggered the skill in your normal operation. The point is: does the skill work on realistic inputs, not synthetic ones?

## Skill design pitfalls

1. **Naming the skill the same as the tool it wraps.** "Skill: do-thing" is not a skill. Skills should be named for the **outcome**, not the tool. ("Skill: vault-route" is a skill. "Skill: write-md-file" is a shortcut.)
2. **Trigger conditions that are too broad.** "When the user mentions notes" triggers on every chat. "When the user pastes an X link" is specific.
3. **Outputs that depend on user confirmation.** A skill that asks "did I do this right?" is broken. Verify internally and return the result.
4. **Procedures that fork too early.** Decision-heavy procedures are workflows, not skills. Reduce decision points or split into multiple skills.
5. **Cost not estimated.** If you can't estimate tokens / tool calls / time, the skill isn't bounded. Don't build it.

## What the agent needs

The agent doesn't need to know which skills exist ahead of time. The agent should:
- Have a way to list available skills (your tool's skill manager)
- Have a way to load a skill by name
- Have a way to validate that a skill is the right one for the current task (trigger conditions)

If your tool doesn't have a skill manager, the agent will resort to inline prompts. That's fine for v0, but you should design a skill manager for v1.

## Worked example (skeleton)

See `worked-examples/01-*/methodology-notes/02-skills.md` in the archetype closest to your operator role for an anonymized example.

## See also

- `methodology/01a-decide-memory.md` — **skills are procedural memory (Tier 2).** The skill set is the agent's procedural memory; each skill is loaded on trigger conditions; ≤30 skills per session is the budget.
- `methodology/03a-decide-agent-vault-interaction.md` — skills are one of the 5 interaction patterns (read-only, propose, edit, scaffold, archive). A skill that reads the vault uses Pattern 1; a skill that writes uses Pattern 3. The tool-call budget applies per skill invocation.

## What's next

- `methodology/03-decide-agents.md` — once you know which skills exist, you know which agents to scaffold
- `methodology/05-strike-rules.md` — the constraints that prevent skills from overreaching
- `methodology/06-iteration-loop.md` — the engine that drives skill usage

## Anti-patterns to watch for

1. **"I should build a skill for everything."** → No. SOPs are the substrate. Skills are the layer above.
2. **"I should build a skill for the one time I'll need it."** → No. One-off. Just do it.
3. **"I'll improve the skill as I go."** → Yes, but start with the SOP, not the skill. The SOP is what you're improving.
4. **"The skill should be flexible."** → No. Skills should be specific. "Skill: validate-rule" is better than "Skill: do-stuff."

## The no-nesting rule (skills are 1 layer deep)

**Rule:** Skills do not nest. A skill is one layer deep — a flat, named procedure the agent loads on trigger. If a skill needs sub-procedures, those sub-procedures are **separate skills**, each with its own trigger conditions, linked from the parent by name or wikilink. They do **not** live inside the parent's body, do **not** appear as nested `### sub-skill` headings, and do **not** ship as inlined procedure steps in the parent.

**Rationale:** nesting collapses the trigger surface — the agent cannot load a sub-skill independently, the parent's SKILL.md becomes a wall of text, and updates to a sub-procedure force a re-version of every parent that includes it.

### Anti-pattern

```markdown
# Skill: run-vault-cleanup
## Trigger
When the operator says "tidy the vault"
## Procedure
1. Run orphan-scan ...
2. For each orphan, run:
   ### Sub-skill: decide-archive-or-keep
   - If last_touched > 1y → archive
   - If linked > 3 times → keep
   - Else → needs_input
3. Apply results
```

The "sub-skill" inside `run-vault-cleanup` is not a skill. It is a procedure inlined into the parent. It cannot be loaded by name. Its trigger conditions are implicit. Its cost is bundled into the parent's cost. Updates to "decide-archive-or-keep" silently re-version `run-vault-cleanup`.

### What the agent does instead

Promote the sub-procedure to its own skill with an explicit trigger, and link to it from the parent by name:

- `skill: decide-archive-or-keep` — own SKILL.md, own trigger ("when the operator flags a vault orphan"), own verification, own version.
- `skill: run-vault-cleanup` — references `decide-archive-or-keep` by wikilink in the procedure, does NOT inline its logic.

The depth is **always 1**. A skill references other skills; it does not contain them.

### Boundary case: "but my sub-procedure is too small to be a skill"

If the sub-procedure is < 5 lines and has no independent trigger, it is **inline procedure text** in the parent's body — not a sub-skill, not a skill. The no-nesting rule says "no sub-skill headings," not "no helper text." A skill can have helper paragraphs, examples, or a 3-line checklist as part of its own procedure. The rule kicks in when the helper grows enough to want its own trigger or its own version — at that point, promote it to a skill.

## The no-implicit-model-knowledge rule

**Rule:** An agent's domain work is **the sum of its skills**. The agent does not fall back on training-data knowledge to perform domain work. If the operator asks the agent to do X, and X has no matching skill, the agent does NOT improvise from priors. It escalates — typically by filing a `needs_input` ticket that names the gap, or by asking the operator to point at a skill or write one.

**Rationale:** training-data knowledge is invisible, un-versioned, and drifts. A skill is visible, versioned, and tested. Letting the agent fill the gap with training data means the operator cannot audit what the agent did, cannot correct it via the skill system, and cannot tell "did the agent use the documented procedure or did it make something up that happened to work?"

### Anti-pattern

The operator says: "Triage the inbox." No skill named `triage-inbox` exists. The agent, drawing on training-data knowledge of what triage means, sorts the inbox by date, labels items, drafts replies, and reports "triage complete." The skill index has zero new entries. The operator cannot reproduce the agent's behavior, cannot tell which items were sorted on what rule, and cannot fix the triage policy next time without re-prompting the agent from scratch.

This is also the failure mode that produces **invisible failures**: the agent "does the right thing" until it doesn't, and the operator has no log to diff against.

### What the agent does instead

Three valid responses, in priority order:

1. **Surface the gap as a ticket.** File a `needs_input` ticket (or escalate via the operator's preferred channel) that names the missing skill: "Triage the inbox — no matching skill found. Options: (a) write a skill-brief for `triage-inbox`, (b) point me at an existing skill, (c) this is a one-shot — do it inline and capture the SOP for later."
2. **Do it inline and capture the SOP.** If the operator says "just do it," the agent does the task without a skill, AND captures the procedure as a 1-page SOP in atomic tier. The SOP is the seed for a future skill (per the lifecycle in this doc).
3. **Ask the operator.** If neither (1) nor (2) fits, ask the operator. "I don't have a skill for this. Do you want me to write one, do it inline, or skip it?"

What the agent does NOT do: invent the procedure from training data, present the result as if a skill had run, and skip the SOP capture. The result of "no skill" is **always** one of the three responses above.

### Boundary case: "but the agent has to know *something* to load the skill"

Yes — the agent's cold-start context (Tier 1) tells it the skill manager exists and what skills are loadable by name. That is **infrastructure knowledge**, not domain knowledge. Domain knowledge lives in skills. The line is: if the knowledge would change the agent's behavior on a specific task, it belongs in a skill. If the knowledge is "how to use the skill system," it belongs in cold-start.
