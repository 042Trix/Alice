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

## Skill maintenance

Shipping v1 begins the maintenance cycle; it does not end the skill lifecycle. A healthy library stays small enough to inspect, specific enough to route reliably, and current enough to trust. Review every skill at least once every 90 days, and review it sooner after a failure, repeated operator correction, major tool change, or discovery of an overlapping skill.

Maintenance is evidence-led. Usage age is a prompt for review, not automatic proof that a skill is worthless. Likewise, a large file is a prompt to trim or split, not permission to discard working knowledge. Preserve history, name the disposition, and keep a clear route from an old skill to its replacement.

### 1. The maintenance test: a four-condition cycle

At each review, test four conditions in order:

1. **Recent use.** Was the skill invoked successfully within the last 90 days?
2. **Specific triggers.** Can an agent still distinguish this skill from neighboring skills without guessing?
3. **Non-overlapping body.** Does this skill own a distinct procedure rather than duplicate another skill's steps?
4. **Bounded procedure.** Is the procedure still small, testable, and economical enough to load and execute as one skill?

Record one verdict per condition: `pass`, `revise`, `merge`, `deprecate`, or `needs evidence`. Do not collapse the four answers into a vague overall score; the condition identifies the corrective action.

#### Condition 1 — recent use

Use invocation records, task logs, or another durable usage record. Record both `last_used_on` and the evidence source. If the skill was used in the last 90 days, continue to the other conditions. Recent use does not excuse overlap or bloat.

If there is no use in 90 days, place the skill in **deprecation review**. Do not retire it solely because the date is old. Check whether:

- The triggering task is seasonal or rare but high consequence.
- The skill is a required fallback or recovery procedure.
- Usage telemetry is incomplete.
- A renamed or replacement skill absorbed the traffic.
- The underlying capability is no longer needed.

A skill may remain active after the cutoff when the reviewer records a concrete reason and a next review date. If the capability is obsolete or replaced, use the deprecation pattern below.

#### Condition 2 — specific triggers

Read the positive and negative trigger conditions as if you were a cold-start agent. The triggers pass when the agent can answer all three questions without inspecting the body:

- What exact event or input loads this skill?
- Which similar event does not load it?
- When two candidates appear, which skill wins?

Refine triggers that rely on broad topics such as “when working with files” or “when research is needed.” Prefer observable events, input types, named outcomes, and explicit exclusions. Re-run the three-context test after changing triggers.

#### Condition 3 — non-overlapping body

Compare the skill with every skill sharing a trigger term, outcome, tool family, or procedure step. A distinct trigger with a copied body is still overlap. A shared tool with different outcomes is not necessarily overlap.

The body passes when it owns a coherent outcome and does not repeat another skill's authoritative procedure. If two skills must share a rule, put the rule in one canonical reference and have both skills point to it rather than maintaining two copies.

#### Condition 4 — bounded procedure

A bounded skill has one primary outcome, a procedure of at most 100 lines, no more than five references, and a total `SKILL.md` of at most 200 lines. Its execution cost and stop condition remain explicit.

Review actual execution evidence, not just file length. Warning signs include:

- The skill routinely chooses among many unrelated workflows.
- Its procedure exceeds the estimated token or tool-call cost.
- More exceptions exist than core steps.
- Most of the file is examples, vendor documentation, or background.
- A change to one branch forces unrelated branches to be retested.

When the condition fails, trim, extract references, or split by distinct trigger and outcome. Do not hide complexity by shrinking prose until the procedure becomes ambiguous.

#### Maintenance decision table

| Review result | Action | Verification |
|---|---|---|
| All four pass | Keep active | Record review date and next review |
| Usage unclear | Gather evidence | Confirm telemetry before disposition |
| Trigger fails | Refine triggers | Re-run three real contexts |
| Body overlaps | Merge or supersede | Confirm one canonical procedure remains |
| Procedure unbounded | Trim, refactor, or split | Recount and retest each resulting skill |
| Capability obsolete | Deprecate | Confirm replacement or explicit no-replacement notice |

### 2. The size budget

Use these default limits:

- `SKILL.md`: **200 lines or fewer**.
- Procedure section: **100 lines or fewer**.
- Linked reference documents: **5 or fewer**.
- Primary outcome: **one**.

The limits are review gates, not formatting games. Blank-line removal, compressed prose, or moving every sentence into references does not make an oversized skill healthy.

Count references that the agent may need to load to execute the procedure. Assets, scripts, and templates are not prose references, but each still needs a clear call site and purpose. An unused supporting file is maintenance debt.

When a skill exceeds a limit, choose the smallest sound correction:

1. Delete obsolete or duplicated material.
2. Move detailed examples or vendor notes to a relevant reference.
3. Replace duplicated rules with a link to one canonical source.
4. Split only when the resulting skills have independent triggers and outcomes.
5. Keep an exception only when the reviewer records why splitting would reduce reliability.

