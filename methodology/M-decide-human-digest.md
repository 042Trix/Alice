---
id: alice-methodology-human-digest-template
created: 2026-08-07T00:00:00Z
updated: 2026-08-08T16:35:00Z
title: "Methodology — Human-readable digest for operator-facing retro findings"
type: methodology
status: draft
source: alice-framework
version: 0.4.0
tags: [kind:methodology, kind:human-digest, kind:operator-facing, kind:retro, kind:interaction, kind:pdf-format, kind:operator-review, kind:executive-reporting-aligned, project:alice]
confidence: 0.9
links: ["[[methodology/06a-decide-retro-v2.md]]", "[[methodology/04c-decide-master-ticket.md]]", "[[methodology/M-decide-x-article-review-flow.md]]", "[[methodology/03b-decide-operator-agent-interaction.md]]", "[[templates/human-digest.md.template]]"]
---

# Methodology — Human-readable digest for operator-facing retro findings

> The technical retro is the audit record. The human-readable digest is the operator's reading surface.

> **Spec alignment (2026-08-08, source=t_5dc19cae):** The retro digest is the **Type 2 (Retro digest)** specialization in the canonical 5-type shape taxonomy at `~/.hermes/skills/productivity/executive-reporting/references/per-digest-type-shape.md`. The canonical 8-section shape contract lives in `methodology/03b-decide-operator-agent-interaction.md` Part 13; the retro specialization preserves all 8 canonical sections and adds 2 retro-specific sections — §4 "Council / agent agree and disagree" (between canonical §3 and §5) and §9 "Individual council / agent responses" (between canonical §5 and §6, with the per-seat detail at the bottom tier). The Hermes-instance implementation of the canonical contract is the `executive-reporting` skill (Scribe-owned, doc-writer-authored, shipped 2026-08-08 12:21 → 12:27 UTC, ticket `t_4699bb4a`); the retro-digest generator (`~/.hermes/tools/post_graph_retro_human_digest.py`) follows the skill's 6 translation rules + 5-step delivery protocol.

## Part 1: Purpose

A completed Retro-A contains valuable evidence, but its task body is written for traceability, verification, and follow-up work. It may include identifiers, paths, event details, and implementation vocabulary that are useful to the system but slow for a human to read. The human digest translates the same findings into plain language without replacing or weakening the technical record.

The digest answers three practical questions:

1. What happened in this flow?
2. What matters most?
3. What, if anything, does the operator need to decide?

The digest is not a second source of truth. When the digest and technical record disagree, the technical record wins and the digest must be corrected before it is presented as ready.

### Spec position

The retro digest is one of **5 operator-review artifact types** codified by the `executive-reporting` skill (Scribe-owned, shipped 2026-08-08, ticket `t_4699bb4a`). The canonical 8-section shape contract is in `methodology/03b-decide-operator-agent-interaction.md` Part 13; the retro specialization is the 10-section specialization described in this methodology. Other operator-review artifacts (council verdict digests, master-ticket summaries, audit-line digests, approval requests) follow the canonical 8 directly without the retro's per-seat specialization. See the skill's `references/per-digest-type-shape.md` for the full 5-type taxonomy and the per-type section-content map. The skill is the canonical Hermes-instance implementation of this methodology's contract; the methodology is the Alice-canonical source.

## Part 2: Two-layer record

Every eligible flow has two deliberately separate layers:

| Layer | Purpose | Content | Canonical reader |
|---|---|---|---|
| Technical record | Audit, verification, and follow-up execution | Retro task body, findings, comments, events, parent/child links | Verifier and kanban system |
| Human digest | Fast operator understanding and decision-making | Plain-language headline, six-axis explanation, recommendations, decisions, observations | Operator |

The technical record is not made less detailed to improve readability. The digest is added as a translation layer so audit detail and operator usability can coexist.

## Part 3: Digest contract

The digest MUST contain these sections, in this order. The order is critical: **the operator reads top-down; only the top sections need to be read for a quick decision.** Lower sections are reference material for when the operator wants depth.

### Section order (top = most actionable)

