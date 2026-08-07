# Methodology 07 — Council methodology

> The seventh decision: when and how to run a multi-perspective council review. Councils are the system's deliberation mechanism — for decisions too important for a single agent's judgment.

## What a council is (in Alice's terms)

A **council** is a multi-seat deliberation on a single question or proposal. Each seat has a perspective. The seats deliberate independently and produce a verdict.

Councils are **deliberate** (as opposed to automatic). They're invoked for decisions that:
- Have multiple legitimate perspectives
- Affect more than one part of the system
- Have irreversible consequences if wrong
- Have observed failure patterns that need a structured review

## When to convene a council

Convene a council when **at least two** of these are true:

1. **Multi-perspective** — at least 2 legitimate seats have different views
2. **Cross-cutting** — affects 2+ domains (e.g., skill + agent + cron)
3. **Irreversible** — the action is hard to reverse (e.g., delete a rule, change a domain)
4. **Pattern observed** — there's a recent failure pattern this decision could address

If only one is true, the decision is a single-agent or operator call. Not a council.

## The 4-seat minimum

A council has **4 seats minimum**. The 4 canonical seats are:

1. **Strategist** — long-term view, system-level, "is this the right thing to build?"
2. **Engineer** — implementation view, "is this buildable, what's the cost, what's the failure mode?"
3. **Operator** — user/operator view, "does this fit the operator's workflow, is it sustainable?"
4. **Skeptic** — failure-mode view, "what could go wrong, what's the rollback plan?"

You can have more seats (5-6 is fine), but 4 is the minimum. Fewer than 4 is **decision-by-fiat**, not a council.

## The 3-step process

```
1. Frame  →  2. Deliberate  →  3. Verdict
```

**1. Frame.** Define the question precisely. "Should we build X?" is too broad. "Given the failure pattern of [Y] observed 3+ times in [Z] context, should we build [X] as a skill, write an operational guard, or change the agent's soul?" is precise.

**2. Deliberate.** Each seat produces a position (1-2 paragraphs). The seats don't talk to each other. The orchestrator (operator or a designated chair) reads all positions and surfaces agreements and disagreements.

**3. Verdict.** The orchestrator produces a final verdict, with the seat positions cited. The verdict can be:
- **BUILD / SHIP** — proceed with the proposed action
- **VALIDATE-FIRST** — proceed with a constrained experiment before full build
- **PARK** — defer, revisit when conditions change
- **KILL** — do not build; document the kill reason

The verdict is logged to a single markdown file. The file name is `YYYY-MM-DD_council-<topic>.md`.

## When NOT to convene a council

- **For one-off decisions.** "Should I run cron X at midnight or 1am?" is a one-off, not a council question.
- **For skill-brief validation.** The skill-brief is the spec; the council reviews the spec, not the spec-approval.
- **For trivial rules.** Operational guards come from observed failures, not from council deliberation.
- **For personal preferences.** "Should the DM header say 'X' or 'Y'?" is operator preference, not a council question.

## Council cadence

Don't run councils for every decision. Run them:
- **Once per skill-brief**, before promoting to a skill (validate the spec)
- **Once per major system change** (e.g., new focus vertical, new operational guard)
- **Once per failure pattern** that doesn't fit existing methodology
- **Never** for one-off decisions or operator preferences

If you're convening a council every day, you're over-deliberating. The system should be quiet most days.

## Council-seat template

Use `templates/council-seat.md.template`. The seat has 5 sections:

1. **Identity** — name, role, lens
2. **Inputs** — what does the seat read before deliberating?
3. **Outputs** — what does the seat produce?
4. **Boundaries** — what does the seat NOT consider?
5. **Disagreement protocol** — when does the seat escalate?

If any section is empty, the seat isn't ready.

## Council output format

The council produces a single markdown file with this structure:

```markdown
# Council verdict — YYYY-MM-DD

## Question
[precise question]

## Seat positions
### Strategist
[1-2 paragraphs]

### Engineer
[1-2 paragraphs]

### Operator
[1-2 paragraphs]

### Skeptic
[1-2 paragraphs]

## Agreements
- [list of things all seats agree on]

## Disagreements
- [list of things seats disagree on, with the dissent position cited]

## Verdict
[final verdict: BUILD / VALIDATE-FIRST / PARK / KILL]

## Sources
[relevant tickets, docs, prior councils]
```

The file is the **single source of truth** for the council decision. Don't spread council output across multiple files.

## Council anti-patterns

