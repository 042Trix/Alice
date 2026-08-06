---
id: alice-changelog
created: 2026-08-04T15:30:00Z
updated: 2026-08-05T20:30:00Z
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

## [Unreleased] — LICENSE pick
- `methodology/00-decide-ticket-naming.md` — new methodology doc codifying the ticket-naming convention v2 (the `AREA (<Section>) TOPIC <N> (<descriptive name>)` format) for the `alice-framework` board. Effective 2026-08-05. Source: ticket `t_d17af817`.
- `methodology/04d-decide-flow-spec.md` — new methodology doc codifying the **6-field flow spec** (goal, inputs, outputs, success criteria, retry parameters, escalation). Defines each field, names per-field anti-patterns, and ships a worked example for a one-page cheat-sheet flow. Closes the gap in the GRAPH area between the work-graph substrate (04a) and the iteration-loop primitive (06). Source: ticket `t_dec4b9ce`.
- `methodology/06a-decide-retro.md` — new methodology doc codifying the **post-execution retro + 1-day-open human-feedback pattern** for every graph execution. Defines the 3 reflection fields (what worked / what didn't / what to change), the 24h hard-timeout window, the auto-apply step (follow-up ticket routing), and a worked example (v0.2 release). Sibling to `methodology/06-iteration-loop.md`; closes the gap in the GRAPH area where the feedback element points at a protocol that didn't yet exist. Source: ticket `t_a57e2b9f`.
- `methodology/06-iteration-loop.md` — added a forward-link in the "How this maps to GRAPH / retro + feedback" section pointing readers at `methodology/06a-decide-retro.md` for the full retro protocol; added `06a-decide-retro.md` to the See-also list. No primitive changes. Source: ticket `t_a57e2b9f` (paired wiki-update per strike-5).

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

## [Unreleased] — blocked-ticket recovery procedure (paired wiki-update per strike-5)
- `methodology/06b-decide-blocked-ticket-recovery.md` — new methodology doc codifying the **blocked-ticket recovery procedure** for the chat-side agent (jarvis) on the `alice-framework` and `agent-resources` boards. Defines: 4 trigger conditions (blocked >1h, running-stub >4h, running-stale-heartbeat >2h, running-no-completion >1h), 4-step investigation procedure (check file system, check process, check toolset limits, decide rescue action), 4 rescue actions in priority order (re-scope, close as filed-in-error, file kill-stuck-process follow-up, document in audit log), and cadence (30-min check). Codifies the operator's 2026-08-05 direction ("we aren't proactively responding to blocked tasks again") as a standing rule. Also documents why this is a methodology (not a strike rule) and the 3-failure promotion criteria. Source: ticket `t_78ffd7e5`.
- `templates/AGENTS.md.template` — added **Section 7a (Blocked-ticket recovery procedure)** with the 4 trigger conditions, 30-min cadence, 4-priority rescue actions, and the audit-log schema. The new section is a navigation summary; the canonical reference is `methodology/06b-decide-blocked-ticket-recovery.md`. Paired wiki-update per strike-5. Source: ticket `t_78ffd7e5`. Template still under 20,000-char limit (well under; ~8.2 KB).

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
Link references are added at tag time, not now. The repo is not yet public;
fabricating a GitHub URL here would be a documentation bug. Pattern at tag time:

  [Unreleased]: https://github.com/<owner>/alice-framework/compare/v0.1.0...HEAD
  [0.1.0]: https://github.com/<owner>/alice-framework/releases/tag/v0.1.0
-->