1. **What this is** — one or two sentences describing the reviewed flow and review stage. Scope/summary at the top.
2. **The headline** — no more than five bullets, written in plain language. The operator's first read.
3. **Action items** — bulleted list of what the operator needs to decide, in priority order. Each item: state what to do + the deadline + the consequence of not acting. **When the parent master ticket carries council content decisions (Q1..Q5 or analogous) on a separate operator gate, those decisions MUST be embedded inline under "Council content decisions (inline)" — not referenced by pointer only.** Per operator direction 2026-08-08 (source=t_9450b7e0).
4. **Council / agent agree and disagree** — if a council deliberated, this is the agree-vs-disagree summary table. The operator sees the structure of the disagreement in 10 seconds.
5. **Detailed findings** — bulleted list of the substantive findings, with severity and proposed corrective action. This is the agent's synthesis across the individual responses below; the operator reads it before drilling into per-seat detail.
6. **Operator decisions** — Accept / Reject / Defer decision table. The operator records their decision against each finding.
7. **Operator's observations** — blank section for the operator to add observations, override the agent's recommendation, or record context the agent could not know.
8. **Evidence anchors** — a collapsible section pointing to the technical record. The operator expands only when they need depth.
9. **Individual council / agent responses** — per-seat or per-axis summary. Each section is short (1-2 paragraphs). **Technical detail; lives at the bottom per the operator's 2026-08-08 direction so the top stays decision-driven.** **When the parent master ticket carries council content decisions, this section MUST also include a "Council content decisions (per-seat)" subsection that surfaces each seat's verdict on each Q in a compact table — so the operator sees the disagreement structure on the content gate in 10 seconds, without leaving the digest.** Per operator direction 2026-08-08 (source=t_9450b7e0).
10. **Digest metadata** — timestamp, source ticket, generation method.

### Section → skill canonical mapping

The 10 retro sections preserve all 8 sections of the canonical shape contract from `methodology/03b-decide-operator-agent-interaction.md` Part 13 and add 2 retro-specific sections. The mapping below shows where each retro section sits in the canonical 8 (cross-referenced against the `executive-reporting` skill's `SKILL.md` §"The shape contract" and `references/per-digest-type-shape.md` §"Type 2"):

| Retro section | Canonical mapping | Notes |
|---|---|---|
| 1. What this is | Canonical §1 | Unchanged. |
| 2. The headline | Canonical §2 | Unchanged. |
| 3. Action items | Canonical §4 (Recommended next steps) — preserves the canonical action-items rule | Positioned where the canonical contract puts "Recommended next steps" (the first hand-on decision surface). When the parent master carries content decisions, those decisions embed inline under "Council content decisions (inline)" per skill `per-digest-type-shape.md` Type 2 §3. |
| 4. Council / agent agree and disagree | Retro specialization (inserted between canonical §4 and §5) | The agree-vs-disagree table. The retro specialization renders canonical §3 "What the agent found" as sections 4 + 5 (council agree/disagree + detailed findings). |
| 5. Detailed findings | Retro specialization (renders canonical §3 substantively) | The agent's synthesis across per-seat / per-axis responses; severity + proposed corrective action. |
| 6. Operator decisions | Canonical §7 (Operator decisions) | Accept / Reject / Defer table. The placement before §7 Operator's observations preserves the canonical pairing of observations + decisions in the operator action surface. |
| 7. Operator's observations | Canonical §6 (Operator's observations) | Empty starter bullets. |
| 8. Evidence anchors | Canonical §5 (Evidence anchors) | Collapsible. The only section where technical identifiers, ticket IDs, file paths, run IDs, and agent / profile names are allowed without translation. |
| 9. Individual council / agent responses | Retro specialization (inserted between canonical §5 and §6, at the bottom tier) | Per-seat / per-axis 1-2 paragraph summaries. **Bottom tier** — operator reads only when they want depth. When the parent master carries content decisions, this section MUST include a "Council content decisions (per-seat)" subsection per skill `per-digest-type-shape.md` Type 2 §9. |
| 10. Digest metadata | Canonical §8 (Metadata) | Timestamp, source ticket, generation method, technical-record-is-canonical yes/no. |