A size exception must include an owner, rationale, and review date. “It is complicated” is not a rationale; state what would break if the material were separated.

### 3. The overlap detection algorithm

Run overlap detection across the whole catalog, then inspect likely pairs. A simple repeatable algorithm is better than relying on a curator's memory.

#### Step 1 — build a comparison card

For each active skill, extract:

- Skill name and declared outcome.
- Positive trigger phrases.
- Negative trigger phrases.
- Required inputs and output type.
- Procedure headings or major verbs.
- Tools and references used.

Normalize spelling and aliases, but preserve meaningful distinctions. “Review a pull request” and “request a review” share words while producing different outcomes.

#### Step 2 — generate candidate pairs

Compare skills that share at least one of:

- A trigger noun or event.
- The same promised output.
- Two or more major procedure steps.
- The same authoritative reference.
- A name, alias, or deprecated name.

This candidate pass narrows the review; it does not decide the disposition.

#### Step 3 — classify trigger overlap

Ask whether the same real request could validly load both skills:

- **No overlap:** requests are distinguishable from the trigger text. Keep both.
- **Partial overlap:** some requests load both, while each also has a distinct region. Refine boundaries, merge shared procedure, or designate precedence.
- **Full overlap:** every valid trigger for one skill also triggers the other. Treat the narrower skill as a specialization only if it owns a distinct outcome; otherwise it is a duplicate.

#### Step 4 — classify procedure overlap

Estimate whether the skills perform the same work:

- **Low:** shared tool or setup, but different decisions and outputs.
- **Material:** repeated authoritative steps or checks; extract or merge the shared procedure.
- **Near-total:** same inputs, steps, and output. Select one canonical skill and deprecate the duplicate.

#### Step 5 — choose the canonical owner

When merging or superseding, prefer the skill with:

1. Clearer and narrower trigger conditions.
2. Better verification evidence.
3. More recent successful use.
4. Simpler procedure and lower load cost.
5. A name that describes the outcome rather than the tool.

Do not automatically archive the smaller file. Size alone does not establish quality. Record why the survivor is canonical and map aliases from the retired skill.

#### Step 6 — verify the boundary

Test the resulting catalog against at least three requests: one that should load the survivor, one that should not, and one formerly ambiguous request. The maintenance change is incomplete until routing is unambiguous.

### 4. The deprecation pattern

Deprecation preserves audit history while removing a skill from normal selection. It is not deletion and not a way to hide neglected work.

Use this sequence:

1. Confirm the skill is obsolete, duplicated, unsafe, or replaced.
2. Identify `replaced_by`, or explicitly record `none` and why.
3. Update every inbound link, catalog entry, and agent assignment.
4. Move the skill to an archive location such as `skills/_archived_<YYYY-MM-DD>/<skill-name>/`.
5. Preserve the original `SKILL.md` in version history or an audit copy.
6. Replace the active-facing body with a short deprecation notice.
7. Verify the deprecated skill is no longer selected by default.

Recommended deprecation metadata:

```yaml
status: deprecated
deprecated_on: <YYYY-MM-DD>
replaced_by: <skill-name-or-none>
reason: <one-sentence evidence-based reason>
last_active_version: <version>
```

The notice should say what changed, which skill to load instead, and where the historical procedure can be inspected. If no replacement exists, say whether callers should perform a one-shot procedure, escalate, or stop.

Never delete a skill merely because it has not been used recently. Never mark a still-required skill deprecated simply to reduce catalog size. The curator proposes disposition; a human or designated owner confirms retirement at the review cutoff.

### 5. The version migration pattern

Use a new major version when triggers, outputs, safety boundaries, or core procedure change incompatibly. Small clarifications and corrected examples can remain within the current version with a dated change note.

For a major migration:

1. Freeze the prior brief and procedure as read-only audit material.
2. Write a new skill brief describing the changed contract.
3. Validate the new version against three real contexts, including one prior-version case.
4. Make the new version canonical only after validation passes.
5. Update catalogs, agent assignments, links, and aliases.
6. Monitor initial invocations for regressions.
7. Deprecate the prior version after the defined compatibility window.

A portable directory pattern is:

```text
skills/<name>/SKILL.md          # current canonical version
skills/<name>/v1/SKILL.md       # read-only prior version
skills/<name>/v1/brief.md       # preserved prior contract
skills/<name>/brief.md          # current contract
```

If the host tool cannot store nested versions, preserve the same information in version control or an archive directory. The method matters more than the path: one canonical version, preserved prior contracts, and an explicit migration record.

Agents load the canonical version unless a task explicitly requires an older contract. Do not keep two active versions with identical triggers; that creates nondeterministic routing.

A migration record should name the old and new versions, incompatible changes, validation evidence, cutover date, rollback condition, and owner. Roll back when the new version fails its verification contract; do not silently edit v1 to resemble v2.

### 6. The trim, refactor, and split pattern

