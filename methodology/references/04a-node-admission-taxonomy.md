---
id: alice-methodology-references-04a-node-admission-taxonomy
created: 2026-08-09T18:30:00Z
updated: 2026-08-09T18:30:00Z
title: "Reference — Node-admission signal taxonomy (deeper than the 3-question headline)"
type: methodology-reference
status: draft
source: alice-framework
version: 0.1.0
parent: "[[methodology/04a-decide-work-graph.md]]"
amended_by: ["[[ticket:t_78936c6e]]"]
tags: [kind:methodology-reference, kind:work-graph, kind:node-admission, project:alice]
confidence: 0.0
links: ["[[methodology/04a-decide-work-graph.md]]", "[[methodology/04c-decide-master-ticket.md]]", "[[methodology/04d-decide-flow-spec.md]]"]
---

# Reference — Node-admission signal taxonomy

> **This is a reference, not the headline.** The headline node-admission test in `methodology/04a-decide-work-graph.md` Part 3 is the **3-question** test: differentiated job, evidence the parent cannot own it, collapse condition. Apply the 3-question test first. Open this reference only when the 3-question answer is "yes" and you want to enumerate *which* boundary force justifies the node.

This reference lists the categories of signals that justify a non-root node. The taxonomy is **illustrative, not finite** — a node may earn its place by a signal not in this list, provided the 3-question test is met. The taxonomy exists so contributors can name what they are doing; it does not enumerate every possible reason a node could exist.

## The taxonomy (illustrative categories)

Each entry names a signal, what kind of evidence satisfies it, and the canonical mistake when the signal is missing.

### 1. Distinct specialty

**Signal.** The node owns a discipline or craft the parent cannot perform without losing focus or rigor. Examples: a Security reviewer in a code-ship flow, a Documentation writer in a research-paper flow.

**Evidence.** The parent has demonstrably failed to produce output at the specialty's quality bar when the specialty is folded into the parent's scope.

**Canonical mistake.** Conflating "different focus" with "different node." A parent with two distinct focuses is not yet a graph; it is a parent with too broad a scope. Narrow the parent first; add a child second.

### 2. Independent fan-out

**Signal.** The node is one of N parallel siblings dispatched by a parent, each owning an independent slice. See `methodology/04a-decide-work-graph.md` Part 3.7 for the fan-out / fan-in contract.

**Evidence.** The siblings write to disjoint paths; their results compose deterministically at fan-in.

**Canonical mistake.** Fanning out for parallelism when the work is actually sequential with a slow middle. Measure wall-clock before parallelizing; sequential work parallelized is parallel work that surprises the join.

### 3. Executor/tool boundary

**Signal.** The node owns access to a specific executor, runtime, or tool surface that the parent cannot directly reach. Examples: a Scripts/Code node that runs a CI step the parent has no shell access to; a node that holds a service-account token the parent must not see.

**Evidence.** The parent's runtime cannot invoke the executor; the executor requires credentials or capabilities the parent should not hold.

**Canonical mistake.** Folding the executor into the parent for "simplicity." Tool boundaries exist to keep credentials, side-effects, and error surfaces contained; collapsing them is a security and audit regression.

### 4. Auditable branching

**Signal.** The node represents a decision branch that must be recorded separately for audit, replays, or after-action review. Examples: an Approver node in a publish flow; a Reject node in a triage flow.

**Evidence.** The audit trail requires a distinct actor and a distinct timestamp; collapsing the branch into the parent loses one or the other.

**Canonical mistake.** Treating every branch as auditable. Most branches are implementation details that the audit log does not need; recording them is noise.

### 5. Verifier overload

**Signal.** The node is a verifier (per Part 3.5) and the parent cannot simultaneously produce and verify without losing independence. Examples: a Reviewer node in a ship flow; an Auditor node in a finance flow.

**Evidence.** The producer's incentive is to ship; the verifier's incentive is to challenge. Same node, both incentives → reviewer bias.

**Canonical mistake.** "Self-verifying" producers. A producer that emits a self-verification artifact has not been verified; the artifact is at best a smoke test, not a review.