The skill's `per-digest-type-shape.md` Type 2 is the canonical section-content map for retro digests; this methodology's section order is the Alice-canonical source for the retro specialization, and the skill's Type 2 must agree with this mapping. If the two drift, the spec (this methodology) is canonical for the section order; the skill's Type 2 reference must be patched to match on the next skill release.

### Why this order

The operator reads top-down. Sections 1-3 are the **actionable top tier** — *what this is*, *what matters*, *what to do* — readable in 30 seconds. Sections 4-5 are the **transitional interpretation layer**: the council's overall disagreement structure and the substantive findings with proposed corrective actions. Sections 6-7 are the **operator action surface**: decisions and observations the operator adds in their own hand. Sections 8-10 are the **technical-detail bottom tier**: collapsible evidence anchors, per-seat / per-axis individual responses (the dense technical detail), and digest metadata.

**Anti-pattern:** putting technical detail (individual seat responses, severity codes, ticket IDs, file paths, agent profiles, run identifiers) in the top tier or the operator-action surface. The operator doesn't read those unless they're already in the decision. **Technical detail belongs in sections 8-10, collapsed, expandable.** The operator's 2026-08-08 correction specifically moved Individual council / agent responses (formerly section 5, middle) to section 9 (bottom tier) so the actionable top stays clear.

**Anti-pattern (v0.3.0, added 2026-08-08, source=t_9450b7e0):** referencing council content decisions (Q1..Q5 or analogous) by pointer only — e.g. "content Q1–Q5 on the master ticket remain a separate operator gate" in the action items, with the actual question text + council recommendation living on the master ticket body. The operator reads the digest to make a decision; if the digest references content decisions by pointer, the operator has to leave the digest to read another doc, which defeats the digest's purpose. **The digest must embed the content decisions inline in section 3 (action surface) + section 9 (per-seat analysis).** A pointer in section 8 (evidence anchors) is fine for verification; the action surface is the digest itself.

The fillable form is `templates/human-digest.md.template`.

### Headline + axis + recommendation rules

The 6 translation rules in `~/.hermes/skills/productivity/executive-reporting/references/translation-discipline.md` are the canonical discipline for retro digests (and all other operator-review artifacts). The retro digest MUST satisfy:

- **Rule 1 — Preserve conclusions, replace language.** The agent's verdict on each finding must match the technical record's verdict; the language can differ.
- **Rule 2 — Combine closely related evidence; do not invent.** Two findings that point at the same workflow weakness can be combined; inventing a finding absent from the technical record is forbidden.
- **Rule 3 — Translate every identifier.** Ticket IDs, file paths, run IDs, agent / profile names go into **Evidence anchors** (§8), not the headline or action items.
- **Rule 4 — Translate severity into consequence.** "Severity: medium" → "this is moderate; one workflow weakness with a known fix."
- **Rule 5 — Translate jargon into outcome.** "Retro-A producer ran the 6-axis evaluation" → "the agent review ran the standard evaluation."
- **Rule 6 — Do not editorialize.** The digest reports what the technical record says; the operator's commentary lives in §7 (Operator's observations).

The retro-specific application of these canonical rules (below) preserves the headline rules, axis-summary rules, and recommendation rules that the retro has carried since v0.1.0. The canonical translation discipline is the version that all operator-review artifacts share; the retro-specific application is authoritative for the retro specialization and is the surface the verifier checks against the retro template.

### Headline rules (retro-specific application)

The headline is the highest-visibility part of the digest. It MUST:

- use ordinary language rather than internal vocabulary;
- contain 3–5 bullets maximum;
- state the most important outcome, risk, or remaining decision;
- avoid jargon, ticket IDs, file paths, run IDs, and implementation details;
- avoid claiming that a recommendation was accepted unless the operator has recorded that decision.

Technical references belong under **Evidence anchors**, not in the headline. A digest may include identifiers and paths in the collapsible evidence section because that section is intentionally for verification.

### Axis-summary rules (retro-specific application)

