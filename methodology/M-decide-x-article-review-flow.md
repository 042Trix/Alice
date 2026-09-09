---
id: alice-methodology-M-decide-x-article-review-flow
created: 2026-09-05T18:30:00Z
updated: 2026-09-08T15:30:00Z
title: "Methodology M — Decide the x-article-review flow (the operator-facing Retro-A + Retro-H + council-verdict pipeline with Q-N + Retro-A classification rubric + auto-accept policy)"
type: methodology
status: draft
source: alice-framework
version: 0.2.0
alice-ticket: t_653117e5
parent-meta: t_653117e5
amended_by: ["t_653117e5", "t_003a07e2"]
companion: ["[[methodology/04d-decide-flow-spec.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[methodology/04c-decide-master-ticket.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[methodology/M-decide-instance-vs-framework.md]]", "[[methodology/M-decide-graph-readiness.md]]", "[[methodology/M-decide-alice-publish-flow.md]]", "[[methodology/M-decide-graph-audit-promotion-flow.md]]", "[[methodology/06a-decide-retro-v2.md]]", "[[references/instance-leak-check-spec.md]]"]
links: ["[[methodology/04d-decide-flow-spec.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[methodology/04c-decide-master-ticket.md]]", "[[methodology/M-decide-instance-vs-framework.md]]", "[[methodology/M-decide-graph-readiness.md]]", "[[methodology/M-decide-alice-publish-flow.md]]", "[[methodology/M-decide-graph-audit-promotion-flow.md]]", "[[methodology/06a-decide-retro-v2.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[2-ATOMIC/rules/op-guard-16-spec-first-flow-2026-08-05.md]]", "[[2-ATOMIC/rules/op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md]]", "[[2-ATOMIC/rules/op-guard-19-pre-verify-artifact-state-2026-08-08.md]]", "[[2-ATOMIC/rules/op-guard-24-clarify-tool-discord-render-pattern-2026-08-09.md]]", "[[2-ATOMIC/rules/op-guard-29-per-loop-hitl-gate-2026-08-15.md]]", "[[2-ATOMIC/rules/op-guard-32-four-stage-graph-hitl-2026-08-17.md]]"]
teaching-example: true
---

# Methodology M — Decide the x-article-review flow (v0.2.0)

> The `x-article-review` flow takes one external x.com article from "URL filed on alice-framework board" to "operator-facing Retro-H executive report + per-finding corrective-action-B child tickets shipped." The flow is operator-facing (Retro-A + Retro-H + council-verdict HITL-A per `methodology/06a-decide-retro-v2.md` + `methodology/04a-decide-work-graph.md` Amendment 7/8); it is NOT git-facing (no `alice-publish` style 7-step publish pipeline — the `alice-publish` flow's STEP 11 Discord release message is the one-shot topic-only format owned by this flow's Q-N + Retro-A rubric). The master-ticket substrate is shared with the `alice-publish` flow (per `methodology/M-decide-alice-publish-flow.md` Part 1); the x-article-review flow is the operator-facing reader of articles, the alice-publish flow is the git-facing publisher of releases.

> **v0.2.0 (2026-09-08, t_003a07e2):** the v0.1.0 canonical-promote codifies the Q-N classification rubric (cross-link / structural / discipline-preserve classes) + the auto-accept policy for the cross-link class. v0.2.0 extends the same rubric pattern to the Retro-A findings (the 6-axis flow-eval findings emitted in Phase 3 per `methodology/06a-decide-retro-v2.md` Amendment 6). Per operator directive 2026-09-05 (verbatim: *"also auto accepting retro items"*), the Retro-A classification rubric mirrors the Q-N rubric: `cross-link` class auto-accepts; `structural` and `discipline-preserve` classes surface to the operator via the Retro-H gate. The auto-accept policy + mixed-class handling + operator-override escape mirror the Q-N rubric verbatim with `class=retro-a` substituted for `class=council-q`. Frontmatter: version 0.2.0 (MINOR bump — additive feature: new Retro-A classification rubric layer, no contract change to Q-N rubric); `amended_by` extended with `[[ticket:t_003a07e2]]`; `updated:` field bumped to 2026-09-08T15:30Z. The Retro-A rubric is intra-doc (Part 4b); paired-wiki siblings extend their `amended_by` array per op-guard-5. The companion intent-doc amendment ships via coder follow-up per op-guard-16 spec-first-flow + op-guard-17 alice-first / instance-second / compliance-gate. Source ticket: `t_003a07e2` (the operator directive on extending Variant A auto-accept to Retro-A findings).

> **v0.1.0 canonical-promote (2026-09-05, t_653117e5):** this doc is the canonical-promote of the x-article-review flow spec from instance-side (`~/.hermes/methodology/M-decide-x-article-review-flow.md` does not exist as a prior instance-side copy; the doc is canonical-first per the operator's directive on 2026-09-05). Codifies the Q-N classification rubric (cross-link / structural / discipline-preserve classes) + the auto-accept policy for the cross-link class + the mixed-class handling + the operator-override escape. The version starts at `0.1.0` (no prior canonical; this is the first ship). The companion intent doc is the existing `~/.hermes/loops/intents/x-article-review-intent.md` (live at v0.4.0 per the loop's `status: live-pending-test`); the loop + intent are derived from this doc and conform to its contract per op-guard-17. The doc ships with the alice-framework v0.2.x working tree cluster (per `t_653117e5`); the CITATION.cff version is not operator-authorized to bump in this change set, so the doc lands in the canonical home without an accompanying release. Source ticket: `t_653117e5` (the operator disposition on the Q-N classification rubric Variant A; the prior filesystem wedge + body-content issue per the doc-writer run history `t_9d92f28e1d8f4b6d` is the source of the gap).

## Source incidents

### `t_653117e5` (2026-09-05) — the Q-N classification rubric that motivated this doc

