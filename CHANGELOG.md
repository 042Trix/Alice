---
id: alice-changelog
created: 2026-08-04T15:30:00Z
updated: 2026-08-08T00:00:00Z
title: "Alice — Changelog"
type: framework-changelog
status: draft
source: operator
tags: [kind:framework, kind:changelog, domain:agent-ops, project:alice]
links: []
---

# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

> **Note on this `0.1.0` entry:** This is the **changelog stub**, not a release announcement. The entry below names what exists in the repo at the time of writing, and it will be revised at tag time to reflect the final packaging artifacts. The first public release ships only after every box in the OSS prep plan's release-readiness checklist is checkable. See `2-ATOMIC/concepts/alice-oss-prep-plan-2026-08-04.md`.

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
- Cross-references: GBU review at `/Users/homestead/.hermes/cache/documents/doc_6e594c40214f_alice_GBU_0.1.0.txt`; council log at `/Users/homestead/Documents/alice-framework/_inbox/council-v0.1.0-GBU-review-2026-08-06.md`; maintenance council log at `/Users/homestead/Documents/alice-framework/_inbox/council-v0.1.1-build-vs-maintain-review-2026-08-06.md`.

## [Unreleased] — LICENSE pick
- Human-readable retro digest contract added: `methodology/M-decide-human-digest.md` and `templates/human-digest.md.template`. The technical retro remains the canonical audit record; the digest supplies a plain-language headline, six-axis summary, recommendations, collapsible evidence anchors, operator observations, and Accept / Reject / Defer decisions. Retro methodology and x-article-review verification now cross-reference the digest. Environment-side generation, comments, and optional attachment delivery are delegated to implementation ticket `t_2ef12055`. **v0.2.0 (2026-08-08, t_4d1de050)**: section order rearranged per operator direction — actionable top tier (sections 1-3), transitional interpretation layer (sections 4-5), operator action surface (sections 6-7), technical-detail bottom tier (sections 8-10). Individual council / agent responses moved from section 5 to section 9. See CHANGELOG entry "human-digest section order v0.2.0 (operator correction)" below for the full change set.
- `methodology/00-decide-ticket-naming.md` — new methodology doc codifying the ticket-naming convention v2 (the `AREA (<Section>) TOPIC <N> (<descriptive name>)` format) for the `alice-framework` board. Effective 2026-08-05. Source: ticket `t_d17af817`.
- `methodology/04d-decide-flow-spec.md` — new methodology doc codifying the **6-field flow spec** (goal, inputs, outputs, success criteria, retry parameters, escalation). Defines each field, names per-field anti-patterns, and ships a worked example for a one-page cheat-sheet flow. Closes the gap in the GRAPH area between the work-graph substrate (04a) and the iteration-loop primitive (06). Source: ticket `t_dec4b9ce`.
- `methodology/06a-decide-retro.md` — new methodology doc codifying the **post-execution retro + 1-day-open human-feedback pattern** for every graph execution. Defines the 3 reflection fields (what worked / what didn't / what to change), the 24h hard-timeout window, the auto-apply step (follow-up ticket routing), and a worked example (v0.2 release). Sibling to `methodology/06-iteration-loop.md`; closes the gap in the GRAPH area where the feedback element points at a protocol that didn't yet exist. Source: ticket `t_a57e2b9f`.
- `methodology/06-iteration-loop.md` — added a forward-link in the "How this maps to GRAPH / retro + feedback" section pointing readers at `methodology/06a-decide-retro.md` for the full retro protocol; added `06a-decide-retro.md` to the See-also list. No primitive changes. Source: ticket `t_a57e2b9f` (paired wiki-update per op-guard-5).

- Hermes infrastructure parity-3/3 (ticket `t_c029a0bd`) — ships the **retro-1d-window** enforcement: every `0-INBOX/retro-<graph-id>-<date>.md` carries frontmatter `created: <iso>` + `closed: null`; a 24h tick (cheap secondary check wired into `jarvis_stuck_board_monitor.chat_preflight`) auto-archives the file to `.archived-retro-*.md`. If the body contains `<!-- comment: ... -->`, the retro closes with operator feedback folded in; otherwise the no-feedback marker is appended. Implementation: `~/.hermes/tools/retro_one_day_window.py` (CLI + importable module); test suite `~/.hermes/tests/test_retro_one_day_window.py` (17 cases: 4 mandatory acceptance criteria + 13 belt-and-suspenders — young/closed/no-frontmatter/missing-created/dot-prefix/non-retro-name/idempotency/reversibility/case-insensitive/batch/dry-run/frontmatter-preservation/audit-line/JSON). Closes the parity gap for `t_a57e2b9f` (GRAPH / retro + feedback): the methodology doc `06a-decide-retro.md` is now backed by working operator code, not aspirational design. Sister to `t_480d925b` (post_graph_retro.py, the producer) and `t_896a3972` (cron registration, the trigger).

### Changed
- `methodology/00-decide-ticket-naming.md` — **superseded v2 with v3** of the ticket-naming convention on 2026-08-05 afternoon. Format simplified from `AREA (<Section>) TOPIC <N> (<descriptive name — ...>)` (single line) to the two-line `AREA\n<topic-name>: <descriptive name>` shape. Dropped the parenthetical section label, dropped the TOPIC number, and dropped the em-dash from the topic-name line. The colon (`:`) is now the canonical separator between topic name and descriptive name; em-dash survives only inside the descriptive name for sub-clauses. Topic numbering is implicit in filing order. Existing tickets (`t_c490ace1`, `t_b50f1546`, `t_d17af817`, and the three HARNESS tickets filed 2026-08-05) keep their old titles per the out-of-scope rule; the v3 format applies to all future filings. Source: ticket `t_d727f13f`.
- MIT License selected (operator decision 2026-08-04).
- LICENSE file at repo root with verbatim MIT text, year 2026, copyright holder TBD.
- CHANGELOG.md line 25 updated: CC-BY-4.0 default → MIT decision.
- CITATION.cff license field updated: [OPERATOR: SPDX identifier from Card C] → MIT.

### Planned
- LICENSE at repo root — verbatim MIT text, year 2026, copyright holder TBD. Rationale: future-proofs if code is added; matches reference repo `hermes-multi-agent-workflow`. See OSS prep plan Card C.
- CONTRIBUTING.md, CODE_OF_CONDUCT.md, SECURITY.md, GOVERNANCE.md at repo root or `docs/`. See OSS prep plan Card B.
- CITATION.cff at repo root for research/methodology citation. See OSS prep plan Card E.
- Public glossary (`GLOSSARY.md` or `references/glossary.md`) covering the 4-term agent typology and method-not-instance vocabulary. See OSS prep plan Card G.
- Link audit (`doc-cross-reference-audit`) and any subsequent link-graph fixes. See OSS prep plan Card F.
- Public-technical-writing polish pass across the 46-file corpus. See OSS prep plan Card H.
- Signed tag `v0.1.0`; GitHub release page; `compare/` link references. See OSS prep plan release-readiness checklist.

## [Unreleased] — blocked-ticket recovery procedure (paired wiki-update per op-guard-5)
- `methodology/06b-decide-blocked-ticket-recovery.md` — new methodology doc codifying the **blocked-ticket recovery procedure** for the chat-side agent (jarvis) on the `alice-framework` and `agent-resources` boards. Defines: 4 trigger conditions (blocked >1h, running-stub >4h, running-stale-heartbeat >2h, running-no-completion >1h), 4-step investigation procedure (check file system, check process, check toolset limits, decide rescue action), 4 rescue actions in priority order (re-scope, close as filed-in-error, file kill-stuck-process follow-up, document in audit log), and cadence (30-min check). Codifies the operator's 2026-08-05 direction ("we aren't proactively responding to blocked tasks again") as a standing rule. Also documents why this is a methodology (not an operational guard) and the 3-failure promotion criteria. Source: ticket `t_78ffd7e5`.
- `templates/AGENTS.md.template` — added **Section 7a (Blocked-ticket recovery procedure)** with the 4 trigger conditions, 30-min cadence, 4-priority rescue actions, and the audit-log schema. The new section is a navigation summary; the canonical reference is `methodology/06b-decide-blocked-ticket-recovery.md`. Paired wiki-update per op-guard-5. Source: ticket `t_78ffd7e5`. Template still under 20,000-char limit (well under; ~8.2 KB).

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

  [v0.1.2]: https://github.com/042Trix/Alice/releases/tag/v0.1.2
  [v0.1.1]: https://github.com/042Trix/Alice/releases/tag/v0.1.1
  [Unreleased]: https://github.com/042Trix/Alice/compare/v0.1.2...HEAD
  [0.1.0]: https://github.com/042Trix/Alice/releases/tag/v0.1.0
-->