### 6. Privilege / human boundary

**Signal.** The node requires human judgment, sign-off, or a privilege the agent layer cannot or should not hold. Examples: an Operator LGTM node; a payment-authorization node; a deploy-to-prod node.

**Evidence.** The action is irreversible, expensive, or has policy implications the operator owns. Per `op-guard-13` and `methodology/05-op-guards.md`, the agent layer must not bypass this boundary on its own.

**Canonical mistake.** "Pre-approving" the operator. A Human node that the agent is allowed to file "on behalf of" the operator has collapsed the privilege boundary.

### 7. Durable-state boundary

**Signal.** The node owns a piece of state that must persist across runs and across graph invocations, and the parent is a transient computation. Examples: a Repository node that owns a row in a database; a Cron-owned cache.

**Evidence.** The state outlives the parent; the parent cannot read or write the state without going through the owner's contract.

**Canonical mistake.** Inlining durable state into a parent that may be re-run. State in a re-runnable parent is state that mutates on every run, which is a correctness bug.

### 8. Failure containment

**Signal.** The node exists to absorb failures of a specific class so the parent does not have to handle them. Examples: a Retry-wrapper node around a flaky API call; a Circuit-breaker node around an external service.

**Evidence.** The failure class is well-defined (timeout, schema mismatch, rate-limit) and the containment policy is named (retry N times, escalate on persistent failure).

**Canonical mistake.** Building containment into the parent. The parent now has two responsibilities — its own work and the containment policy — and the audit trail loses the boundary.

### 9. Ownership / SLA

**Signal.** The node owns a deliverable with a named SLA (latency, freshness, accuracy) the parent does not commit to. Examples: a Daily-summary node with a 09:00 UTC SLA; a Watchdog node with a 1-minute polling SLA.

**Evidence.** The SLA is named in a contract (operator-facing or downstream-facing); missing the SLA is a defect.

**Canonical mistake.** Promising an SLA the parent does not measure. An SLA without measurement is a wish.

## How to use this taxonomy

When you are adding a node and the 3-question test (Part 3 headline) passes:

1. **Name the signal** the node admits against. If none of the categories above fit, name a new one — the taxonomy is illustrative, not finite. Provide evidence the new category is a real boundary, not a synonym for one already listed.
2. **Cite evidence** for the signal in the design-review notes. The evidence is what a verifier reads on retro to confirm the node still earns its place.
3. **Cite the collapse condition.** Without it, the node is permanent; permanent nodes are a graph-design smell.

When you are running a retro on an existing node:

1. Re-run the 3-question test. If the test now fails, the node is a candidate for collapse.
2. If the test passes, ask whether the signal category is still load-bearing. A node admitted under "verifier overload" that no longer has a producer-vs-verifier incentive conflict can collapse into the producer.

## What this reference is NOT

- **Not the rule itself.** The rule is in Part 3.6 and the Part 3 headline. This reference is the deeper taxonomy the rule references.
- **Not a checklist.** A node does not earn its place by ticking 5 of 9 categories. One signal with evidence is enough; nine signals with no evidence is bureaucracy.
- **Not a finite enumeration.** Real work surfaces boundaries the taxonomy does not name. Add categories when the work forces it; don't force the work into the existing categories.

## Cross-references

- `methodology/04a-decide-work-graph.md` Part 3 headline — the 3-question node-admission test.
- `methodology/04a-decide-work-graph.md` Part 3.6 — the scoped general node-admission rule (where this taxonomy applies).
- `methodology/04c-decide-master-ticket.md` Part 1 — the master-ticket 4-question test (a per-master-version of the 3-question test).
- `methodology/04d-decide-flow-spec.md` — the flow spec that names per-flow signals and policies.

## Audit-line

`## [2026-08-09T18:30Z] 04a-node-admission-taxonomy-reference-shipped -- deeper taxonomy reference added at methodology/references/04a-node-admission-taxonomy.md v0.1.0. Per Part 3.6 amendment (Q1/F-2 ACCEPT), the reference sits behind the 3-question headline; it is illustrative, not finite. Source: t_78936c6e (wandermist article-3 disposition).`