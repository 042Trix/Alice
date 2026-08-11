---
id: alice-changelog
created: 2026-08-04T15:30:00Z
updated: 2026-08-11T11:05:00Z
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
- Cross-references: GBU review at `/Users/homestead/.hermes/cache/documents/doc_6e594c40214f_alice_GBU_0.1.0.txt`; council log at `/Users/homestead/Documents/alice-framework/_inbox/council-v0.1.0-GBU-review-2026-08-06.md`; maintenance council log at `/Users/homestead/Documents/alice-framework/_inbox/council-v0.1.1-build-vs-maintain-review-2026-08-06.md`.

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