Start with evidence from the last 90 days of invocations, failures, and corrections. Mark each section as `used`, `supporting`, `duplicated`, `obsolete`, or `unknown`.

#### Trim

Trim material that is obsolete, repeated, or non-procedural:

- Remove steps for tools or interfaces no longer supported.
- Remove examples that repeat an existing pattern without adding an edge case.
- Collapse repeated cautions into one rule at the point of action.
- Replace copied external documentation with a concise instruction and source link.
- Remove supporting files that have no call site.

Do not remove a rare safety step because it lacks recent use. Safety and recovery procedures are evaluated by consequence and test evidence, not frequency alone.

#### Refactor

Refactor when the outcome remains one skill but the explanation is poorly organized:

- Put trigger and exclusions first.
- Keep the executable procedure linear.
- Move rationale after the action it explains.
- Move long examples, schemas, and vendor-specific details into references.
- Keep verification and stop conditions in the main file.

References support execution; they must not become a dumping ground. Each reference needs a “read when” condition, and the skill must still be understandable without loading all five.

#### Split

Split only when the material contains two or more independently triggerable outcomes. Each child skill must have:

- Its own positive and negative triggers.
- A distinct output and stop condition.
- A bounded procedure.
- Independent verification.
- A migration note from the parent skill.

If two sections always load together and cannot be verified separately, keep them in one skill and refactor instead. The no-nesting rule still applies: resulting skills reference one another by name; neither contains a hidden sub-skill.

After any trim, refactor, or split, rerun the three-context test, recount lines and references, check inbound links, and compare the new procedure against the prior verification contract.

### 7. The audit cadence

Use two cadences:

- **Routine inventory scan:** daily or weekly, automated when the platform supports it.
- **Disposition review:** every 90 days, with a human or designated owner approving merges, migrations, and deprecations.

The routine scan should collect, not decide. It may:

- Inventory active, deprecated, and archived skills.
- Read `last_used_on` and its evidence source.
- Flag missing briefs, owners, verification, or trigger exclusions.
- Count `SKILL.md` lines and linked references.
- Generate candidate overlap pairs.
- Detect broken links and stale replacement pointers.
- Produce a queue of skills due for review.

Update `last_used_on` when the skill is actually invoked, preferably from dispatcher or runtime telemetry. A file edit is not a use. A catalog listing is not a use. When telemetry is unavailable, record `unknown` rather than inventing a date.

The 90-day review applies the four-condition test and records a disposition. High-risk skills may use a shorter cadence. Seasonal skills may use a longer review window when the owner records the reason.

A maintenance report should include:

| Field | Purpose |
|---|---|
| Skill and version | Identifies the reviewed contract |
| Owner | Names who confirms disposition |
| Last used + evidence | Separates fact from assumption |
| Four condition verdicts | Makes the corrective action explicit |
| Size and reference counts | Shows budget status |
| Overlap candidates | Shows catalog boundary risk |
| Proposed disposition | Keep, revise, merge, migrate, or deprecate |
| Reviewer decision | Records the authorized outcome |
| Next review date | Closes the cycle |

Automation may flag and report. It should not rewrite procedures or retire skills without the review authority defined by the operator.

### 8. The curator role

The **skill curator** owns library quality, not the domain procedure inside every skill. The role may be performed by an automated scheduled scan, a dedicated agent, a human operator, or a combination.

The curator:

- Maintains the catalog and review schedule.
- Runs the four-condition maintenance test.
- Enforces size and reference budgets.
- Detects overlap and proposes canonical ownership.
- Produces migration and deprecation recommendations.
- Verifies links, aliases, versions, and review evidence.
- Routes substantive rewrites to the skill's author or documentation owner.
- Records the accepted disposition and next review date.

The curator does **not**:

- Rewrite a domain procedure merely to make a report green.
- Decide that an unused capability is unnecessary without owner review.
- Delete historical versions or evidence.
- Treat age, file size, or similarity score as automatic disposition.
- Change the operator's environment as part of a methodology audit.

A practical division of labor is:

1. Automation performs frequent inventory, counting, link checks, and candidate generation.
2. The curator reviews evidence and proposes action.
3. A human or designated owner approves retirement and incompatible migration.
4. The skill author or documentation owner performs substantive edits.
5. A verifier reruns routing and execution tests.

See `references/skill-curator.md` for the audit-cycle reference and report format.

### Maintenance parity check

This section defines a friend-portable method, not a claim that every platform supplies usage telemetry, scheduled scans, archives, or version routing. Before adopting it, map each function—invocation evidence, inventory scan, review approval, archive storage, and canonical-version selection—to mechanisms available in your own tool.

The size limits and 90-day cycle are methodology defaults. Adjust them when measured execution cost, risk, or seasonality provides better evidence, but record the exception so the catalog remains auditable.

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
- `methodology/05-op-guards.md` — the constraints that prevent skills from overreaching
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
