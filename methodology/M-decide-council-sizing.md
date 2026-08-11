---
id: alice-methodology-M-decide-council-sizing
created: 2026-08-07T23:00:00Z
updated: 2026-08-07T23:00:00Z
version: 0.1.0
title: "Methodology M — Decide council size and seats"
type: methodology
status: active
source: alice-framework
confidence: 1.0
tags: [kind:methodology, kind:council, project:alice]
links: ["[[methodology/07-council-methodology.md]]", "[[M-decide-x-article-review-flow.md]]"]
---

# Methodology M — Decide council size and seats

> Choose the council for the question, not the question for a fixed council.

## Changelog

- **0.1.0** (2026-08-07): Added the three-signal council sizing and seat-selection methodology. The new algorithm replaces “pick 3–5” as an underspecified instruction: reversibility sets the risk floor; stakeholder breadth and question dimensions determine which optional lenses are needed. Added the pre-run `Council Seats Selected` ticket contract and verifier checks. Phase 4 of the x-article-review flow now uses the algorithm rather than a fixed five-seat list.

## Method

A council is useful when a decision needs structured disagreement. It is wasteful when the question is trivial, single-dimensional, or cheaply reversible. This method chooses an appropriate breadth without pretending that every project needs the same standing roster.

## Part 1: Read the three signals

Before selecting seats, write a short assessment of:

### 1. Decision reversibility

Ask how expensive it would be to undo the recommendation after it ships.

- **Costly to reverse or high-stakes:** begin with five seats. Examples: a public release, a framework contract, a schema migration, or a change that creates continuing maintenance work.
- **Cheaply reversible:** begin with three seats. Examples: naming, labels, or a small internal wording choice.

“Reversible” means reversible in the real operating context, not merely editable in theory. A one-line change that creates reader confusion or a migration burden is not cheap just because the diff is small.

### 2. Stakeholder breadth

List the distinct groups that will experience the consequence or carry the work. A stakeholder is material when their needs could change the recommendation, not merely because they can read the output.

- One stakeholder group usually needs only the core trio.
- Add a seat when a second group has a distinct decision lens: Product for users/readers, Operator for the person who runs and maintains the system, Customer for an external buyer or commercial market.

### 3. Question dimensions

List the kinds of reasoning that cannot be collapsed without losing information:

- strategy and opportunity cost;
- engineering feasibility and technical debt;
- product/user value;
- operations and maintenance;
- failure modes and rollback;
- customer or commercial fit.

A single-dimensional question can use three seats. A question spanning several dimensions should use four or five, with one seat covering each material dimension.

The signals work together: reversibility establishes the risk floor, while stakeholders and dimensions determine breadth.

## Part 2: The selection algorithm

1. State the precise decision and its boundary.
2. Start with the **core trio**: Strategist, Engineer, Skeptic.
3. Add **Product** for a user-facing surface, experience, public documentation, API, or prioritization decision.
4. Add **Operator** when the operator must run, maintain, repeatedly review, or pay attention to the result. For Alice framework decisions, Operator is mandatory.
5. Add **Customer** when an external buyer, pricing, market, or commercial positioning is material.
6. Check whether any strategy, engineering, product, operations, risk, or commercial dimension remains uncovered. Add the appropriate seat.
7. Cap the council at five. If six seats appear necessary, narrow the question into separate decisions instead of adding reviewers.
8. Record the composition in the ticket body before the council runs.

The count is normally three for a cheap, narrow question; four for a question with one additional material stakeholder or dimension; and five for a costly-to-reverse, user-facing, cross-dimensional, or commercial decision. The algorithm is a decision aid, not a mechanical scorecard: record the reasoning when a boundary case uses a different count.

## Part 3: Seat menu

| Seat | Covers | Typical trigger |
|---|---|---|
| Strategist | Direction, opportunity cost, second-order effects | Core trio |
| Engineer | Feasibility, implementation, technical debt | Core trio |
| Skeptic | Failure modes, assumptions, rollback | Core trio |
| Product | User value, UX, public surface | User-facing impact |
| Operator | Daily runnability, maintenance, operator time | Framework or operator impact |
| Customer | Buyer value, pricing, external positioning | Commercial decision |

The menu is intentionally not a fixed committee. A seat is selected because its lens can change the answer.

## Part 4: Ticket contract

Every council ticket includes this section before work begins:

```markdown
## Council Seats Selected

### Three-signal assessment
- **Reversibility:** <cheaply reversible | costly to reverse>; <reason>
- **Stakeholders:** <affected groups>; <reason>
- **Dimensions:** <material reasoning dimensions>; <reason>

### Seats
- **Strategist** — <specific contribution>.
- **Engineer** — <specific contribution>.
- **Skeptic** — <specific contribution>.
- **[Optional]** — <why this additional lens can change the answer>.

**Total:** <3–5> seats — <why this is proportionate>.
```

This is the audit trail of the council's breadth. It belongs in the ticket before reviewers deliberate, not as a retrospective explanation.

## Part 5: Worked examples

| Decision | Three-signal reading | Composition |
|---|---|---|
| Add a navigation diagram to Alice | Costly in a release; Matthew + readers; strategy/product/operations | Strategist, Engineer, Product, Operator, Skeptic (5) |
| Use “plan” or “spec” in one method title | Cheaply reversible; one operator; wording only | Strategist, Engineer, Skeptic (3) |
| Publish Alice v0.2.0 | Public and costly to unwind; Matthew + public; release/engineering/operations/risk | Strategist, Engineer, Product, Operator, Skeptic (5) |
| Keep or archive a skill | Reversible; Matthew + maintainers; utility/maintenance/risk | Strategist, Engineer, Operator, Skeptic (4) |
| Choose a buyer lane for a new product | Commercial and costly to unwind; operator + buyers; strategy/product/customer/risk | Strategist, Product, Customer, Operator, Skeptic (5) |

## Part 6: Verifier checks

The verifier confirms:

1. The ticket has a `Council Seats Selected` section before the council output.
2. Reversibility, stakeholders, and dimensions are each assessed.
3. The count is between three and five.
4. Every selected seat is named and justified.
5. Alice/framework decisions include Operator.
6. User-facing decisions include Product.
7. Commercial decisions include Customer when the buyer lens is material.
8. The council output contains a structured position for every selected seat.

The verifier does not decide whether the council should have chosen a different seat. It checks that the composition was explicit, proportionate, and consistent with the stated signals.

## Part 7: Relationship to the base council method

This document extends `methodology/07-council-methodology.md`. The base method says when to convene and establishes the canonical four-seat Alice minimum. This method explains how to size and compose a council for a particular request. When the base method and this algorithm appear to conflict, use the stricter constraint: a council-worthy Alice framework decision has at least four seats including Operator; a low-stakes lightweight council may use the Hermes core trio when the decision is cheaply reversible and narrow.

The output is a recommendation, not an automatic implementation. Accepted findings re-enter Alice's spec-first flow; no council recommendation bypasses operator review or verifier gates.
