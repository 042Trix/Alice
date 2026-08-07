---
id: alice-reference-skill-curator
created: 2026-08-06T00:00:00Z
title: "Skill curator — maintenance cycle and report"
type: reference
status: draft
source: alice-framework
tags: [kind:reference, kind:skill-maintenance, project:alice]
confidence: 0.8
links: [methodology/02-decide-skills.md, templates/skill-brief.md.template]
---

# Skill curator — maintenance cycle and report

> Use this reference when reviewing a skill library for age, trigger ambiguity, overlap, size, version drift, or deprecation. The curator reports and routes maintenance work; the curator does not silently rewrite domain procedures.

## Purpose

A skill library needs a steward after skills ship. Without one, trigger boundaries blur, procedures duplicate, references accumulate, and obsolete versions remain selectable. The skill curator turns those risks into a repeatable review queue.

The role is tool-agnostic. It may be implemented by a scheduled inventory scan, a dedicated agent, a human reviewer, or a combination. The operator maps these responsibilities to mechanisms available in their platform.

## What the curator does

The curator owns catalog quality and review evidence. The curator:

1. Inventories active, deprecated, and archived skills.
2. Records or reads each skill's version, owner, last use, and next review date.
3. Applies the four-condition maintenance test:
   - used or intentionally retained within the review window;
   - trigger conditions remain specific;
   - procedure does not materially overlap another skill;
   - procedure and supporting material remain bounded.
4. Counts `SKILL.md` lines, procedure lines, and linked references.
5. Generates likely overlap pairs and classifies them for human review.
6. Finds broken links, stale aliases, missing briefs, and replacement pointers.
7. Proposes `keep`, `revise`, `merge`, `migrate`, or `deprecate`.
8. Records the authorized disposition and next review date.

The methodology defaults are a 90-day disposition review, `SKILL.md` at 200 lines or fewer, a procedure at 100 lines or fewer, and no more than five references. Exceptions require a recorded rationale and review date.

## How the curator operates

### Routine scan

Run an inventory scan daily or weekly when automation is available. The scan is observational: it gathers facts and flags candidates. It does not retire skills or rewrite procedures.

Collect at least:

- Skill name, path, status, and canonical version.
- Owner or reviewing role.
- `last_used_on` plus its evidence source.
- Last review and next review dates.
- Main-file and procedure line counts.
- Number of linked references.
- Trigger terms, exclusions, outcome, and major procedure steps.
- Broken links and missing replacement targets.

A runtime or dispatcher may update `last_used_on` when a skill is invoked. If the platform does not expose invocation telemetry, record usage as `unknown`; do not infer use from file modification time.

### Candidate generation

Flag a skill for review when any of these is true:

- Its review date is due.
- No reliable invocation appears in the default 90-day window.
- Its trigger has no explicit exclusions.
- It exceeds a size budget.
- Another skill shares its trigger, outcome, or major steps.
- A linked reference or replacement target is broken.
- A failure, correction, or platform change may have invalidated the procedure.

Age is a review trigger, not an automatic deprecation decision. Rare, seasonal, safety, and recovery skills may be retained with evidence and a stated next review.

### Human or owner review

A human or designated owner decides irreversible catalog changes, including retirement and incompatible migration. The reviewer checks the curator's evidence, confirms the canonical skill, and authorizes the disposition.

Substantive edits go to the skill author or documentation owner. A verifier then reruns trigger-routing and execution tests. This division prevents the catalog steward from changing domain behavior merely to satisfy a metric.

## The overlap review

For every candidate pair, compare:

1. **Triggers:** could the same real request load both skills?
2. **Outputs:** do they promise the same result?
3. **Procedures:** do they repeat authoritative steps or only share a tool?
4. **Verification:** do they prove success in the same way?
5. **Ownership:** which skill has clearer triggers, stronger evidence, and lower load cost?

Classify trigger overlap as `none`, `partial`, or `full`, and procedure overlap as `low`, `material`, or `near-total`.

- `none + low`: keep both.
- `partial`: refine boundaries, define precedence, or extract the shared procedure.
- `full + near-total`: select one canonical skill and propose deprecation of the duplicate.
- Distinct outcomes despite shared tools: keep separate and make exclusions explicit.

Verify the resulting boundary against three real requests: a positive case, a negative case, and a formerly ambiguous case.

## Maintenance report template

Produce one report per reviewed skill. A catalog-level report may link these records and summarize counts, but should not replace the evidence per skill.

```markdown
# Skill maintenance report — <skill-name>

- Review date: <YYYY-MM-DD>
- Reviewer: <role or owner>
- Skill version: <version>
- Status: <active | deprecated | archived>
- Last used: <date | unknown>
- Usage evidence: <task, invocation record, or none>
- Next review: <YYYY-MM-DD>

## Four-condition test

| Condition | Verdict | Evidence | Proposed action |
|---|---|---|---|
| Recent use | pass/review/unknown | ... | ... |
| Specific triggers | pass/revise | ... | ... |
| Non-overlapping body | pass/merge | ... | ... |
| Bounded procedure | pass/trim/split | ... | ... |

## Budget

- SKILL.md lines: <N> / 200
- Procedure lines: <N> / 100
- References: <N> / 5
- Exception: <none or rationale + owner + review date>

## Overlap candidates

- <other-skill>: trigger=<none/partial/full>; procedure=<low/material/near-total>; notes=<...>

## Disposition

- Curator proposal: <keep | revise | merge | migrate | deprecate>
- Owner decision: <approved | rejected | deferred>
- Canonical or replacement skill: <name | none>
- Required work owner: <role>
- Verification evidence: <three-context test, link check, or other>
```

## Deprecation and migration handoff

When deprecation is approved, the curator records:

- Evidence-based reason.
- Replacement skill or explicit `none`.
- Inbound links and assignments that must change.
- Archive location and preserved version.
- Cutover date and rollback condition.

When a major version is approved, preserve the prior brief and procedure as read-only audit material, validate the new contract, then designate one canonical version. Two versions must not remain active under identical triggers.

## What the curator does not do

The curator does not:

- Rewrite domain procedures without routing the work to their owner.
- Deprecate a skill solely because 90 days elapsed.
- Delete historical versions, briefs, or evidence.
- Treat similarity scores or line counts as verdicts.
- Invent usage dates when telemetry is missing.
- Change runtime configuration while performing a documentation audit.
- Make two overlapping versions simultaneously canonical.

Automation may surface and summarize. Review authority decides. The procedure owner edits. A verifier checks the result.

## Suggested role split

| Role | Responsibility |
|---|---|
| Scheduled scan | Inventory, counts, link checks, due-date flags, candidate pairs |
| Skill curator | Evidence review, classification, recommendation, report |
| Human or designated owner | Retirement and incompatible-migration approval |
| Skill author or documentation owner | Substantive rewrite, trim, merge, or split |
| Verifier | Trigger routing, three-context execution, link and budget checks |

A small installation may combine all five responsibilities in one person, but should preserve the sequence: collect evidence, review, decide, edit, verify.

## See also

- `methodology/02-decide-skills.md` — full creation and maintenance methodology.
- `templates/skill-brief.md.template` — design contract and maintenance-readiness checks.
- `methodology/01a-decide-memory.md` — skills as procedural memory and session-level budget.
- `methodology/03-decide-agents.md` — functional roles and when to combine them.
