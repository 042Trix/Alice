---
id: alice-methodology-M-decide-v0.1.1-defer-set
created: 2026-08-06T15:30:00Z
updated: 2026-08-06T15:30:00Z
title: "Methodology M — v0.1.1 defer-set (per council review)"
type: methodology
status: draft
source: hermes-council/t_b846d4cc
tags: [kind:methodology, kind:meta, kind:defer, project:alice, version:v0.1.1]
confidence: 0.83
links: ["[[methodology/M-decide-spec-first-flow.md]]", "[[methodology/M-decide-parity-check.md]]", "[[methodology/01-positioning.md]]"]
promoted_from: _inbox/M-decide-v0.1.1-defer-set.md
---

# Methodology M — v0.1.1 defer-set (per council review)

> Alice is the methodology. Three things surfaced by the v0.1.0 GBU review are explicitly **not** in scope for v0.1.1, because adding them would push Alice out of the methodology lane and into the operator-instance lane. The doc-writer reads this as a guardrail before filing any v0.1.1 ticket; the verifier reads this as a stop-condition before accepting any v0.1.1 ticket.

## Why this guardrail exists

The v0.1.0 → v0.1.1 council review (hermes-council task `t_b846d4cc`, confidence 0.83) converged on a focused 5–7 ticket doc-only release (P1–P5 + optional P6). The council also flagged three patterns that would feel natural to add but must not be added in v0.1.1, because they each violate one of Alice's load-bearing constraints:

1. Adding tooling to Alice violates **method-not-instance** (Alice describes the method, not the operator's working environment).
2. Adding a `company-agent-standards/` scaffold to Alice violates **method-not-instance** plus conflates Alice with the operator's separate instance.
3. Trying to lift testability / enterprise-governance scores in v0.1.1 violates Alice's **positioning** (methodology, not enterprise standards framework) and would damage the 8.5/10 methodology score to chase a 4.5/10 governance score.

This document captures the three deferrals as guardrails the doc-writer and verifier can apply in the same turn.

## The three deferrals

### 1. Migration tooling, drift-detection automation, schema validators

**Deferred from:** v0.1.1 (the operator-instance lane; if implemented at all, lives in `~/.hermes/`, not in Alice).

**Why deferred:** Pushes Alice out of the documentation-only lane. A drift detector or schema validator must run against a real environment, and Alice does not own the operator's environment.

**Guardrail for v0.1.1 tickets:** A ticket body that asks for a script, a CI workflow, a validator, or any executable that reads-or-writes Alice files in a non-textual way is misrouted. The doc-writer must either drop the executable from the body or refuse the ticket.

**Where it goes instead:** A separate ticket on a non-alice-framework board (operator-instance repo design), tracked outside Alice.

### 2. A `company-agent-standards/` companion scaffold inside Alice

**Deferred from:** v0.1.1. Treated as **never in Alice** unless the operator explicitly re-positions Alice as an enterprise standards framework in v0.2.0+.

**Why deferred:** The companion repo is the operator's instance, not Alice's. Embedding it in Alice makes Alice look like a standards framework — exactly the positioning Alice v0.1.1 is trying to escape.

**Guardrail for v0.1.1 tickets:** A ticket body that adds a `company-agent-standards/` folder, a `standards/` subfolder, or any sibling scaffolding inside `methodology/`, `references/`, or `templates/` is misrouted. The doc-writer must remove the scaffolding and confine the ticket to a single methodology file edit.

**Where it goes instead:** A separate operator-instance repository (the GBU's `company-agent-standards/` folder structure). Track as a separate operator-instance decision, not as an Alice ticket.

### 3. Testability improvements (5/10 → higher) and enterprise governance (4.5/10 → higher)

**Deferred from:** v0.1.1. Treated as out-of-scope-by-design unless Alice is re-positioned in v0.2.0+.

**Why deferred:** These are category mismatches with Alice's positioning as a methodology. Lifting them in v0.1.1 will damage the 8.5/10 methodology score (because the methodology gets contorted to support governance), and Alice's structure has no surface to attach governance hooks to without becoming an operator instance.

**Guardrail for v0.1.1 tickets:** A ticket body that proposes adding governance fields, role matrices, RACI tables, compliance mappings, audit hooks, automated conformance tests, or "test score improvements" is misrouted. The doc-writer must remove the additions and confine the ticket to documentation text.

**Where it goes instead:** If Alice is ever re-positioned as an enterprise standards framework (a v0.2.0+ decision), lift these scores by adding a `governance/` methodology layer. Until then, acknowledge the gap honestly in `README.md` §1 as a one-paragraph "What Alice cannot verify" note (the optional P6 in the council log).

## How to apply this guardrail

When filing a v0.1.1 ticket on the `alice-framework` board:

1. **Before writing the ticket body**, the doc-writer scans the body for these three categories (executable, sibling scaffolding, governance/testability lifts). If any appear, the doc-writer strips them and re-scopes the ticket to a pure doc edit.
2. **In the ticket body**, the doc-writer adds a `defer-set check:` field with one of three values:
   - `clear` — the ticket proposes no tooling, no scaffolding, no testability/governance lifts.
   - `defer-aware` — the ticket proposes something adjacent to one of the three deferrals but explicitly routes the executable to the operator-instance lane.
   - `out-of-scope` — the ticket proposes something that falls inside one of the three deferrals; the doc-writer must block the ticket and refile on the appropriate non-alice board.
3. **When accepting the ticket**, the verifier reads the `defer-set check:` field and either LGTMs (`clear`) or requests refactoring (`defer-aware` → confirm routing) or blocks (`out-of-scope` → refile elsewhere).

When reviewing a v0.1.1 ticket mid-execution:

- If the doc-writer's `kanban_comment` or `kanban_complete.metadata.changed_files` shows a non-text artifact (executable script, YAML schema, CI workflow), the verifier flags this as a defer-set violation per this guardrail and the ticket returns to `todo` for refactor.

## Stop condition

This guardrail holds until any one of:

- Alice is re-positioned as an enterprise standards framework in v0.2.0+ (then deferral #3 unlocks).
- The operator publishes a separate operator-instance repo and asks Alice to defer anything else that becomes part of that repo (deferrals #1 and #2 may expand).
- The v0.1.0 retro is itself superseded by a v0.1.1 retro that re-runs the council review with different inputs (then this guardrail is re-evaluated).

Until one of these triggers, the doc-writer reads this as a binding constraint on every v0.1.1 ticket.

## Cross-references

- `~/.hermes/kanban/boards/alice-framework/workspaces/council_alice_v0_1_1.md` (the canonical council log; lines 404–419 are the "Top 3 things to defer" section this guardrail codifies).
- `2-ATOMIC/cross-agent/2026-08-06_council-alice-v0-1-1-improvement-plan.md` (the durable cross-agent note for the same council review; this guardrail is its defer-set half).
- `[[methodology/M-decide-parity-check.md]]` (the parity-check rule that already constrains new tickets; defer-set is the sibling rule that constrains the v0.1.1 cycle specifically).
- `[[methodology/M-decide-spec-first-flow.md]]` (the spec-first rule that already constrains doc-vs-environment mutations; defer-set is the sibling rule that constrains doc-vs-instance additions).
- `_inbox/06b-decide-blocked-ticket-recovery.md` (the blocked-ticket rule that constrains how stranded tickets are revived; defer-set violations surface via that path).
- `methodology/01-positioning.md` (the methodology file P1 will edit; defer-set #2 ensures no `company-agent-standards/` scaffold is added alongside).
- `t_b846d4cc` (the council task; source of the 3 deferrals).
- `t_f1cfcb48` (the META task that produced this guardrail).