1. **"I'll convene a council for every decision."** → No. Most decisions are not council-worthy. Use the "when to convene" criteria.
2. **"The council will resolve the disagreement."** → No. The council exposes the disagreement. The orchestrator decides.
3. **"The seats should debate."** → No. Seats deliberate independently. The orchestrator synthesizes.
4. **"The verdict should be 50/50 between seats."** → No. Verdicts are not votes. The orchestrator decides.
5. **"The council should be a single agent with 4 prompts."** → That's a multi-prompt worker, not a council. A council is multiple agents, each with their own soul.
6. **"The council should run for every minor change."** → No. Run councils for major decisions, not minor ones.

## Worked example (skeleton)

See `worked-examples/01-solo-founder-skeleton/methodology-notes/07-council.md` when v0.0 ships for an anonymized example.

## Maintenance

The council is heavyweight. Over time it drifts: convening too often (rubber-stamping), too rarely (decisions made without deliberation), or producing verdicts that nobody routes. This section is the audit surface that catches the drift.

### 1. Audit cadence

Every **6 months**. A twice-yearly check is enough; align it with the operator's quarterly review or half-year boundary.

### 2. Quality threshold

The council pattern is **healthy** when all hold:

- **Seat count** — **3-5 seats** (not 1, not 7; 4 canonical, 5 acceptable as tiebreaker)
- **Documented question** — every council has a precise question on the record
- **Recorded output** — every council produces a markdown log on disk (`YYYY-MM-DD_council-<topic>.md`); unrecorded = conversations, not councils
- **Operator review** — operator reviews the log within 7 days

### 3. Drift signals

The pattern is **drifting** when any of these appear:

- **Convening too often** — daily/weekly; sessions feel like rubber-stamping; verdicts converge on BUILD without real disagreement
- **Convening too rarely** — council-worthy decisions are made by a single agent or operator alone
- **Verdicts not routed** — orchestrator files child tickets pre-emptively, before the verdict lands; verdicts become post-hoc ratification

### 4. Fix actions

When drift is detected:

1. File a `council-effectiveness-review` ticket — assignee is the **council profile itself**
2. Review reads the last 6 months of council logs and counts which drift signals fired
3. **The fix is a methodology patch, not a council verdict.** Drift in the *council mechanism* is fixed by editing this file; drift in a *single decision* is fixed by re-running that council

### 5. Retirement conditions

Retire when **any** hold:

- **No councils in 60 days** — and operator + default are making decisions comfortably without deliberation
- **A better deliberation mechanism exists** — operator has adopted a structured retrospective, smaller advisory panel, etc.
- **Operator explicitly retires it** — council retirement is an operator call, not a council verdict

Retirement ≠ deletion. Stop convening new councils, archive existing logs as historical record, document the retirement in this methodology's changelog.

### Maintenance parity check

This section defines a friend-portable method, not a claim that every platform supplies council-log archival, seat-count enforcement, or operator-review queues. Before adopting it, map each function — seat inventory, question documentation, log archival, operator-review routing — to mechanisms available in your own tool. The 6-month cycle and the four quality conditions are methodology defaults; adjust them when measured council activity, drift rate, or operator-stated cadence provides better evidence, but record the exception so the council methodology remains auditable.

## See also

- `methodology/01a-decide-memory.md` — **council verdicts are episodic memory (Tier 4).** Each verdict is written under the **atomic tier** in a durable `cross-agent/` subdirectory (e.g., `<atomic-tier>/cross-agent/YYYY-MM-DD_council-<topic>.md` per the naming chosen in `methodology/01-decide-vault-tier.md`); verdicts are versioned but never deleted. Council verdicts are the system's "this is what we decided" trail.
- `methodology/03b-decide-operator-agent-interaction.md` — council verdicts are operator-reviewable (the approver role). The operator reviews the verdict, the agent acts on the verdict. The operator-agent doc covers the approver role.

- `methodology/08-inbox-route.md` — how external inputs become council questions (or skip the council)
- `methodology/09-inbox-from-external-sources.md` — X / articles / podcasts as a special case

## Anti-patterns to watch for

1. **"I have a council for every major decision."** → Probably you have too many councils. Most decisions are not council-worthy.
2. **"The council is me, the operator, in 4 voices."** → No. The operator is one seat. The other 3 seats are agent profiles.
3. **"The verdict is always BUILD."** → No. The verdict is what's right, not what's most common. If 4 seats say KILL, the verdict is KILL.
4. **"The council should run daily."** → No. Run councils when warranted, not on a schedule.
