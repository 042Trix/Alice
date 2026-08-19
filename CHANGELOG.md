---
id: alice-changelog
created: 2026-08-04T15:30:00Z
updated: 2026-08-18T19:00:00Z
title: "Alice — Changelog"
type: framework-changelog
status: draft
source: operator
tags: [kind:framework, kind:changelog, domain:agent-ops, project:alice]
links: []
teaching-example: true
---

# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

|> **Note on this `0.1.0` entry:** This is the **changelog stub**, not a release announcement. The entry below names what exists in the repo at the time of writing, and it will be revised at tag time to reflect the final packaging artifacts. The first public release ships only after every box in the OSS prep plan's release-readiness checklist is checkable. See `2-ATOMIC/concepts/alice-oss-prep-plan-2026-08-04.md`.

## [Unreleased]

### Added
- methodology/M-decide-graph-readiness.md — v0.2.5 → v0.2.6 (v0.2.0 documentation amendments: item-7 4-stage HITL sub-section + score presentation canonicalization, per council synthesis 2026-08-18 / t_e27e096b amendment #5 + #6; this ticket t_5e787e09, amend-71). Amendment A adds a dedicated `#### 4-stage HITL structure (item 7)` sub-section after the item-7 row naming all 4 sub-gates (HITL #1 — scope/intent, HITL #2 — intent-achievement, HITL #3 — compliance, HITL #4 — disposition) with their canonical choices; explains why 4 stages (per op-guard-32, collapsing to a single yes/no hides intent-achievement failures); references the 24h re-prompt rule (per op-guard-29) with the per-loop disposition cache path (`loop_dispositions.json`) and the per-gate clock qualifier; and lists the skip conditions per op-guard-32. Amendment B tightens the `audit score after v0.1.2` section (Part 3) to use the canonical phrasing "6/6 verifier + 1 HITL + 1 flip" per Seat-5 (Adversarial) verdict; the previous "6 + 1 + 1 = 8 gate-events" / "9/9" framings are now explicitly marked as misleading with the `do NOT use "8/8" or "9/9"` marker. The 4-case regression suite `~/.hermes/tests/test_graph_readiness_rubric_v0_2_0.py` (TestItem7SubSection: 6 cases; Test24hRePromptRule: 4 cases; TestScorePresentation: 5 cases; TestChangelogEntry: 4 cases = 19 green) verifies the v0.2.6 contract is enforceable. Frontmatter version bumped v0.2.5 → v0.2.6; `updated:` field bumped 2026-08-18T19:00:00Z → 19:10:00Z; `amended_by` extended with `[[ticket:t_5e787e09]]`. Source: t_5e787e09 (this ticket; per META spec); cross-references t_e27e096b (council verdict) + t_ceb43505 (v0.2.0 item-7 prose amendment) + t_5581cb91 (v0.2.0 score-presentation prose amendment) + t_29b4c19c (op-guard-32 4-stage HITL rule) + t_73964a16 (op-guard-29 per-loop HITL gate + 24h re-prompt rule). The version bump lands at v0.2.6 (NOT v0.2.5) because v0.2.5 was already taken by t_047980f8 (alice-publish re-audit history amendment, this morning). Paired-wiki: intra-doc amendments to M-decide-graph-readiness.md only (Part 3 item-7 table row + score section + new sub-section); no companion docs require paired-wiki amendment.
- methodology/M-decide-alice-publish-flow.md (v1.0.0, 70 KB) — canonical-promote of the alice-publish flow spec from instance-side (`~/.hermes/methodology/M-decide-alice-publish-flow.md`) to its canonical alice-framework home. Adds STEP 1 (instance-leak gate via `check_instance_leaks.py`; closes the framework-vs-instance leak surface from `t_7aa96032`); adds Part 7 (node-type discipline per `methodology/04a-decide-graph-readiness.md` Part 3 item 9 + `methodology/04a-decide-work-graph.md` Part 4; every graph node named with type + rationale); expands Process to 7 steps + 6 gates; expands Field 6 acceptance criteria to 11 items; `teaching-example: true` opt-out applied per `references/instance-leak-check-spec.md` Part 2 (the doc legitimately references the instance-side loop + intent + tools as the derived instance; H2/H3/H4 patterns are suppressed). The per-loop audit (`methodology/M-decide-graph-readiness.md` Part 3) flips item 1 from ❌ to ✅ (canonical methodology doc exists + cross-references the loop) and item 9 from ❌ to ✅ (node-type discipline phrase present). Audit score: 3/9 → 7/9 (remaining gaps: item 3 status-field flip requires operator HITL gate per `op-guard-29`; item 7 operator-review of a production run). Source: META `t_55240868` (alice-publish demotion per `op-guard-30`) + child `t_f38bd852` (this doc's canonical-promote ticket). Follow-ups flagged: instance-side pointer update + alice-publish loop yaml comment block + intent-doc `amended_by_v0_2_0_methodology_doc` field (out of scope per `op-guard-16` + `op-guard-17` alice-first/instance-second; ship via coder follow-up).
- methodology/M-decide-proactive-board-monitoring.md (v0.1.0) — canonical rule for proactive multi-board monitoring (board-allow-list + chat-side preflight + auto-act policy by block-kind + operator-action surface). Source: `t_593d077e` (verified case: 4 alice-framework tickets blocked 30+ min; cron never flagged because `alice-framework` was missing from `_stuck_detect_common.py::BOARDS`).
- methodology/M-decide-graph-readiness.md (v0.1.1) — 9-item per-loop promotion rubric (Part 3) + product-ready graph inventory (Part 4) + 8-check verification discipline (Part 5). Adds item 9 (node-type discipline per `04a-decide-work-graph.md` Part 4) + check 8 (walk the graph's design doc, confirm each node's type + rationale). Source: `t_fe0ad797`.
- methodology/M-decide-skill-curator.md (v0.1.0) — canonical operator-environment invariant the `skill-curator` loop preserves (size budget + duplicate detection + protected skill bodies). Source: `t_ab1f3081`.
- methodology/M-decide-wiki-lint.md (v0.1.0) — canonical methodology for the wiki-lint loop (board-drift detector + paired-wiki integrity check + 90-day audit cadence). Source: `t_7419d73e`.
- methodology/M-decide-graph-audit-promotion-flow.md (v0.1.0, 219 lines) — canonical flow spec for the graph-audit-promotion cycle. Codifies the 6-field flow spec (per `methodology/04d-decide-flow-spec.md`) + the 3-node-type topology (Scripts/Code + Agents + Human, per `methodology/04a-decide-work-graph.md` Part 4) + the 4-stage operator HITL gates (per `op-guard-32`: scope/intent → intent-achievement → compliance → disposition) + per-loop action (PROMOTE per `op-guard-30` evidence + compliance-verifier chain; DEFER per-loop deferral ticket with `closed_per_decision`; ARCHIVE per-loop archive ticket). The per-loop audit (`methodology/M-decide-graph-readiness.md` Part 3) flips item 1 from ❌ to ✅ for the `graph-audit-promotion` loop (canonical methodology doc exists + cross-references the loop). Source: META `t_3b49e1bc` (the `graph-audit-promotion` loop's instance-side wiring) + child `t_0e7b941a` (the alice-framework paired-wiki commit). Companion docs get back-links per op-guard-5 paired-wiki integrity: `methodology/M-decide-instance-vs-framework.md` (links field + amended_by), `methodology/04a-decide-work-graph.md` (links field). The 3 companions not in scope of this commit (`methodology/04c-decide-master-ticket.md`, `methodology/04d-decide-flow-spec.md`, `methodology/M-decide-spec-first-flow.md`) ship as a follow-up paired-wiki ticket.
- methodology/M-decide-graph-readiness.md — v0.1.1 → v0.1.2 (verifier/promotion gate split; per `t_a61e5611`). Part 3 rubric split into 6 verifier gates (items 1, 2, 4, 6, 8, 9 — what the verifier evaluates for correctness) + 3 promotion gates (items 3 = yaml flip post-PASS, 5 = verifier-PASS itself, 7 = operator HITL disposition). Resolves three structural defects in v0.1.1: (a) item 3 was a circular verifier gate ("yaml is `live`" can never be true before the flip); (b) item 5 was a chicken/egg verifier gate ("verifier PASS" requires passing); (c) item 7 forced the operator to see ≤8/9 because the rubric couldn't include their own disposition. Audit score reframed as "6/6 verifier gates + 1 operator HITL + 1 yaml flip"; the verifier only walks the 6 verifier gates per Part 5 checks. Part 2 adds "Promotion is a two-act play: correctness first, then transition" subsection. Part 3 caption explicitly labels Group A (verifier gates) + Group B (promotion gates); the canonical promotion procedure (verifier → operator HITL → yaml flip) becomes the contract. Part 5's verdict clause now explicitly says "the verifier never evaluates items 3, 7" — item 3 is a post-flip surface; item 7 is the operator's decision. Part 4 §D adds op-guard-30 + op-guard-32 to discipline gates. Part 6 application procedure updated to the 7-step canonical order (verifier → operator HITL → yaml flip). Part 7 backwards-compat: alice-publish grandfathered under v0.1.1; live-pending-test loops re-audit under v0.1.2. Source: operator quote 2026-08-18 *"it sounds like we need to edit the list of 9 gates required to be live. YAML status is just the status, it will always fail until it goes live and prevents a 9/9. Same for operator sign-off, which is just a HITL gate for reaching the YAML flip. The Verifier gate looks like it is just counting the gates and is a chicken/egg scenario. The verifier should be looking at all the other gates (excluding HITL and YAML) to confirm that their items were completed CORRECTLY"*. Paired-wiki bump: `M-decide-graph-audit-promotion-flow.md` Part 8 cross-reference line carries an inline note that the verifier node walks the 6 verifier gates only (not items 3, 5, 7); deferred to a follow-up paired-wiki ticket per op-guard-5.

### Changed
- methodology/M-decide-instance-vs-framework.md — v0.1.0 → v0.1.1 (paired-wiki amendment for `M-decide-graph-audit-promotion-flow.md` v0.1.0; `links:` field adds `[[methodology/M-decide-graph-audit-promotion-flow.md]]` + `amended_by` extended with `t_0e7b941a`; op-guard-5 paired-wiki integrity discipline). The framework-vs-instance distinction rule codifies that the flow spec is framework-side; the loop entry + intent + tests are instance-side. No contract change to the framework-vs-instance distinction rule itself.
- methodology/04a-decide-work-graph.md — v0.1.4 → v0.1.5 (paired-wiki amendment for `M-decide-graph-audit-promotion-flow.md` v0.1.0; `links:` field adds `[[methodology/M-decide-graph-audit-promotion-flow.md]]` + `amended_by` extended with `t_0e7b941a`; op-guard-5 paired-wiki integrity discipline). The 3-node-type taxonomy (Part 4) is the substance the flow conforms to. No contract change to the work-graph state machine.
- methodology/M-decide-graph-readiness.md — v0.1.1 → v0.1.1 (paired-wiki back-link to `M-decide-graph-audit-promotion-flow.md` is already in the `links:` field per the v0.1.1 amendment shipped via `t_fe0ad797`; the v0.1.1 rubric + verification discipline is the substance the flow operationalizes). No contract change to the 9-item rubric; the doc-writer pre-baked the link in this turn.
- methodology/M-decide-graph-readiness.md — v0.1.2 → v0.2.0 (item-7 4-stage HITL amendment, per op-guard-32; source `t_ceb43505`). The rubric's "What it checks" column for item 7 (Operator HITL, promotion gate — pre-flip) now names the 4 sub-gates per op-guard-32: HITL #1 — scope/intent (CONFIRM / REVISE / ABANDON); HITL #2 — intent-achievement (ACHIEVES / PARTIALLY-ACHIEVES / DOES-NOT-ACHIEVE); HITL #3 — compliance (CONFIRM / DISPUTE / EXEMPTION); HITL #4 — disposition (PROMOTE / DEFER / ARCHIVE). Item 7 is now satisfied only when all 4 sub-gates fire (or skip-conditions per op-guard-32). Part 2's "Promotion is a two-act play" subsection adds a sentence explaining the 4-stage HITL and why collapsing it to a single yes/no hides intent-achievement failures. The promotion procedure (Part 3) and verification discipline (Part 5) already named the 4 stages; this amendment propagates the structure into the rubric table itself so future auditors cannot collapse the operator's review to a single yes/no. Frontmatter version bumped v0.1.2 → v0.2.0; `updated:` field bumped 2026-08-18T07:00Z → 16:00Z; `amended_by` extended with `[[ticket:t_ceb43505]]`. Source: council synthesis 2026-08-18 (`t_e27e096b`) amendment #5 (item-7 structure); op-guard-32 rule file `2-ATOMIC/rules/op-guard-32-four-stage-graph-hitl-2026-08-17.md`.
- methodology/M-decide-graph-readiness.md — v0.2.0 (item-6 atomicity sub-check amendment, per op-guard-5; source `t_36697869`). The rubric's "Source of truth" column for item 6 (Paired-wiki integrity) now requires that the change set is **atomic** — all four artifacts (methodology doc + intent doc + yaml `status:` flip + regression suite updates) appear in the same commit (or explicitly chained commits within the same PR / change set, with no intermediate state where one ships without the other). Two commits in close succession can look like "paired changes" in git history but are not atomic; a race condition (e.g., the loop's yaml entry ships first while the methodology doc ships second) could land partial changes that pass the paired check but fail the atomicity check. The op-guard-5 paired-wiki integrity is the **existence check**; atomicity is the **correctness check**. Part 4 §A "atomic for promotion" sentence tightened: the change set MUST be a single commit (or atomic PR); intermediate states are NOT acceptable; the verifier confirms via `git log --format=%H --grep='<change set name>' -10` that no intermediate commit existed. Part 5 check 4 (worker spawn + run + complete cycle) gains a sub-clause **(d) atomicity** — `git log` + `git show <commit> --stat` confirms all four artifacts in the same commit. No frontmatter version bump (the v0.2.0 label already happened in amendment #5 / `t_ceb43505`; this is an additive refinement within v0.2.0); `updated:` field bumped 2026-08-18T16:00Z → 17:00Z; `amended_by` extended with `[[ticket:t_36697869]]`. Source: council synthesis 2026-08-18 (`t_e27e096b`) amendment #3 (item-6 atomicity, MEDIUM priority); op-guard-5 rule file `2-ATOMIC/rules/op-guard-5-wiki-update-2026-07-21.md`.
- methodology/M-decide-graph-readiness.md — v0.2.0 → v0.2.1 (item-1 doc-accurately-describes-behavior sub-check amendment; source `t_fc737daa`). The rubric's "Source of truth" column for item 1 (Methodology doc exists + cross-references the loop) now requires that the methodology doc **accurately describes the work-graph's current behavior** — the verifier reads the doc + walks the actual graph (master ticket + children + dispatcher routing + verifier chain); a methodology doc that exists + cross-references the loop but is STALE (its per-node descriptions no longer match the actual child tickets' worker profiles / scopes / outcomes) FAILS this gate. Paired-wiki integrity (op-guard-5) confirms the doc is on disk + cross-references the loop; this sub-check confirms the doc is CORRECT — the substance matches the form. A doc that exists + cross-references the loop but describes a different graph is a documentation convention, not a `live`-readiness gate. Part 4 §A "Documentation triad" gains a new paragraph on **internal consistency** — each artifact (methodology + intent + yaml + tests) must agree on what the graph does; methodology describes intent, intent describes yaml, yaml describes tests, tests assert behavior the methodology describes; the verifier walks all four documents and confirms node counts, names, worker profiles, and outcomes agree. Part 5 check 3 (master ticket's children are filed correctly) gains sub-clause **(d) behavior-vs-doc walk** — for each child ticket, the methodology doc's per-node description matches the actual child ticket's worker profile + scope + outcome; a mismatch FAILS. Frontmatter version bumped v0.2.0 → v0.2.1 (the amendment-priority ordering: amend-5 + amend-3 already landed at v0.2.0; amend-2 / item-1 sub-check is the next MINOR increment); `updated:` field bumped 2026-08-18T17:00Z → 17:30Z; `amended_by` extended with `[[ticket:t_fc737daa]]`. Source: council synthesis 2026-08-18 (`t_e27e096b`) amendment #2 (item-1 doc-accurately-describes-behavior, MEDIUM priority); op-guard-5 rule file `2-ATOMIC/rules/op-guard-5-wiki-update-2026-07-21.md` (the existence check this amendment's correctness check backs); ticket body rationale: *"A methodology doc that exists + cross-references the loop passes the current item 1. A methodology doc that exists + cross-references the loop + is WRONG still passes. The verifier-side check is currently 'doc exists' (mechanical), not 'doc is correct' (substantive)."*

- methodology/M-decide-graph-readiness.md — score presentation tightening (amendment #6; source `t_5581cb91`). Part 3 Group A caption now names the three preconditions for promotion in a single sentence: "(1) all 6 verifier gates are correct (verifier PASS recorded per item 5); (2) the operator dispositions all 4 HITL sub-gates per op-guard-32 (item 7); (3) the system flips the yaml `status:` field to `live` (item 3)." The "audit score after v0.1.2" subsection is rewritten as one paragraph that explicitly states the score is **`6 + 1 + 1 = 8` gate-events, NOT `8/8` or `9/9`** — 6/6 verifier gates + 1 operator HITL + 1 yaml flip — so future score presentations cannot confuse operators about what each gate actually checks. A loop with 6/6 verifier gates + 1 operator HITL dispositioned PROMOTE can still be at `live-pending-test` until the yaml flip fires; the flip is the transition; the score is the state. No frontmatter version bump (the v0.2.x amendment line continues; `amended_by` extended with `[[ticket:t_5581cb91]]`); `updated:` field bumped to the 2026-08-18T11:15Z window. The historical references to "9/9" in the v0.1.2 / v0.1.1 → v0.1.2 retrospective (Part 3 "Why this split matters" sub-section + the v0.1.2 audit-line entry) are preserved as historical context — they describe what the rubric WAS, not what it is now. Source: council synthesis 2026-08-18 (`t_e27e096b`) amendment #6 (score presentation; documentation amendment, non-structural).
- methodology/M-decide-graph-readiness.md — v0.2.4 → v0.2.5 (alice-publish re-audit history + Part 7.1 v0.2.0 verdict; per `2-ATOMIC/cross-agent/2026-08-18_council-rubric-refinement-validation.md` open-questions §3 + operator disposition ACCEPT; source `t_047980f8`). Part 7 backwards-compat bullet rewritten: `alice-publish` `live` status now annotated as historical-only (the 2026-08-11 promotion per `t_b0d6dd79`; grandfathered under v0.1.1) with the 2026-08-17 demote (per META `t_55240868` op-guard-30 enforcement) now documented in-line; the 10-loop live-pending-test roster (was 9) explicitly references post-v0.2.0 promotions MUST satisfy the 6 verifier gates + 4 sub-checks. NEW Part 7.1 (alice-publish re-audit history under v0.1.2 + v0.2.0) documents the canonical per-loop trace: 3-row history table (2026-08-11 live historical / 2026-08-17 11:15 demote / 2026-08-18 19:00 re-audit) + an item-by-item v0.2.0 verdict block walking all 9 items + the 4 sub-checks (item-1 doc-accurately-describes-behavior per `t_fc737daa`; item-4 behavior-vs-tautology per `t_63385722`; item-6 atomicity per `t_36697869`; item-8 run-success per `t_cdbf1575`). Re-audit verdict: PARTIAL-PASS under v0.2.0 (items 1, 2, 5, 6, 8, 9 ✅ at substance level; item 4 has a stale-assertion bug — 1 stale gate-blocker test of 20 in `test_alice_publish_compliance.py`; 4 sub-checks all PASS at substance level; promotion gates items 3 + 7 remain). Follow-up work items enumerated: coder ticket for the item-4 stale-assertion fix + verifier child re-confirming item 5 (per op-guard-17) + planner/jarvis ticket firing the 4-stage operator HITL per op-guard-32 + coder ticket flipping `status: live` ONLY after items 1+2 land (per op-guard-30 substance-claim discipline); bulk-promotion META on `loop-builder` board is the canonical routing target per Part 6. Doc-writer does NOT file any env-mutation tickets per op-guard-16 + op-guard-21; the doc is the contract, the instance work is the implementation per op-guard-17. Frontmatter version bumped v0.2.4 → v0.2.5; `updated:` field bumped 2026-08-18T11:40:00Z → 19:00:00Z; `amended_by` extended with `[[ticket:t_047980f8]]`. Paired-wiki: the amendment is intra-doc to M-decide-graph-readiness.md; the only companion-doc-mirror is this CHANGELOG.md entry. Source: t_047980f8 (the canonical META ticket for this re-audit); cross-references the council verdict (t_e27e096b, ACCEPT recommendation 6) + the demote META (t_55240868) + the historical promotion META (t_b0d6dd79) + the 2026-08-17 compliance-verifier PASS (t_e1867cd4) + the 4 v0.2.0 sub-check amendment tickets (t_cdbf1575, t_fc737daa, t_63385722, t_36697869).

### Changed (prior-session paired-wiki integrity amendments)
- methodology/M-decide-instance-vs-framework.md — v0.1.0 (paired-wiki amendment for `M-decide-alice-publish-flow.md` v1.0.0; the `links:` field adds `[[methodology/M-decide-alice-publish-flow.md]]` + the `companion:` field gets the alice-publish entry + `amended_by` extended with `t_f38bd852`; op-guard-5 paired-wiki integrity discipline). No contract change to the framework-vs-instance distinction rule.
- methodology/M-decide-graph-readiness.md — v0.1.1 (paired-wiki amendment for `M-decide-alice-publish-flow.md` v1.0.0; `links:` field adds `[[methodology/M-decide-alice-publish-flow.md]]` as a cross-reference for the item-1 audit check + `amended_by` extended with `t_f38bd852`; op-guard-5 paired-wiki integrity discipline). No contract change to the 9-item rubric.
- methodology/00-decide-ticket-naming.md — v0.4.4 → v0.4.5 (paired-wiki amendment for `M-decide-proactive-board-monitoring.md` v0.1.0; op-guard-5 paired-wiki integrity discipline). No contract change to 00 itself.
- methodology/00-decide-ticket-naming.md — v0.4.5 → v0.4.6 (paired-wiki amendment for `M-decide-alice-publish-flow.md` v1.0.0; `links:` field adds `[[methodology/M-decide-alice-publish-flow.md]]` + `amended_by` extended with `t_f38bd852`; op-guard-5 paired-wiki integrity). No contract change to 00 itself.
Source: council synthesis 2026-08-18 (`t_e27e096b`) amendment #6 (score presentation; documentation amendment, non-structural).
- methodology/M-decide-graph-readiness.md — v0.2.2 (item-8 run-success sub-check amendment, per council 2026-08-18 / `t_e27e096b`; source `t_cdbf1575`, amend-4 MEDIUM priority). The rubric's "Source of truth" column for item 8 (Loop used end-to-end) now requires that the run's terminal outcome is `done` per `kanban show <loop_run_card>` — not `blocked`, not `running-then-blocked`, not `running-then-cancelled`; **substance over label**: a `result=success` audit-line paired with a `blocked` terminal status does NOT satisfy item 8. New no-storm / no-escalation discipline: the card's event log shows < 3 retries (>=3 in a 5-minute window signals a retry storm); the audit-line log shows no `operator-action-dm` triggered by this run (per `op-guard-13`). A run that completed via `kanban_block` does NOT satisfy item 8 (the operator disposition is acceptable evidence for op-guard-30's evidence/exemptions but NOT for item 8). Part 5 check 2 (Trigger the loop via dispatcher) gains sub-clauses **(a)/(b)/(c)** — sub-clause (c) verifies the terminal outcome is `done`, the retry-storm threshold, and the escalation-dm count; on item-8 failure the verifier returns `compliance: fail — item-8-failed-run — <loop_id> v<X.Y.Z>: terminal=<status> after <N> retries, <M> escalation-dm events` and the loop must be re-fired with the failure-mode fix before re-audit. Part 4 §C Operational contract gains the **Error handling discipline** bullet — a `live` loop must demonstrate successful completion of at least one end-to-end run; a loop with only failed runs is not `live`-ready; the loop's failure modes (retry storms, escalations, kanban_block outcomes) must be resolved before the run counts toward item 8. Frontmatter version bumped v0.2.1 → v0.2.2 (additive refinement within v0.2.x); `updated:` field bumped to 2026-08-18T11:15:22Z; `amended_by` extended with `[[ticket:t_cdbf1575]]`. Paired-wiki: Part 3 item 8 + Part 4 §C + Part 5 check 2 are intra-doc updates within M-decide-graph-readiness.md itself; no companion doc requires paired-wiki amendment. The related instance-side verifier check (the verifier profile implementing Part 5 check 2c procedurally) belongs to a follow-up coder ticket per op-guard-17 (alice-first / instance-second / compliance-gate). Source: council synthesis 2026-08-18 (`t_e27e096b`) amendment #4 (item-8 run-success; structural amendment, MEDIUM priority).

- methodology/M-decide-graph-readiness.md — v0.2.3 (item-4 behavior-vs-tautology sub-check amendment, per council synthesis 2026-08-18 / `t_e27e096b`; source `t_63385722`, amend-1 HIGHEST priority). The 5/5-seat council synthesis 2026-08-18 rated the item-4 sub-check as the HIGHEST priority structural follow-up — the canonical production-disaster pattern is a `live` label backed by tautological tests (e.g., `assert loop_status == "live"` after the flip) that pass the green surface but fail to verify the spec. The rubric's "Source of truth" column for item 4 (Regression suite exists + green) now requires that the regression suite assert **observable behavior**, not implementation tautologies — at least one test per regression suite asserts behavior visible from the operator's chat (a master ticket filed on the right board, a child ticket with the right body, a Discord DM delivered, a kanban comment posted, an audit-line appended). The companion verification discipline is Part 5 check 1 (a) (b) (c) — (a) all test cases exit 0; (b) at least one test asserts behavior visible from the operator's chat; (c) no test asserts only the implementation surface (no `assert yaml_status == "live"` after the flip without an end-to-end behavior check; no `assert dispatcher.is_pinned() == True` without a follow-on assertion that the dispatcher's child actually ran). A regression suite that is 100% green via implementation tautologies is a documentation convention, not a `live`-readiness gate. Part 4 §B Test coverage gains a new bullet "Behavior-asserting tests" that codifies the sub-check. The sub-check is the YES/NO contract: if the regression suite can be green without any test asserting end-to-end behavior visible from chat, the loop fails item 4. Frontmatter version bumped v0.2.2 → v0.2.3 (additive refinement within v0.2.x); `updated:` field bumped 2026-08-18T11:15:22Z → 18:00:00Z; `amended_by` extended with `[[ticket:t_63385722]]`. Paired-wiki: Part 3 item 4 + Part 4 §B + Part 5 check 1 are intra-doc updates within M-decide-graph-readiness.md itself; no companion doc requires paired-wiki amendment (the related instance-side verifier check — `loop_live_audit.py` must enforce a/b/c — belongs to a follow-up coder ticket per `op-guard-17` alice-first / instance-second / compliance-gate). Source: council synthesis 2026-08-18 (`t_e27e096b`) amendment #1 (item-4 behavior-vs-tautology, HIGHEST priority).

### Note on version bump
Per `~/.hermes/methodology/M-decide-alice-versioning.md` Parts 8-10 (canonical-rule extension), this is a MINOR-level addition (new methodology file = new feature). The exact version label (v0.3.0 vs v0.2.1) is computed by `check_version_discipline.py` at publish time, not pre-bumped in this changelog. The current `version:` field in CITATION.cff is v0.2.0 (the consolidated MINOR release from 2026-08-11); the next publish will resolve the bump.

## [v0.2.0] — 2026-08-11

MINOR release: 5 MINOR features + 1 PATCH tooling batched into a single MINOR release per Path A operator decision (recorded in `kanban:t_a7a26ea4` closed 2026-08-11T16:50Z). Strict semver (0.1.4 → 0.2.0 = MINOR per Semver.org §8, minor digit +1, patch resets to 0) passes the alice-publish Step 0 pre-flight `check_version_discipline.py` (kanban:t_604c0927). The pre-staged placeholder entries for v0.1.5 / v0.1.6 / v0.1.7 / v0.1.8 (filed from the now-closed `kanban:t_11bd706c` 3-option ad-hoc BATCH/SPLIT ticket) are dropped per Path A; their content folds into this consolidated v0.2.0 entry.

### Operator decision rationale

Per operator direction 2026-08-11 (verbatim: *"I don't want to make a 1-off decision. The doc writer should define our rules for semver based on open source repo best practices, and then we apply that standard to our situation."*) + the canonical-rule extension to `~/.hermes/methodology/M-decide-alice-versioning.md` Parts 8-10 (Keep a Changelog 1.1.0 + Semver.org 2.0.0 + Conventional Commits 1.0.0 + GitHub Releases docs), the batch-or-split rule says PATCH + MINOR must be split. **Path A is the operator's documented override for this single release** — strict semver (0.1.4 → 0.2.0 = MINOR) batches the 5 MINOR + 1 PATCH into one MINOR release so the script's pre-flight accepts the publish. The Parts 8-10 rule (filed by the doc-writer post-Path-A) preserves the canonical split rule going forward; Path A is the one-off operator override for the v0.1.4 → v0.2.0 jump.

### Added
- methodology/M-decide-instance-vs-framework.md (v0.1.0) — framework-vs-instance distinction rule
- methodology/M-decide-instance-leak-test.md (v0.0.1) — H1-pattern fixture (deliberate ERROR per t_764d71c9)
- methodology/M-decide-h1-leak.md (v0.0.1) — H1-pattern fixture (deliberate ERROR per t_764d71c9)
- methodology/M-decide-parity-check.md (v0.1.0) — parity-check rule (single-methodology)
- methodology/M-decide-spec-first-flow.md (v0.1.0) — 4-phase flow spec
- methodology/M-decide-v0.1.1-defer-set.md (v0.1.0) — v0.1.1 deferral set
- methodology/06b-decide-blocked-ticket-recovery.md (v0.1.0) — blocked-ticket recovery procedure
- references/instance-leak-check-spec.md (v0.1.0) — instance-leak check pattern catalog
- templates/check_version_discipline.py.template (v0.1.0) — version-discipline pre-flight script
- templates/instance-leak-check.py.template (v0.1.0) — instance-leak CI script
- .github/workflows/ci.yml — CI pipeline that runs check_instance_leaks.py + check_version_discipline.py
- .github/ISSUE_TEMPLATE/bug_report.yml — bug report template
- .github/ISSUE_TEMPLATE/feature_request.yml — feature request template
- .github/PULL_REQUEST_TEMPLATE.md — pull request template
- .github/FUNDING.yml — funding configuration

### Changed
- methodology/M-decide-x-article-review-flow.md — v0.5.0 → v0.6.0 (master-title shape change per t_d2d0db6a; canonical template `[MASTER] X Article - {topic} - {author}`)
- methodology/M-decide-x-article-review-flow.md — v0.6.0 → v0.7.0 (retro-position amendment per t_42e0c91c; Retro-A + Retro-H moved to END of flow)
- methodology/06a-decide-retro-v2.md — v0.1.4 → v0.1.5 (Amendment 5; paired-wiki for retro-position amendment, per t_42e0c91c)
- methodology/04c-decide-master-ticket.md — v0.1.3 → v0.1.4 (paired-wiki for x-article-review title-shape change, per t_d2d0db6a)
- methodology/04c-decide-master-ticket.md — v0.1.4 → v0.1.5 (paired-wiki for version-label discipline cite; new Part 8 §Version-label discipline (v0.2.0 forthcoming))
- methodology/00-decide-ticket-naming.md — v0.4.0 → v0.4.1 (paired-wiki: `links` field adds `[[~/.hermes/methodology/M-decide-alice-versioning.md]]` per op-guard-5; no contract change to 00)
- methodology/03b-decide-operator-agent-interaction.md — frontmatter version 0.1.2 → 0.1.3 (paired-wiki for version-label discipline cite)
- methodology/06a-decide-retro-v2.md — v0.1.5 → v0.1.6 (Amendment 6; paired-wiki for v0.2.0 release; the operator-action surface gains version-label cite)
- references/tool-mapping-guide.md — minor paired-wiki update (M-decide-instance-vs-framework.md link)
- templates/caption-convention-rule.md.template — minor paired-wiki update
- templates/human-digest.md.template — minor paired-wiki update
- CONTRIBUTING.md — instance-leak discipline + check_version_discipline.py install instructions
- METHODOLOGY.md — §7a navigation-aid-caption deprecation note (the convention is now in 04c, not a standalone methodology)
- CHANGELOG.md — this entry (4 pre-staged placeholders consolidated per Path A)

### Removed (env-mutation; ships via the audit reorg per t_7aa96032 Part B/C)
- methodology/M-decide-x-article-review-flow.md → ~/.hermes/methodology/ (instance-side per framework-vs-instance distinction)
- methodology/M-decide-alice-publish-flow.md → ~/.hermes/methodology/
- methodology/M-decide-council-sizing.md → ~/.hermes/methodology/
- methodology/M-decide-human-digest.md → ~/.hermes/methodology/
- methodology/M-decide-navigation-aid-caption.md → ~/.hermes/methodology/
- methodology/M-decide-session-handoff.md → ~/.hermes/methodology/

### Promoted (Alice framework, per t_7aa96032 Part C)
- _inbox/M-decide-spec-first-flow.md → methodology/
- _inbox/M-decide-parity-check.md → methodology/
- _inbox/M-decide-v0.1.1-defer-set.md → methodology/

### Detail

#### methodology/M-decide-instance-vs-framework.md (v0.1.0) — framework-vs-instance distinction rule
- The canonical rule for distinguishing Alice framework content (publish to GitHub) from Hermes-instance operational flows (live in `~/.hermes/`). Codifies Part 2 §Decision tree (Q1: friend-portable? → A. Q2: instance flow? → H. Q3: both? → split. Q4: scratch? → X), Part 3 §Naming convention (numbered 00-09 = Alice framework; M-decide-*.md = either, classified by content), Part 4 §Publishing gate (the instance-leak check before publishing), Part 5 §Done-gate (5 conditions for a doc to be durable-shipped). 11-line decision tree + 9-row concrete examples table. Source: t_7aa96032.

#### methodology/M-decide-h1-leak.md + methodology/M-decide-instance-leak-test.md (v0.0.1) — H1-pattern fixtures (deliberate ERROR per t_764d71c9)
- Two deliberate instance-leak fixtures required by the regression suite `~/.hermes/tests/test_check_instance_leaks.py`. The H1 pattern (instance kind frontmatter) and the body-leak pattern (instance path references) MUST keep firing ERRORs in production to exercise the same code path as the regression suite. Per t_764d71c9: "the 2 remaining are deliberate H1 fixtures that must keep firing." Files paired with the test suite per op-guard-5.

#### methodology/M-decide-parity-check.md (v0.1.0) — parity-check rule
- Promoted from `_inbox/M-decide-parity-check.md` per the audit reorg classification (A = Alice — promote to canonical methodology; friend-portable per `M-decide-instance-vs-framework.md`). Single-methodology doc that codifies the parity-check contract for cross-verification between framework canon and instance implementation.

#### methodology/M-decide-spec-first-flow.md (v0.1.0) — 4-phase flow spec
- Promoted from `_inbox/M-decide-spec-first-flow.md` per the audit reorg classification. Codifies the 4-phase spec-first flow: doc → validate doc → update environment → validate update. Companion to `~/.hermes/loops/loops/SKILL.md` (the loop-template skill). Per op-guard-16 spec-first-flow.

#### methodology/M-decide-v0.1.1-defer-set.md (v0.1.0) — v0.1.1 deferral set
- Promoted from `_inbox/M-decide-v0.1.1-defer-set.md`. Codifies the 4 deferral decisions from v0.1.1 (companion scaffold, testability improvements, enterprise governance, agent-standards positioning). Used as the canonical reference when a v0.2.0+ operator considers re-positioning Alice as an enterprise standards framework.

#### methodology/06b-decide-blocked-ticket-recovery.md (v0.1.0) — blocked-ticket recovery procedure
- Promoted from `_inbox/06b-decide-blocked-ticket-recovery.md` per the audit reorg classification (A = Alice — promote to canonical methodology; friend-portable per `M-decide-instance-vs-framework.md`). The standing rule for stuck-ticket handling (trigger conditions, 4-step investigation procedure, 4 rescue actions, cadence + ownership, mapping to future operational guard). Source: t_275f1fbf.

#### references/instance-leak-check-spec.md (v0.1.0) — instance-leak check pattern catalog
- The instance-leak check specification. Codifies H1-H5 + X1 patterns + U-bucket handling + check output format (text + JSON) + exit codes (0/1/2) + where-the-check-runs (pre-commit + GitHub Actions CI + alice-publish Step 1). The check is tool-agnostic; the pattern catalog is canonical. Source: t_7aa96032.

#### templates/check_version_discipline.py.template (v0.1.0) — version-discipline pre-flight script
- The tool-agnostic template form of the version-discipline check. Companion to `~/.hermes/tools/check_version_discipline.py` (the instance-side implementation, 13/13 regression green per t_604c0927). Verifies (a) proposed version is exactly last-tag + 1 in the appropriate semver position, (b) bump type matches the actual change type, (c) version is not skipping, (d) MAJOR bumps have operator authorization via `[HUMAN ACTION]` child ticket id in master body. Wired into alice-publish loop Step 0 (BEFORE the existing Step 1 instance-leak gate). Source: t_6bbbe1ed.

#### templates/instance-leak-check.py.template (v0.1.0) — instance-leak CI script
- The tool-agnostic CI script template. 349-line Python 3.10+ script with Frontmatter parser (no PyYAML dependency), classify_file() applying H1-H5 + X1 patterns, walk_repo() / check_repo() / format_text() / format_json() helpers, main() with exit code logic. 5-case pytest regression suite covering clean repo, M-decide-with-instance-intent, body-with-instance-path, .bak file, profile-name-warning + --strict-warnings flag. Tool-agnostic: any operator copies to instance + customizes the HERMES_PROFILE_NAMES / HERMES_PATH_PATTERNS / TOOL_CLI_PATTERNS constants. Source: t_7aa96032.

#### x-article-review master-title shape change (M-decide-x-article-review-flow.md v0.5.0 → v0.6.0)
- Per t_d2d0db6a. New canonical template: `[MASTER] X Article - {topic} - {author}`. Domain marker `X Article` at position 2 (immediately after `[MASTER]`), hyphen separators, topic-first then author. The previous shape `[MASTER] x-article-review / <author-slug> <topic-slug> — <description>` is deprecated for this flow. The 80-codepoint budget + the 4-method title-recovery ladder + the rename-application contract + the post-creation assertion are unchanged.

#### x-article-review retro-position amendment (M-decide-x-article-review-flow.md v0.6.0 → v0.7.0)
- Per t_42e0c91c. Retro-A + Retro-H moved to the END of the flow graph (after council output + corrective actions + ship lane). The prior Phase 0 retro spawn (v0.1.1, t_248722d8) was the original structural fix for "master auto-closes before retro fires" but introduced a NEW defect: when Retro-A reached `done`, the dispatcher's auto-close logic fired `master_auto_done_via_children` and the master auto-closed even with Retro-H still `blocked` (verified case: wast3 article `t_092e629c`). v0.7.0 codifies a new Part 5 §Flow order + a new Part 7.5 done-gate with 8 explicit conditions.

#### 06a-decide-retro-v2.md v0.1.4 → v0.1.5 (Amendment 5, paired-wiki for retro-position)
- Per t_42e0c91c. Retro-A's parent-edge list now references the post-council fan-in (NOT just `[master]`). The list is `[master, P4, V-4, V-of-V, corrective-children-done, ship-done-when-applicable]` (conditional on whether corrective-action children + ship lane ran). Retro-H's parent edge remains `parents=[retro_a]` only (Amendment 4; unchanged). Rule count history unchanged (still 6 rules); Amendment 5 is a clarification, not a new rule.

#### 04c-decide-master-ticket.md v0.1.3 → v0.1.4 (paired-wiki, no contract change)
- Per t_d2d0db6a. Part 8 §Master Title Discipline §Format convention sub-section updated to reflect the new x-article-review canonical template `[MASTER] X Article - {topic} - {author}` with worked examples. The flow-agnostic title-rendering discipline (80-codepoint budget + the 4-method ladder + the rename-application contract) is unchanged from v0.1.3 — v0.1.4 is a paired-wiki-only amendment.

#### 04c-decide-master-ticket.md v0.1.4 → v0.1.5 + 03b-decide-operator-agent-interaction.md v0.1.2 → v0.1.3 + 06a-decide-retro-v2.md v0.1.5 → v0.1.6 (paired-wiki for version-label discipline)
- New `**Version-label discipline (v0.2.0 forthcoming)**` paragraph anchors the orchestrator's version-label lookup to `~/.hermes/methodology/M-decide-alice-versioning.md` Part 2 + Part 4. Paired-wiki-only — no contract change to the underlying docs. Per op-guard-5 paired-wiki integrity.

#### 00-decide-ticket-naming.md v0.4.0 → v0.4.1 (paired-wiki, no contract change)
- `links` field adds `[[~/.hermes/methodology/M-decide-alice-versioning.md]]` so the ticket-naming convention's index cross-references the new semver doc (paired-wiki per op-guard-5; no contract change to 00).

#### check_version_discipline.py pre-flight rule (gate contract; instance-side ships via coder child ticket)
- Companion enforcement surface lives in `templates/check_version_discipline.py.template` (Alice-canonical form) + `~/.hermes/tools/check_version_discipline.py` (instance-side implementation, 13/13 regression green). Wired into alice-publish loop Step 0 (BEFORE the existing Step 1 instance-leak gate). Per op-guard-17 alice-first / instance-second / compliance-gate.

#### CONTRIBUTING.md + .github/workflows/ci.yml (audit-reorg tooling)
- The CONTRIBUTING guide gains an instance-leak discipline section + check_version_discipline.py install instructions. The GitHub Actions workflow runs check_instance_leaks.py + check_version_discipline.py on every PR. Pre-commit hook installation instructions in CONTRIBUTING.md §"Pre-commit hooks".

### Source
- MINOR motivation: operator direction 2026-08-11 (verbatim: *"0.1.7? We haven't even shipped 0.1.5 or defined anything for 0.1.6. Need some durable logic for how we are handling symantec versioning."*). Source ticket `t_25edf8f7` (a misfiled `[CODER] alice-publish v0.1.7` for tooling/reorg work) closed `filed_in_error` the same day.
- Canonical-rule origin: operator direction 2026-08-11 (verbatim: *"I don't want to make a 1-off decision. The doc writer should define our rules for semver based on open source repo best practices, and then we apply that standard to our situation."*). The rule is at `~/.hermes/methodology/M-decide-alice-versioning.md` Parts 8-10; this entry is the canonical application of the rule (with Path A operator override) to the v0.1.4 → v0.2.0 ship.
- Operator override (Path A): recorded in `kanban:t_a7a26ea4` closed 2026-08-11T16:50Z. The override rationale: the script's strict-semver contract (0.1.4 → 0.1.5 = PATCH by math) prevents filing v0.1.5 as MINOR even though the work is MINOR-content; the canonical workaround is 0.1.4 → 0.2.0 = MINOR (strict semver passes). The script passes for v0.2.0 with the JSON manifest (11 added / 6 modified / 6 deleted). Verified in the parent ticket body's verified-state capture (op-guard-19).
- Second-strike trigger: this is the 2nd semver-verification failure within this method (`t_7aa96032`'s framework-vs-instance distinction was the first). Per `2-ATOMIC/rules/three-op-guard-rule-2026-07-20.md`, two strikes = durable fix warranted.
- Companion rule: `op-guard-5-paired-wiki-update-2026-07-21.md` (the M-doc + the 04c paired-wiki + the CHANGELOG entry ship in the same change set).
- Companion rule: `op-guard-16-spec-first-flow-2026-08-05.md` (doc-writer is spec-only; this M-doc + CHANGELOG entry + 04c paired-wiki are the doc-writer's ship; the env-mutation child tickets ship via coder per the META `t_6bbbe1ed` Part B + Part C).
- Companion rule: `op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md` (alice-first / instance-second / compliance-gate; the methodology + paired-wiki ship now; the template + script + loop-wiring children ship as Part B coder tickets AFTER the methodology lands; the compliance-verifier is the META done-gate).
- Companion rule: `op-guard-19-pre-verify-artifact-state-2026-08-08.md` (the `verified-state` capture for this amendment lands in the compliance-verifier child ticket body).
- Companion rule: `op-guard-21-chat-side-orchestrator-only-no-direct-edit-2026-08-09.md` (the doc-writer ships the methodology files only; the instance child tickets ship via the coder profile).
- Companion rule: `op-guard-20-use-existing-loops-not-bespoke-2026-08-08.md` (this publish ticket IS the alice-publish loop ticket; loop registration in `~/.hermes/loops/hermes.yaml` alice-publish v0.5.0 with STEP 0 wired).
- Source tickets: t_d2d0db6a (x-article-review master-title shape), t_42e0c91c (x-article-review retro-position amendment), t_7aa96032 (framework-vs-instance distinction + instance-leak CI gate + audit reorg), t_275f1fbf (06b-decide-blocked-ticket-recovery promotion), t_6bbbe1ed (semver discipline + check_version_discipline.py pre-flight), t_36795558 (4 stale x-refs fix), t_764d71c9 (instance-leak H1 fixtures), t_a7a26ea4 (operator Path A decision), t_604c0927 (instance-side check_version_discipline.py + 13-case regression suite), t_60859140 (superseded by Path A), t_773fb4a8 (absorbed by t_b0d6dd79).
- Publish ticket: `t_b0d6dd79` (the consolidated publish ticket; supersedes `t_60859140` per Path A).

## [v0.1.4] — 2026-08-11

PATCH release: canonical-form CHANGELOG v0.1.3 entry + CITATION.cff gate-comment dedup + `post_alice_release_discord.py` STEP 11 delivery for v0.1.3.

### Changed

- CHANGELOG v0.1.3 canonical form (slug bullets + Detail)
- CITATION.cff gate-comment dedup
- STEP 11 v0.1.3 delivery (canonical topic-only message)

### Detail

#### CHANGELOG v0.1.3 canonical form (slug bullets + Detail)
- **`CHANGELOG.md`** — v0.1.3 entry reformatted: bullet items under `### Added` + `### Changed` are now short topic slugs (consumed by `post_alice_release_discord.py`); full description per change lives under `### Detail` sub-sections. The previous v0.1.3 form had full descriptions as bullets, which the script naively extracted as long topic strings (verbose Discord message).

#### CITATION.cff gate-comment dedup
- **`CITATION.cff`** — L4-L8 header deduped: the parallel coder + my edits left a redundant pair of `Gate re-opened rationale` lines + an L7 `Gate re-opened 2026-08-11` line that duplicated L4. Consolidated to 3 rationale lines (v0.1.2, v0.1.3, v0.1.4). `version: 0.1.3 → 0.1.4`; `date-released: 2026-08-11` (unchanged); `Zenodo archived-snapshot DOI: v0.1.3 → v0.1.4`.

#### STEP 11 v0.1.3 delivery (canonical topic-only message)
- **`~/.hermes/tools/post_alice_release_discord.py`** — invoked for v0.1.3 release at `https://github.com/042Trix/Alice/releases/tag/v0.1.3`. The first delivery attempt produced the wrong message (`Alice v0.1.3: documentation-only`) because `### Added`/`### Changed` bullets were full descriptions (script's `_extract_topics` produced empty topic list). Delivery record was deleted + script re-invoked after the CHANGELOG slug-bullet restructure. Canonical message:
  `Alice v0.1.3: alice-publish-flow methodology (v0.3.0), council sizing methodology (three-signal rule), human-digest methodology (v0.4.0), navigation-aid-caption convention, session-handoff protocol, x-article-review-flow methodology (v0.5.0), caption-convention-rule template, human-digest template (v0.4.0), CHANGELOG consolidation (4 Unreleased → single v0.1.3 entry), 04a work-graph (node-admission rubric + reference card), 04c master-ticket (rename-application discipline), 06a retro-v2 (Amendment 4: Retro-H parent-edge fix)`
  — 12 topic slugs, no article author, no article URL, no source handle. Source: `t_cd557fe8`.

### Source

- PATCH motivation: `post_alice_release_discord.py --dry-run` on the v0.1.3 entry showed empty topic list (full-description bullets) → `documentation-only` delivery. PATCH fixes the CHANGELOG to use slug bullets so the script can extract the actual topic list.
- Companion rule: `op-guard-19-pre-verify-artifact-state-2026-08-08.md` (the pre-verify rule; PATCH applied pre-verify before commit).
- Companion rule: `op-guard-21-chat-side-orchestrator-only-no-direct-edit-2026-08-09.md` (PATCH was a chat-side CHANGELOG.md + CITATION.cff edit per the kanban + worker protocol: I am the worker on `t_cd557fe8`, not the chat-side orchestrator; the PATCH is the worker doing the work the dispatcher assigned).
- Companion rule: `op-guard-26-query-board-state-before-reporting-2026-08-09.md` (PATCH status reported via `board_status.py`; the parallel `t_f7d441e9` ticket was discovered mid-run; that worker's commit 3f22c3d already shipped v0.1.3; my PATCH v0.1.4 is the post-ship STEP 11 + dedup).
- Publish ticket: `t_cd557fe8` (alice-publish loop v0.3.0 worker; this PATCH is the PATCH release of v0.1.3, tagged v0.1.4).

## [v0.1.3] — 2026-08-11

The post-v0.1.2 working-tree delta: 6 new methodology docs + 2 new templates + 4 modified files. Consolidates the 4 `[Unreleased]` blocks added incrementally during the 2026-08-08 → 2026-08-11 session into one entry per the release-prep discipline (one entry per released version). Multi-touch change set ships as a single `ALICE v0.1.3` commit on `main` with a tag + GitHub release; the alice-publish loop v0.3.0 ships it end-to-end per `methodology/M-decide-alice-publish-flow.md`.

The bullet items under `### Added` + `### Changed` are the **short topic slugs** used by `post_alice_release_discord.py` to build the operator-facing Discord release message (canonical topic-only format: `Alice v<X.Y.Z>: <topic-1>, <topic-2>, ...`). The full detail per change lives under `### Detail` below the `### Changed` section.

### Added

- alice-publish-flow methodology (v0.3.0)
- council sizing methodology (three-signal rule)
- human-digest methodology (v0.4.0)
- navigation-aid-caption convention
- session-handoff protocol
- x-article-review-flow methodology (v0.5.0)
- caption-convention-rule template
- human-digest template (v0.4.0)

### Changed

- CHANGELOG consolidation (4 Unreleased → single v0.1.3 entry)
- 04a work-graph (node-admission rubric + reference card)
- 04c master-ticket (rename-application discipline)
- 06a retro-v2 (Amendment 4: Retro-H parent-edge fix)

### Detail

#### alice-publish-flow methodology (v0.3.0)
- **`methodology/M-decide-alice-publish-flow.md`** (v0.3.0, 43 KB, 385 lines) — the canonical alice-publish flow spec. Adapts the 6-field flow spec from `methodology/04d-decide-flow-spec.md` to the deterministic git/gh nature of a release publish. Defines the **10-step publish pipeline + 5 NEW gates** in v0.2.0 that close the drift surface from the alice-framework v0.1.2 push incident (`t_ecb73d81`, 2026-08-08): pre-verify gate (op-guard-19) + CHANGELOG consolidation gate + per-file version reconciliation gate + CITATION.cff operator-gate check + per-step verifier continuation + STEP 11 (v0.3.0, `t_a92c1f88`) operator-facing Discord release message via `post_alice_release_discord.py` (canonical topic-only format).

#### council sizing methodology (three-signal rule)
- **`methodology/M-decide-council-sizing.md`** (v0.1.0) — codifies the **three-signal rule** for picking the right council size and seats per request: (1) decision reversibility, (2) number of stakeholder groups affected, (3) question dimensions. Replaces the previous "pick 3-5 per question" instruction with a seat-selection algorithm. Documents the rule in 4 places: `hermes-council` SKILL.md + seat-selection reference + alice `M-decide-council-sizing.md` + verifier checklist. Source: `t_ea62fcff` (council, 2026-08-07).

#### human-digest methodology (v0.4.0)
- **`methodology/M-decide-human-digest.md`** (v0.4.0) — the canonical human-readable retro digest contract (10-section specialization of the 8-section shape from `methodology/03b-decide-operator-agent-interaction.md` Part 13). Sibling to the technical retro at `methodology/06a-decide-retro-v2.md`. v0.2.0 (2026-08-08, `t_4d1de050`): section order rearranged per operator direction — actionable top tier (sections 1-3), transitional interpretation layer (sections 4-5), operator action surface (sections 6-7), technical-detail bottom tier (sections 8-10). Source: `t_5dc19cae`, `t_4d1de050`, `t_d47a8be7`.

#### navigation-aid-caption convention
- **`methodology/M-decide-navigation-aid-caption.md`** (v0.1.0) — codifies the **3-part caption convention** for any diagram or visual aid labeled as a "navigation aid" (vs. a canonical schema): (1) name the artifact a "navigation aid," (2) state the date or context of the framing, (3) name Alice's canonical schema. Origin: 5-seat council re-run on the X article-1 review (2026-08-08, `t_73ea1fff`). Source: `t_6314966f`.

#### session-handoff protocol
- **`methodology/M-decide-session-handoff.md`** (v0.1.0) — codifies the **session-handoff protocol** for Alice: 9-part spec (Purpose, When, Where, 5-section schema, Length budget, Validation gate, Read-side companion, Distinction from human-digest, Cross-references). The opt-in / ask-before-write default is the load-bearing rule. Companion to `methodology/M-decide-human-digest.md`. Source: `t_d6b12927` + `t_b752e517` (verifier round 2).

#### x-article-review-flow methodology (v0.5.0)
- **`methodology/M-decide-x-article-review-flow.md`** (v0.5.0) — the canonical flow spec for the x-article-review loop. v0.5.0 amendment (Amendment 4, `t_cd29b136`): the conditional Alice-only GitHub ship lane. v0.4.1 → v0.5.0: 6 new tags; `amended_by` extended; new Part 7.5 + Part 7.5.1; Part 3 + Part 5 dispatch + Part 10 verification extended. Topic-only release-message discipline (no article author/URL/handle) codified in Part 3 `## Release-message discipline`. Source: `t_cd29b136`, `t_1c61577c`, `t_c11e4845`, `t_dd2819d6`.

#### caption-convention-rule template
- **`templates/caption-convention-rule.md.template`** — fillable record paired with `methodology/M-decide-navigation-aid-caption.md`. Captures the 3-part caption structure + worked examples + anti-patterns + verification gate. Source: `t_6314966f`.

#### human-digest template (v0.4.0)
- **`templates/human-digest.md.template`** (v0.4.0) — fillable template paired with `methodology/M-decide-human-digest.md`. 10-section shape with the operator-direction action-first section order (1-3 actionable top, 4-5 transitional, 6-7 operator-action, 8-10 technical-detail bottom). Source: `t_4d1de050`.

#### CHANGELOG consolidation (4 Unreleased → single v0.1.3 entry)
- **`CHANGELOG.md`** — the four `[Unreleased]` blocks (06a Amendment 4, alice-publish flow v0.2.0, LICENSE pick, blocked-ticket recovery procedure) consolidated into a single `## [v0.1.3]` entry per the alice-publish-flow STEP 4 gate. Frontmatter `updated:` bumped to 2026-08-11. Source: paired-wiki per op-guard-5.

#### 04a work-graph (node-admission rubric + reference card)
- **`methodology/04a-decide-work-graph.md`** (v0.1.2 → v0.1.3) — post-v0.1.2 spec-alignment edits sourced from the wandermist article-3 retro (`t_78936c6e`, ACCEPT Q1/Q2/Q3/Q5): **Headline rubric** ("Is this node earning its place?") + **node-admission taxonomy** in `methodology/references/04a-node-admission-taxonomy.md` (new reference card) + `links:` extended + `amended_by` extended with `t_78936c6e`. Frontmatter `updated:` bumped to 2026-08-09.

#### 04c master-ticket (rename-application discipline)
- **`methodology/04c-decide-master-ticket.md`** (v0.1.2 → v0.1.3) — post-v0.1.2 spec-alignment edits sourced from `t_78936c6e` (rename-application discipline) + `t_1c61577c` (master-ticket title discipline restatement). Title discipline restated: master title format is `[MASTER] <flow-name> / <topic-slug> — <description>` with the URL/identifiers in body `## Source` block. Frontmatter `updated:` bumped to 2026-08-09.

#### 06a retro-v2 (Amendment 4: Retro-H parent-edge fix)
- **`methodology/06a-decide-retro-v2.md`** (v0.1.3 → v0.1.4) — **Amendment 4** (the load-bearing change of v0.1.3): fixes a circular-gate bug in the 2-part retro pattern. Retro-H is now filed with `parents=[retro_a]` ONLY (was `[master, retro_a]`). The master is in Retro-H's required-children set via the done-gate. Verified case: wandermist article-3 master `t_6c49fbd9` + Retro-A `t_59b36bd5` + Retro-H `t_993f88f7`. Operator quote: "I manually remove the master ticket as a parent on retro-H." Source: `t_33843787`.

### Source

- Operator direction 2026-08-08: "Do we have a flow written for pushing latest changes to GitHub? We should make one so we know it's thorough and consistent." (origin of `M-decide-alice-publish-flow.md`).
- Operator direction 2026-08-11: "I manually remove the master ticket as a parent on retro-H. We need to update the graph to list retro-H as a child of the master ticket, not as a parent." (origin of 06a Amendment 4).
- Source tickets: `t_fdba17e5` (alice-publish flow doc-writer), `t_fb90b479` (coder loop v0.2.0 amendment), `t_a92c1f88` (v0.3.0 release-message amendment), `t_ea62fcff` (council sizing), `t_5dc19cae` (human-digest executive-reporting cross-ref), `t_4d1de050` (human-digest section order v0.2.0), `t_d47a8be7` (operator-interaction-patterns cross-ref), `t_d6b12927` (session-handoff methodology), `t_b752e517` (session-handoff verifier round 2), `t_cd29b136` (x-article-review v0.5.0 amendment), `t_1c61577c` (rename discipline amendment), `t_c11e4845` (council source-attribution amendment), `t_dd2819d6` (release-message format amendment), `t_6314966f` (caption-convention rule), `t_dbeda528` (caption-convention verifier), `t_78936c6e` (wandermist article-3 retro → 04a/04c edits), `t_33843787` (06a Amendment 4).
- Companion rule: `op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md` (alice-first / instance-second / compliance-gate; this entry is the doc side; the instance conformance for 06a Amendment 4 is at the parent-edge level on the kanban DB — no dispatcher / DB / profile mutation; future Retro-H filings use `parents=[retro_a]`).
- Companion rule: `op-guard-16-spec-first-flow-2026-08-05.md` (doc-writer is spec-only; this entry is doc-only; no environment mutation in this ticket).
- Companion rule: `op-guard-19-pre-verify-artifact-state-2026-08-08.md` (the pre-verify rule that drives STEP 1 of the alice-publish flow; this entry's ticket body carries a `## Verified state` section).
- Companion rule: `op-guard-5-wiki-update-2026-07-21.md` (paired-wiki integrity; the 06a + 04a + 04c methodology `updated:` frontmatter fields are bumped in this change set).
- Publish ticket: `t_cd557fe8` (alice-publish loop v0.3.0 worker; coder; runs STEP 1-11 end-to-end).

### Migration notes

- The 4 `[Unreleased]` blocks (06a Amendment 4, alice-publish flow v0.2.0, LICENSE pick, blocked-ticket recovery procedure) added incrementally during the 2026-08-08 → 2026-08-11 session are consolidated into this single `## [v0.1.3]` entry per the release-prep discipline (one changelog entry per released version). The LICENSE pick items that pre-dated v0.1.2 are noted as already-shipped in the v0.1.2 entry ("CHANGELOG.md line 25 updated: CC-BY-4.0 default → MIT decision").
- The blocked-ticket recovery procedure (`methodology/06b-decide-blocked-ticket-recovery.md` + `templates/AGENTS.md.template` Section 7a) is shipped at v0.1.3 but documented under "added" only as a methodology cross-reference; the canonical entry will appear under v0.1.2 if it landed earlier or v0.1.3 if first-shipped now. (Per `t_78ffd7e5`, the methodology shipped 2026-08-05; the change is already part of the prior release surface.)
- The v0.1.3 entry was reformatted in a v0.1.4 PATCH (this canonical-form commit) so `post_alice_release_discord.py` can extract topic slugs from the `### Added` / `### Changed` bullets for the operator-facing Discord release message. The previous v0.1.3 form had full descriptions as bullets, which the script naively extracted as long topic strings (verbose Discord message); the v0.1.4 PATCH moves the full descriptions to `### Detail` sub-sections and uses short slug bullets in `### Added` / `### Changed`.
## [v0.1.1] — naming: "Strike rule" → "Operational Guard"
### Changed
- `methodology/05-strike-rules.md` → renamed to `methodology/05-op-guards.md`. Whole body rewritten: "strike rule" → "operational guard" throughout. Lifecycle properties (expiration / deduplication / supersession / severity) updated to use the new term. Worked example updated (Rule R → Guard G, Rule S → Guard H). Cross-references in See-also section updated.
- `templates/strike-rule.md.template` → renamed to `templates/op-guard.md.template`. Frontmatter id `alice-template-strike-rule` → `alice-template-op-guard`. Body uses "operational guard" throughout.
- `references/glossary.md` — Operational guard replaces Strike rule in the canonical-term table. New "Aliases and deprecated forms" entry: "Strike rule | Deprecated (soft-rename) | Operational guard | Renamed in v0.1.1 (2026-08-06). The new term is self-explanatory; 'strike' read as labor/military/baseball to outsiders. Old filenames still exist as redirect stubs for backward compatibility." The "Rule" ambiguous-shorthand entry now points to "Operational guard" instead of "Strike rule."
- 18 source files updated across `methodology/`, `references/`, `templates/`, `worked-examples/`, and `README.md` / `METHODOLOGY.md` to use the new term. Counting: 299 lexical substitutions across 50 files (including the gitignored `_inbox/` and `_polish-archive/` directories, which were re-harmonized for consistency even though they are not part of the public release).
- Two adjacent patterns renamed to disambiguate from the rename:
  - `references/kanban-lite-disciplines.md` "The 3-strike-system for kanban" → "The 3-element-system for kanban" (the section describes 3 system elements, not 3 strike rules).
  - `methodology/06-iteration-loop.md` "The 5-strike-system pattern" → "The 5-loop-archetype pattern" (the section describes 5 loop archetypes, not 5 strike rules).
- Filename references in frontmatter `links:` arrays updated from `methodology/05-strike-rules.md` to `methodology/05-op-guards.md` (8 files).
- `rule-N` / `Strike-N` / `op-guard-N` references in prose updated to `op-guard-N` (rule numbers preserved). E.g. "strike-13" → "op-guard-13", "Strike-15" → "op-guard-15", "strikes 1-3 corrected" → "op-guards 1-3 corrected". The rule numbers themselves did not change.

### Out of scope (kept)
- `methodology/06b-decide-blocked-ticket-recovery.md` is still under `_inbox/` (not yet promoted to `methodology/`); the script touched it for consistency but it is not part of the public methodology corpus.
- Resilience to reviewer-defined terminologies: nothing in the doc contradicts the operator's standing rule that "rule numbers don't change." Only the prefix and the noun rename.

### Source
- Ticket `t_fcc34797` (rename scope). Operator direction: "Let's rename to 'Operational Guards'." (2026-08-06.)
- Cross-reference: `2-ATOMIC/cross-agent/2026-08-06_council-alice-v0-1-1-improvement-plan.md` (Improvement plan that first proposed the rename).

### Out-of-scope follow-up (vault rule files)
The vault rule section (`~/Documents/HermesVault/2-ATOMIC/rules/`) was renamed in the same ticket. **However, the rename script had a content-loss bug**: it overwrote the original rule files with redirect stubs before the read-modify-write loop had a chance to preserve the body. 2 of 13 rules (`op-guard-11-log-md-append-only-2026-07-22.md`, `op-guard-15-must-call-terminal-2026-07-29.md`) were recovered from the MoA trace files at `2-ATOMIC/cross-agent/planner-moa-traces/`. The remaining 11 rules (op-guard-3, 4, 5, 8, 9, 10×2, 11-no-auto-handoff, 13, 16, three-op-guard-rule) are content-loss stubs as of 2026-08-06. The rename ticket is BLOCKED for the vault side until the 11 missing rule bodies are reconstructed. This is operator-visible (the loss-notice files at the old and new filenames). The Alice public repo side of the rename is complete and not blocked.

## [v0.1.1] — Maintenance sections across the methodology corpus

### Added
- **`methodology/_templates/maintenance-scaffold.md.template`** — shared 5-subsection Maintenance scaffold (audit cadence, quality threshold, drift signals, fix actions, retirement conditions) + maintenance parity check, used as the canonical reference for the 12 sister-tickets.
- **`references/skill-curator.md`** — new reference page on the skill curator's role, cadence, and audit gates (closes the gap in `methodology/02-decide-skills.md` — the build-vs-maintain audit identified skills as a Pattern A doc with no maintenance section).
- **`references/template-provenance.md`** — new reference page documenting the `derived_from:` frontmatter pattern (the v0.1.1 P5 deliverable).
- **`derived_from:` frontmatter on 14 templates** — every methodology-relevant template now records its source template, version, and customized date. This addresses the GBU reviewer's "silent drift" concern.

### Changed
- **`methodology/01-decide-vault-tier.md`**, `01a-decide-memory.md`, `01b-decide-vault-content.md`, `02-decide-skills.md`, `03-decide-agents.md`, `03a-decide-agent-vault-interaction.md`, `04-decide-crons.md`, `04b-decide-board-routing.md`, `04d-decide-flow-derivation.md`, `04d-decide-flow-spec.md`, `04e-decide-spec-hierarchy.md`, `06-iteration-loop.md`, `06a-decide-retro-v2.md`, `07-council-methodology.md` — each doc got a `## Maintenance` section with the 5-subsection template applied. The 13 docs that previously had **only build rules** now have explicit maintenance contracts (audit cadence, quality threshold, drift signals, fix actions, retirement conditions). Closes the build-vs-maintain gap flagged by the v0.1.0 council review.
- **`README.md`** — v0.1.1 polish per the GBU reviewer's recommendation:
  - **P1**: §1 tightened to ≤100 words; added explicit non-goals subsection (NOT an enterprise standards framework; NOT a turnkey installer; NOT a vendor product). Positioning line: "Alice is a tool-agnostic methodology for designing your own agent-augmented operating system — not the system itself."
  - **P2**: §1's "45–90 minutes for first pass" claim replaced with an **ongoing-cost subsection** naming the 7 recurring costs: routing accuracy, memory pruning, scheduled-work review, skill updates, tool reconciliation, agent behavior testing, and converting failures into rules. First-pass time is the *cheapest* part.
  - **P3**: ASCII system-map diagram (inbox → routing → work tracker → orchestrator → memory/vault/skills/agents → verification → failure capture → op-guards and iteration). Maps the methodology files to the diagram.
- **`templates/AGENTS.md.template`**, `agent-soul.md.template`, `agent-vault-permissions.md.template`, `council-seat.md.template`, `cron-spec.md.template`, `frontmatter-schema.md.template`, `inbox-route.md.template`, `memory-budget.md.template`, `operator-interaction-patterns.md.template`, `parity-check.md.template`, `skill-brief.md.template`, `spec-first-flow.md.template` — `derived_from:` frontmatter added (closes the v0.1.1 P5 deliverable).

### Source
- Council verdict: `/Users/homestead/Documents/HermesVault/2-ATOMIC/decisions/alice-v0.1.1-build-vs-maintain-council-verdict.md` (0.78 confidence, 7 recommendations accepted by operator).
- Cross-references: GBU review at `/Users/homestead/.hermes/cache/documents/doc_6e594c40214f_alice_GBU_0.1.0.txt`; council log at `/Users/homestead/.hermes/decisions/council-logs/council-v0.1.0-GBU-review-2026-08-06.md`; maintenance council log at `/Users/homestead/.hermes/decisions/council-logs/council-v0.1.1-build-vs-maintain-review-2026-08-06.md`.

## [v0.1.2] — 2026-08-08

### Added
- **`methodology/M-decide-human-digest.md`** — new methodology doc codifying the human-readable retro digest contract (10-section specialization of the canonical 8-section shape from `methodology/03b-decide-operator-agent-interaction.md` Part 13). Sibling to the technical retro at `methodology/06a-decide-retro-v2.md`; the digest is the operator-facing reading surface, the technical retro remains the canonical audit record. Frontmatter version: 0.1.x → 0.3.0 (over the cluster window).
- **`templates/human-digest.md.template`** — fillable template paired with the methodology. Frontmatter version: 0.1.x → 0.3.0.
- **`methodology/M-decide-navigation-aid-caption.md`** — new methodology doc (section 7a in the linear walkthrough). Codifies the **3-part caption convention** for any diagram or visual aid labeled as a "navigation aid" (vs. a canonical schema): (1) name the artifact a "navigation aid," (2) state the date or context of the framing, (3) name Alice's canonical schema so the reader cannot mistake the aid for the schema. Origin: 5-seat council re-run on the X article-1 review (2026-08-08, ticket `t_73ea1fff`); Product seat verdict identified the caption requirement as load-bearing. Defines when the rule fires, the caption format with worked examples (Delta-1 3-layer stack + hypothetical memory-tier map), the 3-part anti-patterns, and the verification gate. Maintenance burden: ~0.5 doc edits/year.
- **`templates/caption-convention-rule.md.template`** — fillable template paired with the methodology.
- **`METHODOLOGY.md` §7a ("Decide navigation-aid caption convention")** — inserted between section 7 (Council methodology) and section 8 (Inbox routing); the ASCII relation diagram at the bottom of the doc gains a new branch layer (7a. Navigation-aid caption → the rendering convention).

### Changed — 5 sub-sections
1. **Universal title discipline** — `methodology/00-decide-ticket-naming.md` Part 3 codifies the canonical 3-rule title discipline for every ticket on every board (not just masters). Operator can identify the work in 2-3 seconds from list view. URL-based titles are out; descriptive-topic titles are in. Source: `t_94c0c7cf`. Frontmatter version: 0.4.0 (amendment stream).
2. **Master Title Discipline** — `methodology/04c-decide-master-ticket.md` Part 8 codifies that master tickets carry descriptive topics in the title; URL/identifiers go in body `## Source` block. Format: `[MASTER] x-article-review / <author-slug> <topic-slug> — <description>`. Source: `t_2a513f60`. Frontmatter version: 0.1.2.
3. **Done-gate opt-in for operator-facing flows** — `methodology/04c-decide-master-ticket.md` Part 7.5. Default is auto-done; operator-facing masters MUST explicitly opt into `operator-LGTM-done` in the body. Retro child spawns at Phase 0 (master creation), not at phase completion. Source: `t_248722d8` (x-article-review flow ship).
4. **2-part retro pattern (Retro-A + Retro-H)** — `methodology/06a-decide-retro-v2.md` (Amendment 3). Retro-A (verifier, 6-axis eval) + Retro-H (operator, ACCEPT/REJECT/DEFER). Master cannot auto-close until both retro paths complete. Source: `t_323ad698`. Frontmatter version: 0.1.3.
5. **Operator-review artifact format (PDF rule)** — `methodology/03b-decide-operator-agent-interaction.md` Part 13 + `templates/operator-interaction-patterns.md.template` new section. Any artifact that requires the operator's review MUST ship in human-readable PDF format (Markdown source preserved for editability; both formats ship). Defines the 5-step delivery protocol (`Markdown (canonical) → PDF (rendered) → Discord attachment → ticket comment → vault copy`), the 8-section shape contract, the 6 discipline rules (plain-language headline, decision table mandatory, silence is not consent, technical record remains canonical), and 6 anti-patterns. Lists 7 artifact types. Trailing sections renumbered (anti-patterns Part 13 → Part 14, revision Part 14 → Part 15). Source: `t_34dc1c2b` (rule-shipment ticket), paired with `t_d47a8be7` (template cross-reference).

### Changed — auxiliary
- **`templates/operator-interaction-patterns.md.template`** — "operator-facing digest format" section's first bullet now references the 10-section retro specialization from `templates/human-digest.md.template` with the four-tier ordering (actionable top, transitional interpretation layer, operator action surface, technical-detail bottom). The "Operator decisions" bullet specifies "section 6". Shape-contract subsection points the canonical 8-section shape at `methodology/03b-decide-operator-agent-interaction.md` Part 13. Frontmatter `updated:` bumped to 2026-08-08. Source: `t_4d1de050` + `t_d47a8be7`.
- **`methodology/06a-decide-retro-v2.md`** — Human-readable digest subsection updated to list operator-facing elements in their final digest order and name the four-tier ordering. Frontmatter `updated:` bumped to 2026-08-08. Source: `t_4d1de050`.
- **`methodology/03b-decide-operator-agent-interaction.md`** — frontmatter `version: 0.1.2` field added (was missing); `links:` array extended to include `M-decide-human-digest.md` + `templates/human-digest.md.template`; `amended_by` field added with citations `t_5dc19cae`, `t_472c75f2`, `t_34dc1c2b`. Source: `t_5dc19cae` + `t_472c75f2`.
- **`methodology/04a-decide-work-graph.md`** — minor cross-reference updates. Source: paired wiki-update per op-guard-5.
- **`README.md`** §2 — new paragraph referencing the 3-layer navigation aid (HARNESS / LOOP / GRAPH) at `methodology/00-decide-ticket-naming.md`, framed honestly as a reading-onramp (Alice's canonical schema is the AREA taxonomy). Source: paired with the 3-layer stack addition in `00-decide-ticket-naming.md` Part 1.5.
- **`CHANGELOG.md`** — this entry. The four `[Unreleased]` blocks added incrementally during the session are consolidated into the single `## [v0.1.2]` entry per the release-prep discipline (one changelog entry per released version).

### Navigation aid (3-layer stack) — `methodology/00-decide-ticket-naming.md` Part 1.5
- Public-discourse framing for newcomers (HARNESS → LOOP → GRAPH). The AREA taxonomy remains canonical; the 3-layer stack is a reading-onramp. Carries the 3-part caption per `methodology/M-decide-navigation-aid-caption.md` (labeled navigation aid, date 2026-08-08, canonical schema = AREA taxonomy). Source: paired with the caption-convention rule.
- **Caption (verbatim, per `M-decide-navigation-aid-caption.md`):** *"Navigation aid, not canonical schema. Captured 2026-08-08 against the public 2026-mid-year layered-agent-system discourse. Canonical schema: AREA taxonomy (decision-ordered, in `METHODOLOGY.md` + `methodology/`). The 3-layer stack is a reading-onramp, not a replacement."*

### Source
- Operator direction (2026-08-08): "After the v0.5.0 updates land, let's push to GitHub so we can easily revert if needed" + "I am saying we push the latest to Alice."
- Source tickets: `t_94c0c7cf` (universal title discipline), `t_2a513f60` (master title discipline), `t_248722d8` (x-article-review v0.1.1 ship / done-gate opt-in), `t_323ad698` (Amendment 3 to retro v2), `t_5dc19cae` (executive-reporting skill cross-reference), `t_472c75f2` (operator-review 8-section shape), `t_d47a8be7` (operator-interaction-patterns template cross-reference), `t_34dc1c2b` (PDF rule shipment), `t_4d1de050` (human-digest section order v0.2.0), `t_9450b7e0` (human-digest self-contained v0.3.0), `t_73ea1fff` (5-seat council on X article-1; caption convention origin).
- Companion rule: `op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md` (alice-first / instance-second / compliance-gate; ordering rationale for the deferred Phase B hermes-side push).
- Companion rule: `op-guard-16-spec-first-flow-2026-08-05.md` (doc-writer is spec-only; doc-writer did not file environment-mutation tickets for any of these changes).

## [0.1.0] - 2026-08-04

### Added
- First tracked packaging entry for the Alice framework. **No public release yet; this section records what currently exists in the repo at the time of writing.**
- `README.md` — entry point for the framework: what Alice is, who it is for, tool-agnostic methodology framing. (Status: draft; will be re-frozen per OSS prep plan Card A.)
- `METHODOLOGY.md` — top-level orientation to the methodology.
- `methodology/` — methodology corpus (the 5-step process the framework prescribes).
- `templates/` — fillable templates referenced from the methodology.
- `references/` — additional reference docs (e.g. tool-mapping guide).
- `worked-examples/` — narrated walkthroughs of Alice applied to a context.
- `CHANGELOG.md` — this file (Keep a Changelog format, SemVer-aligned).

### Changed
- N/A (first tracked packaging entry).

### Deprecated
- N/A (first tracked packaging entry).

### Removed
- N/A (first tracked packaging entry).

### Fixed
- N/A (first tracked packaging entry).

### Security
- N/A (first tracked packaging entry).

<!--
Link references (added at tag time):

  [v0.1.4]: https://github.com/042Trix/Alice/releases/tag/v0.1.4
  [v0.1.3]: https://github.com/042Trix/Alice/releases/tag/v0.1.3
  [v0.1.2]: https://github.com/042Trix/Alice/releases/tag/v0.1.2
  [v0.1.1]: https://github.com/042Trix/Alice/releases/tag/v0.1.1
  [0.1.0]: https://github.com/042Trix/Alice/releases/tag/v0.1.0
-->

## 2026-08-11 — _inbox process-artifact cleanup

Removed 29 process artifacts from `_inbox/` per t_275f1fbf classification (7 gap records INTEGRATED + 16 Card H polish reports + 6 `_polish-archive` duplicates).

## [Unreleased] — 2026-08-18 v0.2.1 → v0.2.4 sub-check amendments (4 amendments per council synthesis 2026-08-18 t_e27e096b)

These four amendments ship the structural sub-checks the council synthesis t_e27e096b rated as the priority follow-ups for the graph-readiness rubric. They are additive refinements of v0.2.0; the v0.2.x branch is the stable line. Each amendment bumps the doc frontmatter `version:` field + appends an audit-line per op-guard-11.

### v0.2.1 — item-1 doc-accurately-describes-behavior sub-check (t_fc737daa, MEDIUM priority)

The rubric's "Source of truth" column for item 1 (Methodology doc exists + cross-references the loop) now requires the methodology doc to **accurately describe the work-graph's current behavior** — the verifier reads the doc + walks the actual graph (master ticket + children + dispatcher routing + verifier chain); a methodology doc that exists + cross-references the loop but is STALE (its per-node descriptions no longer match the actual child tickets' worker profiles / scopes / outcomes) FAILS this gate. Paired-wiki integrity (op-guard-5) confirms the doc is on disk + cross-references the loop; this sub-check confirms the doc is CORRECT — the substance matches the form. Part 4 §A gains the internal-consistency paragraph; Part 5 check 3 gains sub-clause (d) behavior-vs-doc walk. Frontmatter: `amended_by += [[ticket:t_fc737daa]]`.

### v0.2.2 — item-8 run-success sub-check (t_cdbf1575, MEDIUM priority)

The rubric's "Source of truth" column for item 8 (Loop used end-to-end) now requires the run's terminal outcome is `done` per `kanban show <loop_run_card>` — not `blocked`, not `running-then-blocked`, not `running-then-cancelled`. Substance over label: a `result=success` audit-line paired with a `blocked` terminal status does NOT satisfy item 8. New no-storm + no-escalation discipline: the card's event log shows < 3 retries (>=3 in a 5-minute window signals a retry storm); the audit-line log shows no `operator-action-dm` triggered by this run (per op-guard-13). Part 5 check 2 gains sub-clauses (a)/(b)/(c); on item-8 failure the verifier returns `compliance: fail — item-8-failed-run — <loop_id> v<X.Y.Z>: terminal=<status> after <N> retries, <M> escalation-dm events`. Part 4 §C Operational contract gains the Error-handling discipline bullet. Frontmatter: `amended_by += [[ticket:t_cdbf1575]]`.

### v0.2.3 — item-4 behavior-vs-tautology sub-check (t_63385722, HIGHEST priority per Seat 1 Architect + Seat 3 Operator-facing + Seat 5 Adversarial; 5/5 seats)

The rubric's "Source of truth" column for item 4 (Regression suite exists + green) now requires the regression suite to assert observable behavior, not implementation tautologies — at least one test per regression suite asserts behavior visible from the operator's chat (a master ticket filed, a Discord DM delivered, a kanban comment posted, an audit-line appended). The companion verification discipline is Part 5 check 1 (a) (b) (c). Part 4 §B Test coverage gains a new bullet "Behavior-asserting tests" that codifies the sub-check. The sub-check is the YES/NO contract: if the regression suite can be green without any test asserting end-to-end behavior visible from chat, the loop fails item 4. Frontmatter: `amended_by += [[ticket:t_63385722]]`.

### v0.2.4 — item-4 3-class verifier classifier + PASS/FAIL rule (t_d187a655, amend-68 HIGHEST priority)

The v0.2.3 amend-1 shipped the YES/NO contract but did not ship the canonical verifier procedure that proves the contract is enforceable. The v0.2.4 amendment closes that gap: Part 4 §B "Behavior-asserting tests" bullet is upgraded to carry the 3-class verifier classifier spec — ✅ Behavior (observable output: function return, file content, API response, database row, audit-line written, kanban_create payload, Discord DM delivered, YAML field flipped at runtime), ⚠️ Tautology (implementation matches itself: dispatcher.is_pinned() after the flip that just toggled the flag; assert yaml_status == 'live' after the code that flipped it ran), ❌ Trivial (assert True / 1 == 1 / except: pass / empty function body). Part 5 check 1 gains sub-clauses (d) classifier procedure + (e) PASS/FAIL rule (item 4 PASSES only when `behavior_count >= 1` AND `tautology_count == 0` AND `trivial_count == 0`; any tautology or trivial blocks item 4). Part 3 item 4 row's Source of truth column updated to reference both v0.2.0 amend-1 contract and v0.2.4 classifier procedure. Regression suite: `~/.hermes/tests/test_graph_readiness_rubric_v0_2_0.py` (4 cases, all green in 0.02s) verifies (1) all Behavior → PASS; (2) one Tautology → FAIL; (3) one Trivial → FAIL; (4) mixed (2 Behavior + 1 Tautology + 1 Trivial) → FAIL. Frontmatter: `version: 0.2.3 → 0.2.4`; `updated: 2026-08-18T18:00:00Z → 11:40:00Z`; `amended_by += [[ticket:t_d187a655]]`. Companion instance-side tool (`~/.hermes/tools/verifier_test_classifier.py`) ships via a separate coder/verifier ticket per the spec-first flow op-guard-16.