The x-article-review loop runs the council-verdict phase (`methodology/04a-decide-work-graph.md` Phase 4) on every article review. The council verdict emits per-finding Q-N findings with `ACCEPT / REJECT / DEFER` disposition choices (per `methodology/04a-decide-work-graph.md` Amendment 7 / HITL-A gate). With a 60+ article backlog (per the operator's standing backlog at the 2026-09-05 chat), the HITL-A disposition is the bottleneck: every Q-N finding surfaces to the operator, even when the finding is a non-structural cross-link (a citation, a cross-reference, an axiom-register entry) that the council verdict has already evaluated.

The operator's directive on 2026-09-05 (verbatim: *"Let's try it"*) approves Variant A: auto-accept council Q-N findings that fall into the `cross-link` class (non-structural, audit-trail-preserving); only surface `structural` and `discipline-preserve` findings via HITL-A. The Q-N classification rubric is the contract that distinguishes the three classes; this doc codifies the rubric + the auto-accept policy + the mixed-class handling + the override escape. The rubric is a CHANGELOG-style amendment to the methodology side; the instance-side loop + intent are derived (per op-guard-17 alice-first / instance-second) and conform to the rubric on the next audit cycle.

### `t_9d92f28e1d8f4b6d` (2026-09-07, jarvis probe) — the host-filesystem wedge that surfaced the body-content gap

The first attempt at this methodology doc was blocked on 2026-09-07 by a host-filesystem wedge on `/Users/homestead/Documents/alice-framework/` (per the doc-writer run history on `t_653117e5`). The jarvis probe independently re-verified the wedge (per the ticket comment) and surfaced a stacked gap: the canonical target file `M-decide-x-article-review-flow.md` is genuinely absent from the alice-framework repo (4 sibling `M-decide-*.md` files `stat()` fine; the target `stat()`s as `No such file or directory` instantly, not as EINTR). The body assumed an existing file to amend; the actual on-disk state was that the file had never been canonical-promoted to alice-framework. The body-content issue is the substantive gap that this doc ships. The filesystem wedge resolved 2026-09-08T14:52Z (verified by `default` lane); this doc ships as the v0.1.0 canonical-promote.

## Part 1: Purpose

The `x-article-review` flow takes one external x.com article from "URL filed on alice-framework board" to "operator-facing Retro-H executive report + per-finding corrective-action-B child tickets shipped + audit-line + auto-accept or HITL-A disposition per the Q-N classification rubric + auto-accept or Retro-H gate surface per the Retro-A classification rubric." The flow is the operator's contract for processing articles: it names the gates, the inputs, the outputs, and the verifier that confirms each step landed.

Without this doc, the `x-article-review` loop at `~/.hermes/loops/hermes.yaml` is a council-verdict HITL-A + Retro-A + Retro-H pipeline with no classification rubric for either Q-N findings OR Retro-A findings — every finding surfaces to the operator regardless of structural significance. The 60+ article backlog is the operator-action surface that gets drowned; the doc is the structural fix that closes the surface on both gates.

This doc is the canonical reference for the `x-article-review` flow. The instance side is the `x-article-review` loop + its intent file (`~/.hermes/loops/intents/x-article-review-intent.md`); both are derived from this doc and conform to its contract per op-guard-17. The companion reference for the alice-publish-side release-message discipline is `methodology/M-decide-alice-publish-flow.md` Part 2 Field 3 Output 6 — the topic-only release message is the alice-publish publisher of the format; this doc is the owner of the rule (the rule lives at the x-article-review level because the rule is about cross-flow attribution discipline — the article is the input to multiple flows; the release message format must be consistent across them).

## Part 2: The flow spec (adapted from `methodology/04d-decide-flow-spec.md`)

The canonical 6-field flow spec in `04d-decide-flow-spec.md` is **Goal / Inputs / Outputs / Success Criteria / Retry Parameters / Escalation Process**. The x-article-review flow adapts the pattern to its council-verdict + Retro-A + Retro-H + Q-N classification nature: the flow's "retry" and "escalation" are uniform across all steps (transient → retry, hard-failure → operator-blocked); the "outputs" include per-finding corrective-action-B child tickets (per `methodology/06a-decide-retro-v2.md` Amendment 6) + the Q-N auto-accept audit line (per the rubric in Part 4 below). The verifier-gated acceptance criteria substitute for the canonical "Success Criteria" field with a sharper "the verifier checks these N conditions" framing.

### Field 1 — Goal

Review one x.com article end-to-end with council-verdict Q-N findings + operator HITL-A disposition + Retro-A 6-axis flow-eval findings + Retro-H operator review + per-finding corrective-action-B children + Q-N classification rubric enforcement (cross-link auto-accept; structural + discipline-preserve surface to operator) + Retro-A classification rubric enforcement (same three classes + auto-accept policy; mirrors Q-N rubric). **Multi-article cards rejected** (one article per invocation; one URL per master ticket).

### Field 2 — Inputs

- **`## Source` block** with `path: x-article-review` + the article URL + author handle + post ID. Required in the master ticket body. Per `methodology/04c-decide-master-ticket.md` Master Title Discipline, the title carries the descriptive topic; the URL/handle/post-id goes in the `## Source` block.
- **`## Verified state`** — the captured output of `python3 ~/.hermes/tools/verify_artifact_state.py <repo> --ticket-id T_xxx` at ticket-filing time. Per op-guard-19, this section is mandatory for every commit/tag/release/push ticket; the x-article-review flow inherits the rule for the loop's amend commits (the master ticket itself is filing-time evidence, not a git-tag ship).
- **Operator authorization** — per the strike-8 emergency-exception protocol, every council-verdict disposition with auto-accept requires operator authorization recorded as `kanban_comment` on the master ticket before the orchestrator's auto-accept fires. The comment references the operator's directive chat timestamp (per `t_653117e5` source quote: *"Let's try it"* = `2026-09-05` chat disposition on Variant A) or the operator's explicit override comment.
- **Q-N classification rubric (this doc, Part 4)** — the rubric is the contract that distinguishes the three classes (cross-link / structural / discipline-preserve); the orchestrator reads the council verdict body + applies the rubric + fires auto-accept OR HITL-A surface.
- **Council verdict body** — emitted by the council-verdict worker per the canonical 4-seat emulation pattern (per `methodology/04a-decide-work-graph.md` Phase 4). The verdict body carries `Q-N` finding rows + ACCEPT / REJECT / DEFER choices per finding. The orchestrator reads the verdict body to classify the Q-N findings.
- **Audit-line cadence** — every auto-accept OR HITL-A surface appends an audit-line per op-guard-11 (vault log.md is append-only).

### Field 3 — Outputs

- **Auto-accept audit line (cross-link class)** — appended to `~/Documents/HermesVault/log.md` per op-guard-11: `## [ISO-TIMESTAMP] auto-accept-council-q — run_id=<N> master=<id> article=<slug> q_count=<N> class=cross-link decision_ref=operator-2026-09-05-auto-accept-variant-a`. Format per Part 4 §Auto-accept policy below.
- **HITL-A ticket (structural + discipline-preserve class)** — filed by the orchestrator per `methodology/04a-decide-work-graph.md` Amendment 7. The HITL-A ticket carries the per-finding disposition table + the pre-populated cross-link table (the cross-link items are auto-accepted; the operator dispositions only the structural + discipline-preserve items).
- **Retro-A findings (per `methodology/06a-decide-retro-v2.md` Amendment 6)** — the 6-axis flow-eval findings + the Retro-A PDF + Discord delivery + audit-line. Retro-A fires after HITL-A passes. The Retro-A worker emits per-finding F-RN rows (each carrying a `cross-link` / `structural` / `discipline-preserve` classification per the Part 4b Retro-A classification rubric).
- **Auto-accept audit line for Retro-A (cross-link class)** — appended to `~/Documents/HermesVault/log.md` per op-guard-11: `## [ISO-TIMESTAMP] auto-accept-retro-a — run_id=<N> master=<id> article=<slug> fr_count=<N> class=cross-link decision_ref=operator-2026-09-05-auto-accept-variant-a`. Format per Part 4b §Auto-accept policy below.
- **Retro-A classification rubric pre-populated table (mixed-class path)** — when at least one F-RN finding classifies as `structural` or `discipline-preserve`, the orchestrator files the Retro-H gate ticket (per `methodology/06a-decide-retro-v2.md` Amendment 6) with the F-RN table pre-populated: the cross-link rows carry `ACCEPT (auto)` + `decision_ref=operator-2026-09-05-auto-accept-variant-a`; the structural + discipline-preserve rows are blank pending operator disposition at the Retro-H gate.
- **Retro-H PDF + Discord DM (per `methodology/06a-decide-retro-v2.md` Amendment 6)** — the operator-facing executive report + the per-finding corrective-action-B children parented to `[retro_h, finding_id]`. Retro-H is the SECOND HITL gate in the flow (after HITL-A); the operator dispositions each Retro-A finding with ACCEPT / DEFER / REJECT / ADDITIONAL.
- **Per-finding corrective-action-B children** — for each Retro-H ACCEPT (or ADDITIONAL-ACCEPT) finding, the orchestrator files a `corrective-action-B` child ticket parented to `[retro_h, finding_id]` (NOT to `[hitl_a, finding_id]` — that edge is reserved for corrective-action-A children per the v0.8.0 amendment 7).
- **Master ticket closed** via `kanban_complete` with the Retro-H disposition URL in `--result` and the per-ticket artifact paths + Retro-A + Retro-H PDF paths + per-finding corrective-action-B child ids in `--metadata`.

### Field 4 — Process (4 phases + 6 gates)

The 4 phases run in order. Each phase is verifier-gated (the gate is the verifier check that runs before the worker advances). The 6 NEW gates in v0.1.0 close the Q-N classification surface (Phase 2 auto-accept OR HITL-A) + the Retro-A + Retro-H HITL gate surface (Phase 3 + Phase 4).

#### Phase 1 — File master + verify URL + intake evidence

The operator files the master ticket on alice-framework board with `path: x-article-review` + `## Source` block carrying the URL + author + post-id. The dispatcher routes to the `x-article-review` loop (per `~/.hermes/loops/hermes.yaml`). The intake worker verifies the URL resolves + captures the article content (per `x-article-review-intent.md` worker_prompt).

#### Phase 2 — Council verdict + Q-N classification + auto-accept OR HITL-A

The council-verdict worker emits the 4-seat emulation verdict + per-finding Q-N findings with ACCEPT / REJECT / DEFER choices. The orchestrator (jarvis lane) applies the Q-N classification rubric (Part 4 below) + fires one of two paths:

- **All-cross-link path (auto-accept):** every Q-N finding classifies as `cross-link`. The orchestrator auto-accepts the verdict, appends the auto-accept audit line per op-guard-11 + decision_ref `operator-2026-09-05-auto-accept-variant-a`, and proceeds directly to Phase 3 (Retro-A).
- **Mixed-class OR all-structural / all-discipline-preserve path (HITL-A):** at least one Q-N finding classifies as `structural` or `discipline-preserve`. The orchestrator files the HITL-A ticket (per `methodology/04a-decide-work-graph.md` Amendment 7) + pre-populates the cross-link items as auto-accepted + surfaces the structural + discipline-preserve items to the operator. Operator dispositions the structural + discipline-preserve items; the cross-link items carry `decision_ref=operator-2026-09-05-auto-accept-variant-a` per the auto-accept policy.

#### Phase 3 — Retro-A 6-axis flow-eval + classification rubric + auto-accept OR Retro-H gate

Per `methodology/06a-decide-retro-v2.md` Amendment 6, Retro-A emits 6-axis flow-eval findings + the Retro-A PDF + Discord DM delivery + audit-line. The Retro-A fires AFTER HITL-A passes (or after the auto-accept completes per the all-cross-link Q-N path). The Retro-A worker is the retro-A worker profile (per the loop's worker_prompt). The Retro-A classification rubric (Part 4b below) gates the Retro-A → Retro-H transition:

- **All-cross-link Retro-A path (auto-accept):** every F-RN finding classifies as `cross-link`. The orchestrator auto-accepts the Retro-A verdict, appends the auto-accept audit line per op-guard-11 + decision_ref `operator-2026-09-05-auto-accept-variant-a`, and proceeds directly to Phase 4 (Retro-H operator review) — Retro-H then fires as an empty-PDF / no-finding-disposition gate (the operator confirms the auto-accept was correct; corrective-action-B children fire from the Q-N path's already-dispositioned findings, not from Retro-A).
- **Mixed-class OR all-structural / all-discipline-preserve Retro-A path (Retro-H gate):** at least one F-RN finding classifies as `structural` or `discipline-preserve`. The orchestrator fires the Retro-H gate ticket with the F-RN table pre-populated: cross-link rows auto-accepted; structural + discipline-preserve rows blank pending operator disposition. Operator dispositions the structural + discipline-preserve items; the cross-link items carry `decision_ref=operator-2026-09-05-auto-accept-variant-a`. The orchestrator does NOT file corrective-action-B children for Retro-A cross-link items (cross-link items do not need corrective action — they preserve audit-trail integrity via the auto-accept); corrective-action-B children fire ONLY from Retro-H ACCEPT (or ADDITIONAL-ACCEPT) findings on structural + discipline-preserve items.

#### Phase 4 — Retro-H operator review + corrective-action-B spawn

Per `methodology/06a-decide-retro-v2.md` Amendment 6, Retro-H is the SECOND HITL gate in the flow. The orchestrator fires the Retro-H PDF + Discord DM delivery (fail-closed to `kind=needs_input` per the Retro-H PDF-gate contract) + the operator dispositions each Retro-A finding with ACCEPT / DEFER / REJECT / ADDITIONAL + the orchestrator files corrective-action-B children for ACCEPT (and ADDITIONAL-ACCEPT) dispositions, parented to `[retro_h, finding_id]`.

### Field 5 — Skill version (canonical-instance reference)

The canonical-instance implementation of this flow is the `x-article-review` loop at `~/.hermes/loops/hermes.yaml`. The loop's worker_prompt + the intent file (`~/.hermes/loops/intents/x-article-review-intent.md`) MUST be amended to reference this doc; the doc is the canonical contract, the loop + intent are the derived instance.

The loop's `status` field is currently `live-pending-test` (per `methodology/M-decide-graph-readiness.md` Part 3 + op-guard-30 enforcement on 2026-08-17). Re-promotion to `live` requires the 9-item per-loop audit to pass — items 1, 2, 5, 6, 8, 9 are the substance gates (item 1 = canonical methodology doc exists + cross-references the loop, item 2 = intent doc mirrored, item 5 = verifier-PASS, item 6 = paired-wiki atomic, item 8 = production run, item 9 = node-type discipline); items 3 + 7 are the promotion gates (item 3 = yaml flip post-PASS, item 7 = operator HITL disposition per op-guard-32).

### Field 6 — Acceptance criteria (verifier-gated)

The 8 acceptance criteria below are the verifier checks on `kanban_complete`. The verifier runs each check; the ticket closes only when all 8 pass.

1. **Master-filing gate (Phase 1)** — the master ticket body carries a `## Source` block with `path: x-article-review` + the article URL + author handle + post ID. Per `methodology/04c-decide-master-ticket.md` Master Title Discipline.
2. **URL-resolves gate (Phase 1)** — the article URL resolves via `curl -I <url>` to a 2xx response (3xx redirects allowed; 4xx / 5xx REFUSED). The verifier captures the response code + the redirect chain in the master ticket body under `## Verified state (Phase 1)`.
3. **Council-verdict gate (Phase 2)** — the council verdict body carries per-finding Q-N rows + ACCEPT / REJECT / DEFER choices per finding. The verdict body is the canonical input to the Q-N classification rubric.
4. **Q-N classification gate (Phase 2)** — every Q-N finding is classified into one of `cross-link` / `structural` / `discipline-preserve` per the rubric in Part 4. The classification table is captured in the master ticket body under `## Q-N classification (Phase 2)`. The path taken (all-cross-link auto-accept OR mixed / all-structural HITL-A) is recorded in the same section.
5. **Auto-accept OR HITL-A gate (Phase 2)** — on the all-cross-link path, the auto-accept audit line exists in `~/Documents/HermesVault/log.md` per op-guard-11 + carries `decision_ref=operator-2026-09-05-auto-accept-variant-a`. On the mixed-class OR all-structural / all-discipline-preserve path, the HITL-A ticket is `done` (per op-guard-10 v2 §1.1) + the operator's per-finding disposition is captured in the HITL-A ticket body.
6. **Retro-A gate (Phase 3)** — the Retro-A PDF exists at `~/Documents/HermesVault/2-ATOMIC/decisions/<article-slug>-retro-a.pdf` + the Discord DM delivery succeeded (per the Retro-A delivery record) + the audit line exists in `log.md`. Per `methodology/06a-decide-retro-v2.md` Amendment 6. Plus the **Retro-A classification gate** (Phase 3, NEW in v0.2.0): every F-RN finding is classified into one of `cross-link` / `structural` / `discipline-preserve` per the Part 4b rubric; on the all-cross-link path the auto-accept audit line exists in `log.md` per op-guard-11 + carries `decision_ref=operator-2026-09-05-auto-accept-variant-a`; on the mixed-class OR all-structural / all-discipline-preserve path the Retro-H ticket body carries the pre-populated F-RN table with cross-link rows pre-dispositioned to `ACCEPT (auto)` + structural + discipline-preserve rows blank.
7. **Retro-H gate (Phase 4)** — the Retro-H PDF exists at `~/Documents/HermesVault/2-ATOMIC/decisions/<article-slug>-retro-h.pdf` + the Discord DM delivery succeeded (fail-closed to `kind=needs_input`) + the operator's per-finding ACCEPT / DEFER / REJECT / ADDITIONAL disposition is captured in the Retro-H ticket body. Per `methodology/06a-decide-retro-v2.md` Amendment 6 + the Retro-H PDF-gate contract.
8. **Corrective-action-B spawn gate (Phase 4)** — for every Retro-H ACCEPT (or ADDITIONAL-ACCEPT) disposition, a `corrective-action-B` child ticket exists with `parents=[retro_h_ticket_id, finding_id]` (NOT `parents=[hitl_a_ticket_id, finding_id]` — that edge is reserved for corrective-action-A children per the v0.8.0 amendment 7 + `methodology/06a-decide-retro-v2.md` Amendment 6). Every `corrective-action-B` child has reached `done` per op-guard-10 v2 §1.1.

A `kanban_complete` call that fails any of the 8 criteria is a verifier-gate failure; the ticket returns to the worker with the failed criteria enumerated in the verifier's `--result`.

## Part 3: Release-message discipline (the cross-flow attribution rule)

This Part is the canonical-source for the topic-only Discord release-message rule. The rule is owned here because the rule is about cross-flow attribution discipline — the article is the input to multiple flows (x-article-review for the operator-facing review, alice-publish for the git-facing release); the release message format must be consistent across them.

The canonical release-message format is:

```
Alice v<X.Y.Z>: <topic-1>, <topic-2>, ...
```

- **Version prefix:** literal `Alice v<X.Y.Z>` (no brackets, no Markdown, no version control tag prefix).
- **Topic list:** one to N comma-separated topic slugs. Topics are the high-level change themes (e.g., `scoped node-admission rule`, `parallel fan-out/fan-in contract`, `Alice graph definition restatement`, `3-question headline rubric`). The topic slugs are operator-facing plain language, not internal jargon.
- **Empty topic list:** when the release contains no thematic changes (e.g., a doc-only patch + CITATION.cff bump), the message becomes `Alice v<X.Y.Z>: documentation-only` (the literal `documentation-only` token).
- **No article attribution:** the message MUST NOT name the article author, the article URL, the source handle, or the post ID. The article is one data point in the methodology's research; the release is Alice's, not the article's. Source: operator Retro-H body observation (verbatim: *"The message on the push should mention what topic was changed but not the article author, the article was just the inspiration for the change."*).
- **No bracketed versions:** the `[v<X.Y.Z>]` form lives in CHANGELOG.md and the GitHub release notes — separate surfaces with their own attribution rules.

**Surface attribution summary (where the article reference does and does not live):**

| Surface | Article reference lives here? |
|---|---|
| Operator-facing Discord release message | **NO** — topic-only. |
| Master ticket body `## Source` block | YES — full URL + author byline + post ID (audit trail). |
| `~/Documents/HermesVault/log.md` audit line | Optional — may cite article URL as the input that motivated the change, but the change itself is described by topic. |
| Methodology amendment rationale | YES — may cite article as one source among several under `## Sources` or per-finding evidence-anchor section. |
| `CHANGELOG.md` `## Source` sub-section | YES — may cite article as one source among several. |
| GitHub release notes `## Source` section | YES — may cite article as one source among several. |
| GitHub release title | NO — the title is `Alice v<X.Y.Z>`. |

**Cross-reference.** `methodology/M-decide-alice-publish-flow.md` Part 2 Field 3 Output 6 is the alice-publish-flow's instance-side rule (the publisher of the format). This Part 3 is the canonical-source for the rule; alice-publish references it as the authority.

## Part 4: Q-N classification rubric + auto-accept policy (added 2026-09-05 per operator directive)

Each council Q-N finding is classified into one of three disposition-classes. The class determines whether operator disposition is required or auto-accept applies.

### Q-N classification rubric

| Class | Definition | Examples | Disposition |
|-------|-----------|----------|-------------|
| `cross-link` | Adds a citation, axiom register, or reference link to existing methodology without changing structure | M-04a citation, M-06 cross-link, operator-facing axiom register | **auto-accept** |
| `structural` | Changes methodology structure: new admission path, new section contract, new gate discipline | Q2 Pawel objective-as-spec, new node-admission path | **operator disposition required** |
| `discipline-preserve` | DECLINE-by-council to preserve evidence/feedback separation or audit-trail discipline | Q5 wandermist evidence/feedback conflate | **operator disposition required** |

The rubric is a classification discipline, NOT a verdict substitution. The council's ACCEPT / REJECT / DEFER choices per finding are unchanged; the rubric is the additional layer that decides whether the operator sees the choice at HITL-A (structural + discipline-preserve surface) or the orchestrator auto-accepts (cross-link). The auditor that runs the rubric is the orchestrator (jarvis lane) reading the council verdict body + applying the rubric table per Q-N finding.

### Auto-accept policy

When all Q-N findings in a council verdict are class `cross-link`, the orchestrator (jarvis lane) auto-accepts and proceeds to Phase 3 (Retro-A) without firing the HITL-A ticket. The audit-line per op-guard-11 captures the auto-accept with `decision_ref=operator-2026-09-05-auto-accept-variant-a`:

```
## [ISO-TIMESTAMP] auto-accept-council-q — run_id=<N> master=<id> article=<slug> \
    q_count=<N> class=cross-link decision_ref=operator-2026-09-05-auto-accept-variant-a
```

The audit-line format carries 6 fields per op-guard-11: ISO-TIMESTAMP, kind (`auto-accept-council-q`), run_id (the council-verdict run id), master (the master ticket id), article (the article slug), q_count (the number of Q-N findings in the verdict), class (the class of the verdict — `cross-link` for the all-cross-link path), and decision_ref (the operator's directive citation: `operator-2026-09-05-auto-accept-variant-a` = the operator's 2026-09-05 *"Let's try it"* directive on Variant A).

### Mixed-class handling

When a verdict has any `structural` or `discipline-preserve` finding, the orchestrator surfaces the HITL-A gate to operator. The operator dispositions the structural + discipline-preserve items; the cross-link items are auto-accepted by the orchestrator's pre-populated table in the HITL-A ticket body.

The HITL-A ticket body carries the per-finding disposition table:

```
| Q-N | Class | Disposition | Operator | decision_ref |
|-----|--------|-------------|----------|--------------|
| Q1  | cross-link | ACCEPT (auto) | (orchestrator) | operator-2026-09-05-auto-accept-variant-a |
| Q2  | structural | <operator choice> | <operator disposition> | <operator decision_ref> |
| Q3  | discipline-preserve | <operator choice> | <operator disposition> | <operator decision_ref> |
```

The cross-link row's Disposition column is pre-populated with `ACCEPT (auto)` + the orchestrator's auto-accept `decision_ref`. The structural + discipline-preserve rows are blank pending operator disposition. The orchestrator does NOT advance to Phase 3 (Retro-A) until the operator's structural + discipline-preserve dispositions land.

### Override

Operator can override any auto-accept decision via:

- `; operator-override-reject: <reason>` prefix on chat message — the chat-side (jarvis lane) intercepts the prefix per op-guard-24 + op-guard-28 and posts a `kanban_comment` on the master ticket with the operator's REJECT + reason; the orchestrator re-runs the auto-accept path with REJECT instead of ACCEPT.
- Direct kanban comment on the master ticket with REJECT + reason — the orchestrator reads the master ticket's `task_comments` for `operator-override-reject` markers and re-runs the auto-accept path accordingly.

The override is the structural escape from the auto-accept policy; it is logged as `decision_ref=operator-override-reject-<ISO-timestamp>` per op-guard-11 + op-guard-10 v2 §1.2 `closed_per_decision` requires `decision_ref`.

## Part 4b: Retro-A finding classification rubric + auto-accept policy (added 2026-09-08 per operator directive "also auto accepting retro items")

Each Retro-A F-RN finding (per `methodology/06a-decide-retro-v2.md` Amendment 6, the 6-axis flow-eval findings emitted in Phase 3) is classified into one of three disposition-classes. The class determines whether operator disposition is required at the Retro-H gate OR auto-accept applies. The Retro-A classification rubric mirrors the Q-N rubric (Part 4 above) with `class=retro-a` substituted for `class=council-q`; the operator authorization decision_ref is the same `operator-2026-09-05-auto-accept-variant-a` token (the directive covers both rubrics per the operator's *"also auto accepting retro items"* directive on 2026-09-05).

### Retro-A classification rubric

| Class | Definition | Examples | Disposition |
|-------|-----------|----------|-------------|
| `cross-link` | Adds a citation, section, or reference to existing record without changing structure | Phase 3 re-classification, bottom-tier sources append, citation additions | **auto-accept** |
| `structural` | Changes flow structure: new gate discipline, contract changes, parent-edge enforcement | F-R2 gate discipline, F-R5 verifier contract auto-V-N re-run | **operator disposition required** |
| `discipline-preserve` | Preserves audit-trail integrity, closes stale ticket states, fixes decision_ref | F-R6 audit-trail cleanup, HITL-A dependency clearing | **operator disposition required** |

The rubric is a classification discipline, NOT a verdict substitution. The Retro-A worker emits per-finding F-RN rows with their own observed-issue + proposed-corrective-action; the rubric is the additional layer that decides whether the operator sees the choice at the Retro-H gate (structural + discipline-preserve surface) or the orchestrator auto-accepts (cross-link). The auditor that runs the rubric is the orchestrator (jarvis lane) reading the Retro-A findings body + applying the rubric table per F-RN finding.

### Auto-accept policy

When all Retro-A F-RN findings in a Retro-A verdict are class `cross-link`, the orchestrator (jarvis lane) auto-accepts and proceeds to Phase 4 (Retro-H operator review) without surfacing the F-RN rows for operator disposition. The audit-line per op-guard-11 captures the auto-accept with `decision_ref=operator-2026-09-05-auto-accept-variant-a`:

```
## [ISO-TIMESTAMP] auto-accept-retro-a — run_id=<N> master=<id> article=<slug> \
    fr_count=<N> class=cross-link decision_ref=operator-2026-09-05-auto-accept-variant-a
```

The audit-line format carries 6 fields per op-guard-11: ISO-TIMESTAMP, kind (`auto-accept-retro-a`), run_id (the Retro-A run id), master (the master ticket id), article (the article slug), fr_count (the number of F-RN findings in the Retro-A verdict), class (the class of the verdict — `cross-link` for the all-cross-link path), and decision_ref (the operator's directive citation: `operator-2026-09-05-auto-accept-variant-a` = the operator's 2026-09-05 *"Let's try it"* + *"also auto accepting retro items"* directives on Variant A).

### Mixed-class handling

When a Retro-A verdict has any `structural` or `discipline-preserve` finding, the orchestrator surfaces the Retro-H gate to operator with the F-RN table pre-populated. The operator dispositions the structural + discipline-preserve items; the cross-link items are auto-accepted by the orchestrator's pre-populated table in the Retro-H ticket body.

The Retro-H ticket body carries the per-F-RN-finding disposition table:

```
| F-RN | Class | Disposition | Operator | decision_ref |
|------|--------|-------------|----------|--------------|
| F-R1 | cross-link | ACCEPT (auto) | (orchestrator) | operator-2026-09-05-auto-accept-variant-a |
| F-R2 | structural | <operator choice> | <operator disposition> | <operator decision_ref> |
| F-R3 | discipline-preserve | <operator choice> | <operator disposition> | <operator decision_ref> |
```

The cross-link row's Disposition column is pre-populated with `ACCEPT (auto)` + the orchestrator's auto-accept `decision_ref`. The structural + discipline-preserve rows are blank pending operator disposition. The orchestrator does NOT advance to corrective-action-B spawn (per Phase 4) until the operator's structural + discipline-preserve dispositions land.

**Corrective-action-B spawn policy:** corrective-action-B children fire ONLY from Retro-H ACCEPT (or ADDITIONAL-ACCEPT) findings on `structural` or `discipline-preserve` items. Retro-A cross-link items do NOT spawn corrective-action-B children — the cross-link class's contract is "preserves audit-trail integrity via the auto-accept"; corrective-action-B would be the wrong shape (it would create work that the auto-accept policy already determined was unnecessary).

### Override

Operator can override any Retro-A auto-accept decision via:

- `; operator-override-reject: <reason>` prefix on chat message — the chat-side (jarvis lane) intercepts the prefix per op-guard-24 + op-guard-28 and posts a `kanban_comment` on the master ticket with the operator's REJECT + reason; the orchestrator re-runs the Retro-A auto-accept path with REJECT instead of ACCEPT.
- Direct kanban comment on the master ticket with REJECT + reason — the orchestrator reads the master ticket's `task_comments` for `operator-override-reject` markers and re-runs the Retro-A auto-accept path accordingly.

The override is the structural escape from the Retro-A auto-accept policy; it is logged as `decision_ref=operator-override-reject-<ISO-timestamp>` per op-guard-11 + op-guard-10 v2 §1.2 `closed_per_decision` requires `decision_ref`.

## Part 5: Worked example — synthetic article review under v0.2.0

Below is a worked example of the 6-field flow spec applied to a synthetic x-article-review under this doc. The example walks through the all-cross-link path (auto-accept) + the mixed-class path (HITL-A surface).

### Goal

Review one synthetic x.com article end-to-end with Q-N classification rubric + auto-accept OR HITL-A disposition per the verdict.

### Inputs

- `## Source` block with `path: x-article-review` + URL `https://x.com/<handle>/status/<id>` + author + post-id.
- `## Verified state` — captured output of `verify_artifact_state.py` at ticket-filing time.
- Operator authorization — `kanban_comment` on the master ticket at intake time citing the operator's 2026-09-05 directive chat timestamp (`t_653117e5` source quote).
- Council verdict body — the 4-seat emulation verdict + per-finding Q-N findings.

### Outputs (all-cross-link Q-N path + all-cross-link Retro-A path)

- Q-N auto-accept audit line in `~/Documents/HermesVault/log.md`: `## [2026-09-05T18:30Z] auto-accept-council-q — run_id=42 master=t_xxx article=<slug> q_count=3 class=cross-link decision_ref=operator-2026-09-05-auto-accept-variant-a`.
- Retro-A auto-accept audit line (NEW in v0.2.0): `## [2026-09-08T15:30Z] auto-accept-retro-a — run_id=43 master=t_xxx article=<slug> fr_count=2 class=cross-link decision_ref=operator-2026-09-05-auto-accept-variant-a`.
- Retro-A PDF + Discord DM delivery + audit-line per Phase 3.
- Retro-H PDF + Discord DM delivery (empty-disposition gate because both Q-N + Retro-A are all-cross-link) + per-finding corrective-action-B children parented to `[retro_h, finding_id]` per Phase 4. Note: in this all-cross-link path, the corrective-action-B children fire ONLY from any Q-N HITL-A ACCEPT (none in this path) or Retro-A structural / discipline-preserve (none in this path) — the all-cross-link path produces zero corrective-action-B children.
- Master ticket closed via `kanban_complete` with the Retro-H disposition URL in `--result`.

### Outputs (mixed-class path)

- HITL-A ticket filed with pre-populated cross-link table + blank structural + discipline-preserve rows.
- Operator's HITL-A disposition captures the structural + discipline-preserve choices + decision_refs.
- Retro-A findings emitted (Phase 3); Retro-A classification rubric applied; F-RN table pre-populated with cross-link rows auto-accepted + structural + discipline-preserve rows blank.
- Retro-H ticket filed with the pre-populated F-RN table (Retro-A cross-link rows + HITL-A cross-link rows both auto-accepted; the operator dispositions only the structural + discipline-preserve rows).
- Operator's Retro-H disposition captures the structural + discipline-preserve choices + decision_refs.
- Corrective-action-B children fire from Retro-H ACCEPT (or ADDITIONAL-ACCEPT) findings on structural + discipline-preserve items (BOTH from Q-N path and Retro-A path — the Retro-H ticket collects both Q-N and Retro-A disposition decisions into one operator surface).
- After Retro-H passes, the master ticket closes via `kanban_complete` with the Retro-H disposition URL in `--result`.

### Process

The worker runs the 4 phases per Part 2 Field 4. Each phase is verifier-gated (the gate is the verifier check that runs before the worker advances). Phase 2's Q-N classification gate (criterion #4) and Phase 3's Retro-A classification gate (criterion #6, NEW in v0.2.0) are the auto-accept OR HITL-A/Retro-H surfaces; the gates fire before the worker advances to the next phase.

### Skill version

- Loop: `~/.hermes/loops/hermes.yaml` `x-article-review` (live-pending-test per op-guard-30).
- Intent: `~/.hermes/loops/intents/x-article-review-intent.md` v0.4.0 (the existing live-pending-test intent; the v0.1.0 instance-side amendment is out-of-scope per op-guard-16 + op-guard-17 alice-first/instance-second; ships via coder follow-up).
- Pre-verify: `~/.hermes/tools/verify_artifact_state.py` v0.1.0 (shipped 2026-08-08 per op-guard-19).

### Acceptance criteria

All 8 criteria from Part 2 Field 6 apply. The verifier runs each criterion; the master ticket closes only when all 8 pass. Criterion #6 (Retro-A gate) includes the NEW v0.2.0 Retro-A classification sub-gate per the Retro-A rubric in Part 4b.

## Part 6: Boundaries

This doc does NOT:

- Modify the x-article-review loop's worker profile assignment (`run_with_profile: planner` for the council-verdict phase + `run_with_profile: jarvis` for the orchestrator phase + `run_with_profile: coder` for the corrective-action-B children). The doc-writer's role is to maintain this methodology doc + the alice-framework CHANGELOG entry. Per op-guard-16, the doc-writer is spec-only and does not mutate `~/.hermes/loops/` files; the instance amendment ships via a follow-up coder ticket per op-guard-17 alice-first/instance-second.
- Migrate any other loop to the Q-N classification rubric OR the Retro-A classification rubric. The v0.2.0 rubric pair (Q-N + Retro-A) is x-article-review-specific; other council-verdict flows (e.g., council methodology) keep their existing HITL-A surfaces. A future amendment may generalize the rubric pair to other council-verdict flows, but that's a separate ticket.
- Bump CITATION.cff. The CITATION.cff operator gate is a structural protection (per `methodology/M-decide-alice-publish-flow.md` Part 2 Field 6 criterion #7); only the operator can authorize a gate re-opening, and the authorization is recorded at publish time, not in this methodology doc. The v0.2.0 doc ships in the alice-framework canonical home without an accompanying release.
- Define the council verdict body schema OR the Retro-A findings body schema. Both are canonical inputs to the classification rubrics; the schemas are defined by `methodology/04a-decide-work-graph.md` Phase 4 + the per-finding ACCEPT / REJECT / DEFER choice discipline (Q-N) and `methodology/06a-decide-retro-v2.md` Amendment 6 + the observed-issue + proposed-corrective-action shape (Retro-A). This doc references the schemas; it does not redefine them.
- Override the operator's per-finding disposition. The orchestrator's auto-accept path applies ONLY to cross-link findings (BOTH Q-N and Retro-A); structural + discipline-preserve findings always surface to the operator via HITL-A or the Retro-H gate regardless of the auto-accept policy. The operator's per-finding disposition is canonical; the orchestrator is the surface that decides which findings surface, not the verdict itself.
- Allow corrective-action-B children to fire from cross-link findings. Per Part 4b §Mixed-class handling, corrective-action-B children fire ONLY from Retro-H ACCEPT (or ADDITIONAL-ACCEPT) findings on `structural` or `discipline-preserve` items. Cross-link items preserve audit-trail integrity via the auto-accept; corrective-action-B is the wrong shape for them.

## Part 7: Maintenance

This methodology doc is the canonical contract for the `x-article-review` flow. Drift in this doc compounds across every article review that runs through this flow; the 60+ article backlog is the operator-action surface that gets drowned if the rubric drifts.

### Audit cadence

Audit this doc every **90 days** from the date it was last reviewed. The audit walks the 4 checks below; the cadence is a calendar event, not "when I remember."

**When to audit sooner than 90 days:**

- An auto-accept fires (Q-N OR Retro-A) and the operator surfaces an override rejection (the auto-accept policy missed a structural / discipline-preserve class).
- A HITL-A surface fires with no operator disposition (the operator-action surface drowned).
- A Retro-A or Retro-H PDF fails to deliver (the fail-closed to `kind=needs_input` path fails).
- A corrective-action-B child ticket is filed with the wrong parent edge (e.g., `parents=[hitl_a, finding_id]` instead of `parents=[retro_h, finding_id]`) OR is filed from a cross-link F-RN finding (per Part 4b §Mixed-class handling).
- A new council-verdict flow is added that emits Q-N findings and the rubric is not generalized to it.
- A new retro phase is added that emits F-RN findings and the Retro-A rubric is not generalized to it.
- A new tool, agent, or routing convention changes which gates the dispatcher can satisfy.
- A council review or retro names this doc as a contributing cause of a Q-N or Retro-A misclassification.

An audit-on-failure is **added**, not substituted — the 90-day cadence still runs for this doc.

### Quality threshold

This doc passes the maintenance check when **all 6 fields are present, the goal field is one sentence, the 4 phases + 6 gates are documented as load-bearing steps, the Part 4 Q-N classification rubric table is unambiguous (no overlapping classes), the Part 4 auto-accept policy is fail-closed (decision_ref is mandatory), the Part 4b Retro-A classification rubric table is unambiguous (mirrors Part 4), the Part 4b auto-accept policy is fail-closed (decision_ref is mandatory), AND the Part 6 boundaries section names every flow this doc does NOT modify**. The threshold is binary per field; the doc is acceptable when every field passes its own check.

## Part 8: Versioning

### v0.2.0 (2026-09-08, t_003a07e2)

Extends the v0.1.0 Variant A auto-accept rubric pattern to the Retro-A 6-axis flow-eval findings (per `methodology/06a-decide-retro-v2.md` Amendment 6). Adds Part 4b — the Retro-A classification rubric (cross-link / structural / discipline-preserve classes) — mirroring the Part 4 Q-N rubric verbatim with `class=retro-a` substituted for `class=council-q` + the auto-accept policy for the cross-link class + the mixed-class handling (Retro-A cross-link items pre-populated in the Retro-H ticket body) + the operator-override escape. Per the operator's 2026-09-05 *"also auto accepting retro items"* directive (chat), the operator authorization decision_ref is the same `operator-2026-09-05-auto-accept-variant-a` token. Additive feature (new classification layer on Retro-A side; no contract change to Q-N rubric); MINOR version bump per semver. Frontmatter: version 0.1.0 → 0.2.0; `amended_by` extended with `[[ticket:t_003a07e2]]`; `updated:` field bumped to 2026-09-08T15:30Z. Updates Part 1 (Purpose) to add Retro-A rubric to the contract list; Part 2 Field 1 (Goal) to add Retro-A classification rubric enforcement; Part 2 Field 3 (Outputs) to add the Retro-A auto-accept audit line + the Retro-A pre-populated Retro-H table; Part 2 Field 4 (Phase 3) to extend the Retro-A phase with the classification rubric + the auto-accept OR Retro-H gate paths; Part 2 Field 6 (criterion 6) to add the NEW Retro-A classification sub-gate; Part 5 (worked example) to walk through the all-cross-link Q-N + all-cross-link Retro-A path + the mixed-class path; Part 6 (Boundaries) to clarify the corrective-action-B spawn restriction (cross-link items do NOT spawn corrective-action-B); Part 7 (Maintenance) to add the cross-link-spawn and rubric-pair audit triggers; Part 7 quality threshold to add Part 4b unambiguity + fail-closed contract. The companion intent-doc amendment ships via coder follow-up per op-guard-16 spec-first-flow + op-guard-17 alice-first / instance-second / compliance-gate.

### v0.1.0 (2026-09-05, t_653117e5)

Initial canonical-promote. Codifies the Q-N classification rubric (cross-link / structural / discipline-preserve classes) + the auto-accept policy for the cross-link class + the mixed-class handling + the operator-override escape. Ships the 6-field flow spec + Part 3 (release-message discipline) + Part 4 (rubric) + Part 5 (worked example) + Part 6 (boundaries) + Part 7 (maintenance). No companion intent-doc amendment (out of scope per op-guard-16 + op-guard-17); the existing `~/.hermes/loops/intents/x-article-review-intent.md` v0.4.0 (live-pending-test) is the derived instance that conforms to this doc on the next audit cycle. Frontmatter: version 0.1.0; alice-ticket: t_653117e5; parent-meta: t_653117e5; amended_by: [t_653117e5]. Source: operator 2026-09-05 *"Let's try it"* directive on Variant A (the all-cross-link auto-accept policy + the operator disposition surface for structural + discipline-preserve findings). Companion intent-doc amendment ships via coder follow-up per op-guard-16 spec-first-flow + op-guard-17 alice-first / instance-second / compliance-gate.

## Cross-references

- `methodology/04d-decide-flow-spec.md` — canonical 6-field flow spec; this doc adapts the pattern to the x-article-review flow.
- `methodology/04a-decide-work-graph.md` — node-type taxonomy + Phase 4 council verdict + Amendment 7 HITL-A gate + Amendment 8 Retro-H gate.
- `methodology/04c-decide-master-ticket.md` — Master Title Discipline (the title-shape contract for x-article-review masters).
- `methodology/M-decide-instance-vs-framework.md` — framework-vs-instance distinction rule.
- `methodology/M-decide-graph-readiness.md` — 9-item per-loop audit rubric; items 1 + 2 + 5 + 6 + 8 + 9 are the substance gates.
- `methodology/M-decide-alice-publish-flow.md` — sibling flow (git-facing; uses the 7-step publish pipeline + GitHub release).
- `methodology/M-decide-graph-audit-promotion-flow.md` — sibling flow (the canonical 2-HITL audit + promotion cycle for the loop registry).
- `methodology/06a-decide-retro-v2.md` — Retro-A + Retro-H + per-finding corrective-action-B children pattern.
- `references/instance-leak-check-spec.md` — H1-H5 + X1 pattern catalog (for the alice-framework instance-leak check at publish time).
- `2-ATOMIC/rules/op-guard-16-spec-first-flow-2026-08-05.md` — doc-writer is spec-only; the canonical-writer's output is the document.
- `2-ATOMIC/rules/op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md` — alice-first / instance-second / compliance-gate ordering; this doc ships before the instance-side amendment.
- `2-ATOMIC/rules/op-guard-19-pre-verify-artifact-state-2026-08-08.md` — pre-verify before commit/tag/release/push filings.
- `2-ATOMIC/rules/op-guard-24-clarify-tool-discord-render-pattern-2026-08-09.md` — canonical `clarify` format (descriptive text in `question` body, short labels in `choices`).
- `2-ATOMIC/rules/op-guard-29-per-loop-hitl-gate-2026-08-15.md` — per-loop HITL gate discipline (PROMOTE / DEFER / ARCHIVE); the 24h skip rule.
- `2-ATOMIC/rules/op-guard-32-four-stage-graph-hitl-2026-08-17.md` — 4-stage HITL pattern (scope/intent → intent-achievement → compliance → disposition); the gate-order enforcement.
- `~/.hermes/loops/hermes.yaml` `x-article-review` loop — the canonical-instance implementation.
- `~/.hermes/loops/intents/x-article-review-intent.md` v0.4.0 — the canonical-instance intent doc (the derived instance; conforms to this doc on the next audit cycle).
- `~/Documents/HermesVault/2-ATOMIC/concepts/alice-oss-prep-card-h-canonical-terms.md` — canonical-terms concept reference.
- `t_653117e5` — the source META ticket (operator directive on Variant A + the prior filesystem wedge that surfaced the body-content gap).
- `t_9d92f28e1d8f4b6d` — the jarvis probe ticket that independently re-verified the filesystem wedge + surfaced the body-content gap (per the prior doc-writer run history on `t_653117e5`).
- Source: operator 2026-09-05 *"Let's try it"* directive on Variant A.
- `t_003a07e2` — the source META ticket (the operator directive on extending Variant A auto-accept to Retro-A findings + the partial-compliance gap on the v0.1.0 META).