The six axis summaries mirror `methodology/06a-decide-retro-v2.md` but translate its evidence into plain English. Each axis receives one `pass`, `partial`, or `fail` assessment and a 3–5 sentence explanation. The digest may combine closely related evidence, but it must not omit an axis or invent an observation absent from the technical record. (Per canonical translation Rule 4 — translate severity into consequence — the "pass / partial / fail" label is acceptable in the axis summary because it is the canonical 6-axis vocabulary; the consequence of the label is what the per-axis explanation translates.)

### Recommendation rules (retro-specific application)

Each recommendation states:

- the proposed action;
- why the action matters;
- the decision the operator must make: Accept, Reject, or Defer.

Recommendations are not implementations. An accepted recommendation follows the normal spec-first and verifier-gated workflow. Operator silence is not acceptance.

## Part 4: Generation and delivery lifecycle

The digest is generated after the Retro-A writer has published a complete retro task body and before the operator's Retro-H review window begins.

1. **Read the technical record.** Confirm the retro is eligible and that all six axes and findings are present.
2. **Translate, do not reinterpret.** Preserve the technical record's conclusions while replacing internal language with plain language.
3. **Validate.** Check that the headline has no jargon, ticket IDs, file paths, or run IDs; every axis appears; recommendations identify a decision; and all evidence anchors resolve.
4. **Save the artifact.** Write `human-digest.md` in the retro workspace, alongside the technical record or its workspace output. The technical record remains unchanged.
5. **Post visibility pointers.** Add a short comment to the Retro-A task and a comment to the master ticket. The master comment begins with `Digest ready` and points to the digest artifact.
6. **Deliver when applicable.** If the flow originated in the operator's connected chat, send the digest as a real attachment using the existing attachment-delivery path. If no connected chat applies, do not fabricate a delivery; the saved artifact and ticket comments remain the surfaces.
7. **Open Retro-H.** The human review task opens with the digest pointer first, so the operator does not need to navigate through the technical record to begin review.

### Ingesting the parent master's council content decisions (v0.3.0, 2026-08-08)

When the Retro-A is attached to a master ticket that carries council content decisions (Q1..Q5 or analogous) on a separate operator gate, the digest generator MUST:

1. **Discover the parent master ticket** via the Retro-A's parent edge (or sibling lookup if Retro-A was filed as a child of the master).
2. **Read the master's "Operator decisions" table** (canonical location for content decisions awaiting operator disposition). The table is structured as `| Item | Disposition | Notes |` with rows like `| Council Q1 | PROPOSED | awaiting operator |`.
3. **Read the canonical council verdict** for each Q. The verdict lives in the most recent council comment on the master (typically the 5-seat re-run ticket's comment thread, or a "Phase 4 council synthesis" comment). The verdict text format is `**Q<N>: <question>** — Council recommendation: <recommendation>`.
4. **Read the per-seat rationale** from the council comment. Each seat produces `**N. <seat name> — verdict: <verdict>.**` with a Position/Reasoning paragraph. The generator maps these to the per-seat table in section 9.
5. **Embed inline, not by pointer.** Section 3 renders each Q as a bullet with the question + council recommendation + Accept/Reject/Defer placeholder. Section 9 renders the per-seat table. Section 8 (evidence anchors) keeps the verification pointer to the master + council ticket for forensics, but the action surface is the digest itself.

The data path is the same regardless of which council ticket is canonical (3-seat original, 5-seat re-run, or future n-seat variant): the generator picks the most recent council comment on the master that contains the per-decision Q-N verdict structure. This is portable across article reviews.

The delivery step is best-effort only after the artifact is saved and the ticket pointers are posted. A failed attachment must not erase or invalidate the digest; it must be recorded and retried through the existing notifier path.

## Part 5: Retro-A and Retro-H responsibilities

### Retro-A — agent review

Retro-A produces the technical record and the first human digest. The writer is still bound by the independence rule in `06a`: the writer must be different from the executor. Retro-A does not make operator decisions and does not mark findings accepted on the operator's behalf.

### Retro-H — operator review

Retro-H opens with the human digest. The operator's review reads top-down: first the actionable top tier (sections 1-3) to see what matters, then sets each finding to Accept, Reject, or Defer in the **Operator decisions** section (section 6), then adds observations in the **Operator's observations** section (section 7) for context the agent could not know or to override the agent's recommendation. Those decisions are recorded against the technical retro task according to `06a`; the digest may mirror the decisions for readability but does not replace the technical disposition.

## Part 6: Boundaries

This pattern does not:

- change the technical retro task format;
- remove evidence, identifiers, or paths from the technical record;
- auto-accept findings;
- replace the verifier gate;
- change the operator's decision window;
- silently alter a methodology, skill, profile, or cron when a recommendation is accepted.

A digest is a presentation artifact, not permission to skip the normal implementation flow.

## Part 7: Verification checklist

Before marking a digest ready, verify:

- [ ] The technical retro is complete and is the source used for the digest.
- [ ] The digest exists at the retro workspace's `human-digest.md` path.
- [ ] The headline has 3–5 plain-language bullets.
- [ ] The headline contains no jargon, ticket IDs, file paths, or run IDs.
- [ ] All six axes have a pass/partial/fail summary and evidence-based explanation.
- [ ] Each recommended next step says what to do, why it matters, and what decision is needed.
- [ ] Evidence anchors point to the technical record.
- [ ] Operator decisions (section 6) and Operator's observations (section 7) sections are present and unfilled, ready for the operator.
- [ ] Individual council / agent responses (section 9) is at the bottom tier, after evidence anchors (section 8) — not in the middle.
- [ ] **Council content decisions are embedded inline in section 3 (action surface) + section 9 (per-seat analysis) when the parent master carries content decisions (Q1..Q5 or analogous). The digest does NOT reference content decisions by pointer only.** (v0.3.0, source=t_9450b7e0)
- [ ] Retro-A and master comments point to the digest.
- [ ] Discord attachment delivery is confirmed when applicable, or its absence is recorded as not applicable / retryable.
- [ ] Retro-H opens with the digest pointer.
- [ ] **All 6 translation rules from `~/.hermes/skills/productivity/executive-reporting/references/translation-discipline.md` are satisfied (verification checklist at end of that reference doc).** (v0.4.0, source=t_5dc19cae)
- [ ] **Section order matches the canonical 8 + retro specialization mapping documented in this methodology Part 3 §"Section → skill canonical mapping".** (v0.4.0, source=t_5dc19cae)
- [ ] **The 5-step delivery protocol (Markdown → PDF → Discord → ticket comment → vault copy) was followed per `methodology/03b-decide-operator-agent-interaction.md` Part 13.** (v0.4.0, source=t_5dc19cae)
- [ ] **Retro §4 agreement column reflects the actual per-seat outcome (§9), not the per-Q recommendation header. Spot-check before marking ready.** (v0.4.0, source=t_5dc19cae; closes audit gap 3)

## Part 8: Example headline

A suitable headline is short and readable without system context:

- The main work completed successfully, but the review found a few workflow weaknesses.
- The biggest risk is that one important check happened too late to prevent rework.
- Several improvements are already in place; the remaining choices are small and clearly defined.
- Your review is needed on which changes to accept, reject, or defer.

An unsuitable headline would list internal phase names, ticket identifiers, paths, event numbers, or tool failures. Those belong in Evidence anchors.

## See also

- `methodology/06a-decide-retro-v2.md` — canonical technical retro contract.
- `methodology/04c-decide-master-ticket.md` — operator-facing master and done-gate.
- `methodology/M-decide-x-article-review-flow.md` — current operator-facing flow using Retro-A and Retro-H. **Part 7.5** owns the parallel Phase 4 council-verdict PDF delivery (same canonical 8-section shape, same 5-step protocol). Retro-A digests and council-verdict PDFs are the two downstream applies of `methodology/03b-decide-operator-agent-interaction.md` Part 13's operator-review artifact rule.
- `methodology/03b-decide-operator-agent-interaction.md` Part 13 — the operator-review artifact format / PDF rule (the canonical rule; this methodology specializes it for retro digests). The canonical 8-section shape there (What this is / Headline / What the agent found / Recommended next steps / Evidence anchors / Operator observations / Operator decisions / Metadata) is the minimal contract; the **retro specialization below is 10 sections** — it splits canonical #3 "What the agent found" into Council / agent agree-and-disagree (transitional) + Detailed findings + Individual council / agent responses, and splits canonical #4 "Recommended next steps" into Action items + Detailed findings (proposed corrective actions).
- `templates/human-digest.md.template` — fillable 10-section digest form (v0.2.0 with the reordered sections).
- `~/.hermes/skills/productivity/executive-reporting/SKILL.md` — the Hermes-instance skill that operationalizes the canonical operator-review artifact contract (Scribe-owned, doc-writer-authored, shipped 2026-08-08, ticket `t_4699bb4a`). Covers all 5 digest types (council verdict / retro / master summary / audit-line / approval request), the 6 translation rules, the canonical 8-section shape contract, and the 5-step delivery protocol. The retro digest is **Type 2** in this skill's taxonomy.
- `~/.hermes/skills/productivity/executive-reporting/references/per-digest-type-shape.md` — the 5 digest types with the per-type section-content map. The retro specialization (this methodology) is **Type 2**. The per-type shapes are the canonical instance-side map; this methodology's section order is the Alice-canonical source. If the two drift, the spec wins.
- `~/.hermes/skills/productivity/executive-reporting/references/translation-discipline.md` — the 6 translation rules (canonical version; this methodology's headline / axis / recommendation rules are the retro-specific application).
- `~/.hermes/skills/productivity/executive-reporting/references/worked-examples.md` — the canonical worked examples (council 5-seat v0.2.0, retro v0.3.0, council 3-seat v0.1.x superseded). The retro specialization's worked example is the canonical source for retro §4 + §9 alignment (closes audit gap 3).
- `~/.hermes/skills/productivity/executive-reporting/references/audit-existing-artifacts-against-template.md` — the 10 audit gaps surfaced in the 2026-08-08 audit. **Gaps 3, 4, 5** are spec-side fixes (template shape inconsistencies); the rest are instance-state fixes. The spec amendment in v0.4.0 documents the gap state for future agents and closes gaps 3, 4, 5. **Gaps 1, 2, 6, 7, 8, 9, 10** are out of scope for the spec amendment per operator direction ("don't worry about historical reports"); they remain on the audit reference for future addressing.
- `~/.hermes/skills/productivity/executive-reporting/templates/executive-report.md.template` — the canonical 8-section fillable form for non-retro operator-review artifacts (Types 1, 3, 4, 5). The skill's `templates/` directory currently holds only this template; the retro specialization's fillable form remains at `templates/human-digest.md.template` (this methodology's template). The retro template is the Alice-canonical source; the skill's `executive-report.md.template` is the canonical-8 fallback for non-retro artifacts. (The asymmetry — retro template in alice-framework, canonical-8 template in the skill — is intentional: the retro specialization is one of the 5 types, and Alice owns the specialization; the skill owns the non-retro canonical shapes.)

> **Note on scope (2026-08-07, t_e35032aa):** Retro-A is the original human-digest consumer. The same canonical 8-section shape is also the shape for council-verdict PDFs (per `M-decide-x-article-review-flow.md` Part 7.5). Templates and headline hygiene rules below apply to both surfaces. The retro specialization's 10 sections are the operator's reading contract for retro-style flows; council verdict PDFs may use the canonical 8 directly if they do not need per-seat responses.

> **Note on section order (2026-08-08, t_4d1de050):** v0.2.0 reordered the 10 sections so actionable content leads, technical detail trails. See the Audit-line entry and the **Section order** subsection in Part 3.

## Audit-line

`## [2026-08-07T23:30Z] human-digest-template — documented the two-layer retro record: technical task remains canonical; human-readable digest provides plain-language headline, six-axis summary, recommendations, collapsible evidence anchors, operator observations, and Accept/Reject/Defer decisions. source=t_a278d431`

`## [2026-08-07T23:30Z] human-digest-scope-v0.1.1 — extended the "See also" to flag the 8-section PDF shape contract as the canonical shape for both Retro-A digests AND Phase 4 council-verdict PDFs (per `M-decide-x-article-review-flow.md` Part 7.5, t_e35032aa); the retro methodology is the canonical instance, the council-verdict PDF is the parallel downstream apply. Template + headline hygiene rules apply to both surfaces.`

`## [2026-08-07T23:30Z] human-digest-scope-v0.1.1-frontmatter — bumped version 0.1.0 → 0.1.1, added tags `kind:pdf-format` + `kind:operator-review`, added link to `methodology/03b-decide-operator-agent-interaction.md` Part 13. source=t_e35032aa`

`## [2026-08-08T00:00Z] human-digest-section-order-v0.2.0 — operator correction (2026-08-08): rearranged the 10-section digest so the **actionable top tier** (sections 1-3: What this is / Headline / Action items) leads, the **operator action surface** (sections 6-7: Operator decisions / Operator observations) sits between the transitional interpretation layer and the technical-detail bottom tier, and **technical detail** (sections 8-10: Evidence anchors / Individual council-or-agent responses / Digest metadata) is at the bottom, collapsed. The single largest move: Individual council / agent responses was section 5 (middle) and is now section 9 (bottom tier). Detailed findings promoted from section 6 to section 5 (the transitional synthesis across the individual seat responses, read before drilling into per-seat detail). Operator decisions moved from section 9 to section 6 (the operator's first hand-on action surface, paired with Action items at the top). Operator observations moved from section 8 to section 7 (paired with Operator decisions). Frontmatter bumped 0.1.1 → 0.2.0. Template (`templates/human-digest.md.template`) reordered to match. source=t_4d1de050`

`## [2026-08-08T08:55Z] human-digest-self-contained-v0.3.0 — operator correction (2026-08-08, source=t_9450b7e0): the digest must be self-contained for the operator's decision. When the parent master ticket carries council content decisions (Q1..Q5 or analogous) on a separate operator gate, those decisions MUST be embedded inline in section 3 (action surface, under "Council content decisions (inline)") + section 9 (per-seat analysis, under "Council content decisions (per-seat)"). The digest must NOT reference content decisions by pointer only — the operator reads the digest to make a decision; if the digest points to another doc, the operator has to leave the digest to know what the content questions are, which defeats the digest's purpose. Added anti-pattern: pointer-only reference. Added Part 4.5: ingestion data path (master's "Operator decisions" table + canonical council comment with Q-N verdict structure + per-seat rationale). Template (`templates/human-digest.md.template`) gained the two new subsections; section 3 + section 9 HTML comments updated to require the embed when content decisions exist. Verification checklist gained the new item. Frontmatter bumped 0.2.0 → 0.3.0.`

`## [2026-08-08T16:35Z] human-digest-spec-alignment-v0.4.0 — operator correction (2026-08-08): aligned `M-decide-human-digest.md` with the `executive-reporting` skill shipped earlier today (ticket `t_4699bb4a`). Added top-of-file Spec alignment blockquote (retro digest = Type 2 in the skill's 5-type taxonomy); added Part 1 Spec position paragraph; added Part 3 §"Section → skill canonical mapping" table (retro 10 → canonical 8 + 2 retro specializations); added cross-reference to the 6 translation rules in `~/.hermes/skills/productivity/executive-reporting/references/translation-discipline.md` and relabeled the existing headline / axis / recommendation rules as retro-specific application; expanded Part 7 verification checklist with 4 new items (translation rules, section-order mapping, 5-step delivery protocol, §4 §9 agreement-column spot-check); added 6 new skill references to the See also list (SKILL.md, per-digest-type-shape, translation-discipline, worked-examples, audit-existing-artifacts, executive-report template); documented the spec-side audit gaps (gaps 3, 4, 5 closed; gaps 1, 2, 6-10 out of scope per operator direction). Frontmatter bumped 0.3.0 → 0.4.0; tag `kind:executive-reporting-aligned` added. Template `templates/human-digest.md.template` also updated to v0.4.0 (top-of-file banner; §4 agreement-column guidance HTML comment closing audit gap 3; §3 "Why it matters" sourcing guidance HTML comment closing audit gap 4). Companion update to `methodology/03b-decide-operator-agent-interaction.md` Part 13 added canonical-implementation reference + skill cross-reference in See also. source=t_5dc19cae`
