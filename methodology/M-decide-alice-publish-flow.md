---
id: alice-methodology-decide-alice-publish-flow
created: 2026-08-08T20:30:00Z
updated: 2026-09-08T15:30:00Z
title: "Methodology M — Decide the alice-publish flow (the canonical publish pipeline with STEP 0 version-discipline pre-flight + 6 gates + topic-only release message)"
type: methodology
status: draft
source: alice-framework
version: 1.0.1
tags: [kind:methodology, kind:flow-spec, kind:publish, kind:git, kind:gate, kind:pre-verify, kind:release, kind:version-discipline, project:alice]
confidence: 0.95
alice-ticket: t_f38bd852
amended_by: ["t_fdba17e5", "t_a92c1f88", "t_4d9bd6c5", "t_f38bd852", "t_3203ac1f", "t_653117e5", "t_003a07e2"]
parent-meta: t_55240868
companion: ["[[methodology/04d-decide-flow-spec.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[methodology/04c-decide-master-ticket.md]]", "[[methodology/00-decide-ticket-naming.md]]", "[[methodology/M-decide-instance-vs-framework.md]]", "[[methodology/M-decide-graph-readiness.md]]", "[[methodology/M-decide-x-article-review-flow.md]]", "[[references/instance-leak-check-spec.md]]", "[[templates/check_version_discipline.py.template]]", "[[templates/instance-leak-check.py.template]]"]
links: ["[[methodology/04d-decide-flow-spec.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[methodology/04c-decide-master-ticket.md]]", "[[methodology/00-decide-ticket-naming.md]]", "[[methodology/M-decide-instance-vs-framework.md]]", "[[methodology/M-decide-versioning-discipline.md]]", "[[methodology/M-decide-graph-readiness.md]]", "[[methodology/M-decide-x-article-review-flow.md]]", "[[2-ATOMIC/rules/op-guard-16-spec-first-flow-2026-08-05.md]]", "[[2-ATOMIC/rules/op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md]]", "[[2-ATOMIC/rules/op-guard-19-pre-verify-artifact-state-2026-08-08.md]]"]
teaching-example: true
---

# Methodology M — Decide the alice-publish flow (v1.0.0)

> The `alice-publish` loop publishes Alice releases to GitHub end-to-end. This methodology is the canonical contract: a 7-step deterministic pipeline (instance-leak gate + version-discipline pre-flight + pre-verify gate + work-completion gates + commit + push + tag + GitHub release + audit-line + Discord delivery) with 6 verifier-gated gates. STEP 0 (version-discipline pre-flight) closes the v0.1.4 → v0.1.7 skip-pattern surfaced by `t_25edf8f7`; STEP 1 (instance-leak gate) closes the framework-vs-instance leak surface surfaced by `t_7aa96032`; the remaining 4 gates close the drift surface surfaced by the alice-framework v0.1.2 push incident (`t_ecb73d81`, 2026-08-08). The Discord delivery (STEP 11) follows the topic-only release-message format (no article author/URL/handle) per Retro-H body observation #3.

> **v1.0.0 canonical-promote (2026-08-17, t_f38bd852):** this doc is promoted from the instance-side path (`~/.hermes/methodology/M-decide-alice-publish-flow.md`) to its canonical alice-framework home. The instance-side copy is retained as a 1-line pointer. The per-loop audit (`M-decide-graph-readiness.md` Part 3 item 1) now checks this path; the doc carries a node-type discipline section (Part 7) to also satisfy item 9. The version bumps `0.4.0 → 1.0.0` because the alice-framework-side is now the canonical surface, not a downstream copy.

## Source incidents

### `t_ecb73d81` alice-framework v0.1.2 push (2026-08-08) — the 4-gap pre-block audit that motivated this doc

The v0.1.2 push ticket was filed on 2026-08-08 with claim-vs-reality gaps:

| Claim in ticket body | Actual on-disk state |
|---|---|
| "CHANGELOG.md v0.1.2 entry" | Four separate `[Unreleased]` sub-sections; no `v0.1.2` entry yet (consolidated in the same ticket before commit) |
| "CITATION.cff at v0.1.1" | At `v0.1.0` with explicit operator-gate warning |
| "Per-file versions aligned" | `00` at `v0.4.0`, `03b` had no version field, `06a` at `v0.1.3` |
| "tag v0.1.2 will be created" | No `v0.1.2` tag existed; coder pre-block audit identified all 4 gaps |

Coder pre-block audit correctly blocked the push (Path C: full release with re-opened CITATION.cff operator gate). The cost of recovering from drift (4-gap pre-block audit + operator re-authorization + manual CHANGELOG consolidation) is more expensive than the upfront gate. The first v0.4.0 of this flow spec ships those gates as load-bearing steps. The codership cost of recovering from drift is the documented rationale; the v0.1.0 `alice-publish` loop had the 7-step pipeline but lacked the gate-quality bars the v0.1.2 incident surfaced.

### `t_25edf8f7` (2026-08-11) — the v0.1.4 → v0.1.7 skip-pattern that motivated STEP 0

A retrospective audit-reorg ticket was filed as `[CODER] alice-publish v0.1.7` for PATCH-level work. The v0.1.4 → v0.1.7 skip was a 3-version PATCH jump that bypassed `check_version_discipline.py`'s strict-semver contract. Operator direction 2026-08-11 (verbatim: *"0.1.7? We haven't even shipped 0.1.5 or defined anything for 0.1.6. Need some durable logic for how we are handling symantec versioning."*). The canonical-rule extension to `methodology/M-decide-versioning-discipline.md` Parts 8-10 (Keep a Changelog 1.1.0 + Semver.org 2.0.0 + Conventional Commits 1.0.0 + GitHub Releases docs) + STEP 0 (version-discipline pre-flight) close this surface.

### `t_7aa96032` (2026-08-11) — the framework-vs-instance distinction that motivated STEP 1 (instance-leak gate)

Six `M-decide-*.md` files in `methodology/` described Hermes-instance operational flows, not Alice framework concepts; the next `alice-publish` run would ship instance-specific flow to GitHub as Alice framework content. The framework-vs-instance distinction rule (`methodology/M-decide-instance-vs-framework.md` v0.1.0) + the instance-leak check (`references/instance-leak-check-spec.md` + `templates/instance-leak-check.py.template`) close this surface; STEP 1 of this flow runs the check at publish time as a hard gate.

## Part 1: Purpose

The `alice-publish` flow takes one Alice release version (vX.Y.Z) from "contributing tickets complete on disk" to "GitHub release published + audit line appended + master ticket closed + operator-facing Discord message delivered." The flow is the operator's contract for pushing to GitHub: it names the gates, the inputs, the outputs, and the verifier that confirms each step landed.

Without this doc, the `alice-publish` loop at `~/.hermes/loops/hermes.yaml` is a 7-step pipeline with no gate-quality bars — a worker can run the steps, the verifier can check the steps, but no upstream check exists for "is the work in a state where these steps will succeed?" The v0.1.2 push incident was the failure case: the steps ran in the right order, but the inputs were inconsistent and a pre-flight check would have caught it.

This doc is the canonical reference for the `alice-publish` flow. The instance side is the `alice-publish` loop v0.5.1 at `~/.hermes/loops/hermes.yaml` (demoted to `status: live-pending-test` per op-guard-30 enforcement on 2026-08-17, META t_55240868; the loop and the intent file are derived from this doc and conform to its contract per op-guard-17).

## Part 2: The flow spec (adapted from `methodology/04d-decide-flow-spec.md`)

The canonical 6-field flow spec in `04d-decide-flow-spec.md` is **Goal / Inputs / Outputs / Success Criteria / Retry Parameters / Escalation Process**. The publish flow adapts the pattern to its deterministic git/gh nature: the publish flow's "retry" and "escalation" are uniform across all steps (transient → retry, hard-failure → operator-blocked), so the spec uses **Process (7 steps + 6 gates)** as the structural field and **Skill version** as the canonical-instance reference (instead of the more free-form retry/escalation fields). The verifier-gated acceptance criteria substitute for the canonical "Success Criteria" field with a sharper "the verifier checks these N conditions" framing.

### Field 1 — Goal

Publish one Alice release version (vX.Y.Z) to GitHub end-to-end with verifier-gated gates at every step. **Multi-version cards rejected** (one release per invocation; one version per master ticket).

### Field 2 — Inputs

- **`version: v<X.Y.Z>`** — required in the master ticket body. The version MUST match the `git tag -l` invariant (no such tag exists pre-publish; tag created during the ship at STEP 8).
- **`path: alice-publish`** — required in the master ticket body. Routes the dispatcher to this loop. Per `methodology/04c-decide-master-ticket.md` Master Title Discipline, the title carries the descriptive topic; the URL/version goes in `## Source` block.
- **`## Contributing tickets`** — every doc-writer + verifier ticket that contributed to this release. Lives in the master ticket body under that heading. The list is the per-step verification scope (STEP 3 + STEP 4 check each ticket).
- **`## Verified state`** — the captured output of `python3 ~/.hermes/tools/verify_artifact_state.py ~/Documents/alice-framework --ticket-id T_xxx` at ticket-filing time. Per op-guard-19, this section is mandatory for every commit/tag/release/push ticket; the alice-publish flow inherits the rule.
- **Operator authorization** — per the strike-8 emergency-exception protocol, every commit/tag/release/push requires operator authorization recorded as `kanban_comment` on the master ticket before the publish worker starts. The comment references the operator's authorization (chat timestamp, ticket comment id, or in-line quote).
- **CITATION.cff operator-gate state** — read CITATION.cff at pre-verify time; record operator-gate warnings in the master ticket body under `## CITATION.cff gate state`.

### Field 3 — Outputs

- **Commit on `main`** with structured message: `ALICE v<X.Y.Z>: <one-line summary>` followed by a body with stats (files changed, tickets closed, line counts) and source attribution.
- **Tag `v<X.Y.Z>`** annotated with release notes summary.
- **GitHub release** at `https://github.com/042Trix/Alice/releases/tag/v<X.Y.Z>` with structured notes (sections: `## What's new` + `## Stats` + `## Source` + `## License` + `## Author`).
- **Audit line** in `~/Documents/HermesVault/log.md`: `## [ISO8601-UTC] alice-publish — v<X.Y.Z> — result=<success|failure|refused> — release=<url>`. Appended via `vault_log.append_audit_line` per op-guard-11 (vault log.md is append-only).
- **Master ticket closed** via `kanban_complete` with the release URL in `--result` field and a structured handoff in `--metadata` (artifact path, release URL, contributing ticket ids).
- **Operator-facing Discord release message** (v0.3.0, t_a92c1f88) — posted to the operator's connected chat channel via `~/.hermes/tools/post_alice_release_discord.py` after STEP 9 completes. The message MUST follow the canonical topic-only format (see `### Output 6 — Operator-facing Discord release message format` below). Format: `Alice v<X.Y.Z>: <topic-1>, <topic-2>, ...`. NO article author, NO article URL, NO source handle. The article (if any) stays in the master ticket body's `## Source` block + the CHANGELOG `## Source` sub-section + the GitHub release notes `## Source` section; the Discord release message is topic-only. Source: operator 2026-08-09 Retro-H body observation #3 (verbatim: *"The message on the push should mention what topic was changed but not the article author, the article was just the inspiration for the change."*).

#### Output 6 — Operator-facing Discord release message format

The canonical release-message format is:

```
Alice v<X.Y.Z>: <topic-1>, <topic-2>, ...
```

- **Version prefix:** literal `Alice v<X.Y.Z>` (no brackets, no Markdown, no version control tag prefix).
- **Topic list:** one to N comma-separated topic slugs. Topics are the high-level change themes (e.g., `scoped node-admission rule`, `parallel fan-out/fan-in contract`, `Alice graph definition restatement`, `3-question headline rubric`). The topic slugs are operator-facing plain language, not internal jargon.
- **Empty topic list:** when the release contains no thematic changes (e.g., a doc-only patch + CITATION.cff bump), the message becomes `Alice v<X.Y.Z>: documentation-only` (the literal `documentation-only` token).
- **No article attribution:** the message MUST NOT name the article author, the article URL, the source handle, or the post ID. The article is one data point in the methodology's research; the release is Alice's, not the article's.
- **No bracketed versions:** the `[v<X.Y.Z>]` form lives in CHANGELOG.md and the GitHub release notes — separate surfaces with their own attribution rules.

**Surface attribution summary (where the article reference does and does not live):**

| Surface | Article reference lives here? |
|---|---|
| Operator-facing Discord release message (this Output 6) | **NO** — topic-only. |
| Master ticket body `## Source` block | YES — full URL + author byline + post ID (audit trail). |
| `~/Documents/HermesVault/log.md` audit line | Optional — may cite article URL as the input that motivated the change, but the change itself is described by topic. |
| Methodology amendment rationale | YES — may cite article as one source among several under `## Sources` or per-finding evidence-anchor section. |
| `CHANGELOG.md` `## Source` sub-section | YES — may cite article as one source among several. |
| GitHub release notes `## Source` section | YES — may cite article as one source among several. |
| GitHub release title | NO — the title is `Alice v<X.Y.Z>`. |

**Implementation.** The Discord delivery is owned by `~/.hermes/tools/post_alice_release_discord.py` (shipped via v0.3.0 instance work; invoked by STEP 9 after the GitHub release succeeds). The script reads the alice-framework CHANGELOG `## [v<X.Y.Z>]` entry, extracts the topic list (via the standard sub-sections `### Added` + `### Changed` + `### Fixed` + `### Out of scope`), and posts the canonical-format message via `send_dm_attachments`. Idempotent on (`version`, `release_url`): a release that has already produced a Discord message is skipped on re-run. The script header documents the canonical format + the `## Source`-block exception + the audit-line format. The 4-case regression suite at `~/.hermes/tests/test_post_alice_release_discord.py` covers: (1) release with 3 topics → `Alice v0.1.2: scoped node-admission rule, parallel fan-out/fan-in contract, Alice graph definition restatement`; (2) release with 1 topic → `Alice v0.1.2: <topic>`; (3) release with no topics (doc-only) → `Alice v0.1.2: documentation-only`; (4) re-run on already-delivered release → idempotent skip.

**Cross-reference.** The x-article-review flow (`methodology/M-decide-x-article-review-flow.md` Part 3 `## Release-message discipline`) is the canonical source for the topic-only release-message rule; this Output 6 is the alice-publish-flow's instance-side rule. The rule lives at the x-article-review methodology level because the rule is about cross-flow attribution discipline (the article is the input to multiple flows; the release message format must be consistent across them). This doc (alice-publish) is the publisher of the format; the x-article-review doc is the owner of the rule.

### Field 4 — Process (7 steps + 6 gates)

The 7 steps run in order. Each step is verifier-gated (the gate is the verifier check that runs before the worker advances). The 6 NEW gates in v0.2.0+v0.4.0 (STEP 0, STEP 1, STEP 2, STEP 5, STEP 6, plus the per-step verifier continuation in STEPs 7-9) close the drift surface from the v0.1.2 incident (v0.2.0), the v0.1.4 → v0.1.7 skip-pattern from `t_25edf8f7` (v0.4.0 / STEP 0), and the framework-vs-instance leak surface from `t_7aa96032` (v1.0.0 / STEP 1).

#### STEP 0 — Version-discipline pre-flight (new in v0.4.0)

- **Run:** `python3 ~/.hermes/tools/check_version_discipline.py --proposed-version <X.Y.Z> --last-release <last-tag> --working-tree-changes <diff-stat>`.
- **Gate (canonical rule):** the script verifies — (a) the proposed version is exactly `<last-tag> + 1` in the appropriate semver position (PATCH `+0.0.1`, MINOR `+0.1.0` with PATCH→0, MAJOR `+1.0.0` with MINOR+PATCH→0); (b) the proposed version matches the actual change type (MAJOR / MINOR / PATCH) derived from the working-tree changes per `methodology/M-decide-versioning-discipline.md` Part 8.1 (batch-or-split rule, auto-derivable from Conventional Commits prefixes); (c) the version does not skip (a skip from `v0.1.4` to `v0.1.7` is REFUSED); (d) a MAJOR bump requires a `[HUMAN ACTION]` child ticket id in the master body under `## Operator authorization for MAJOR bump`.
- **Gate (operator override):** rare. Per `methodology/M-decide-versioning-discipline.md` Part 8.1 §Operator override, if the operator explicitly overrides the canonical rule (e.g., MINOR-first-then-PATCH to satisfy a compliance-gate sequencing constraint), the master ticket body MUST carry a `## Operator override (Sequence B)` block citing the chat timestamp + the rationale. The override is auditable, not silent.
- **Where the script lives:** `templates/check_version_discipline.py.template` (canonical template, friend-portable) → `~/.hermes/tools/check_version_discipline.py` (instance-side implementation).
- **Output captured:** `git log <last-tag>..HEAD` parsed with Conventional Commits prefixes; the computed next-version label; the override check (if MAJOR); the gate decision (PASS / REFUSE with reason).
- **Failure mode:** REFUSE → patch the proposed version to the computed label (or add the operator authorization block), re-run the script, paste the new output into the master ticket body under `## Verified state (STEP 0)`. Do NOT advance to STEP 1 without the section present and PASS.
- **Cross-reference:** `methodology/M-decide-versioning-discipline.md` Part 4 (the pre-flight gate contract), Part 8.1 (the batch-or-split rule the script implements), Part 10 (the v0.1.5/v0.1.6 application that this gate is wired to enforce).

#### STEP 1 — Instance-leak gate (new in v1.0.0)

- **Run (operator-run strict path):** `python3 ~/.hermes/tools/check_instance_leaks.py ~/Documents/alice-framework --ticket-id T_xxx` (no flag). Gate: exit 0; any H1/H2/H3/H4/H5/X1 leak halts the publish.
- **Run (v1.0.1 dryrun variant — synthetic release pipelines):** same command with `--allow-deliberate-fixtures` appended: `python3 ~/.hermes/tools/check_instance_leaks.py ~/Documents/alice-framework --ticket-id T_xxx --allow-deliberate-fixtures`. The dryrun variant is selected automatically when the master ticket body carries a version string matching `v<X.Y.Z>-dryrun` (e.g. `v0.5.1-dryrun` per `t_ac12ffb3`'s master). The flag downgrades the 2 on-disk H1 deliberate fixtures (`methodology/M-decide-h1-leak.md` + `methodology/M-decide-instance-leak-test.md`, per `references/instance-leak-check-spec.md` Part 2 line 139) from `ERROR` to `INFO` and exits 0; genuine H1 leaks (files matching the structural pattern but lacking the canonical `kind:fixture` + `leak:deliberate` tag tokens) still fire `ERROR` regardless of the flag. The operator-run variant stays strict (no flag, exit 1 on any H1) — the dryrun variant is a synthetic-pipeline test surface, not a release-side override. The `M-decide-h1-leak.md` + `M-decide-instance-leak-test.md` fixtures MUST remain at `methodology/` (regression suite depends on them; `CHANGELOG.md` line 120 codifies "MUST keep firing"; `references/instance-leak-check-spec.md` Part 2 line 139 codifies "cannot be removed or fixed"). Per `references/instance-leak-check-spec.md` Part 2 line 168: "the publish gate enforces the stricter rule but operators can manually override" — the dryrun variant IS the manual override, automated for synthetic release pipelines. amended_by_v1_0_1: t_3203ac1f.
- **Gate:** the script returns exit 0 (no H1/H2/H3/H4/H5/X1 leaks per `references/instance-leak-check-spec.md`). The master ticket body MUST carry a `## Verified state (STEP 1)` section with the script's full output captured at ticket-filing time.
- **Output captured:** per-file leak classification (A/H/U), the H-pattern counts (H1 instance kind frontmatter, H2 `~/.hermes/` body references, H3 profile-name patterns, H4 `~/.hermes/loops/...` paths, H5 `~/.hermes/skills/...` paths), the X1 audit-artifact pattern count, the exit code, and the resolved list of offending files (or `clean`).
- **Failure mode:** exit 1 halts the publish immediately; the master ticket is blocked on `kind=needs_input` with the check's output as the reason, and the operator is notified via Discord. The publish worker does NOT skip, this gate; the surface is closed end-to-end before any commit.
- **Three surfaces where the gate runs:** (a) local pre-commit hook at `~/.hermes/hooks/instance-leak-pre-commit` — **ALWAYS passes `--allow-deliberate-fixtures`** so the 2 on-disk H1 fixtures don't block every commit; (b) GitHub Actions CI at `~/Documents/alice-framework/.github/workflows/ci.yml` — strict by default, operator can override with the same flag; (c) `alice-publish` loop STEP 1 (this gate) — operator-run variant stays strict; dryrun variant (`v<X.Y.Z>-dryrun` version string) passes the flag per the v1.0.1 amendment. Per `methodology/M-decide-instance-vs-framework.md` Part 4 (publishing gate).
- **Source of truth:** `methodology/M-decide-instance-vs-framework.md` v0.1.0 (the framework-vs-instance distinction rule) + `references/instance-leak-check-spec.md` (the H1-H5 + X1 pattern catalog) + `templates/instance-leak-check.py.template` (the tool-agnostic script template).

#### STEP 2 — Pre-verify gate (new in v0.2.0)

- **Run:** `python3 ~/.hermes/tools/verify_artifact_state.py ~/Documents/alice-framework --ticket-id T_xxx`
- **Gate:** the master ticket body MUST contain a `## Verified state` section with the script's full output captured at ticket-filing time. Per op-guard-19, this gate is mandatory; a ticket without the section fails pre-verify.
- **Output captured:** `git status --short`, `git tag -l`, `git log --oneline -5`, `git diff --stat HEAD`, `CITATION.cff version` + operator-gate warnings, `CHANGELOG.md` head (30 lines), per-file `version:` field scan across `methodology/*.md` + `templates/*.md.template`.
- **Failure mode:** re-run the script, paste the output into the ticket body under `## Verified state`, file or re-file the ticket. Do NOT advance to STEP 3 without the section present.

#### STEP 3 — Verify work is complete on disk

- **Run:** for each contributing ticket, check the artifact exists at the expected path (read the contributing ticket body for the artifact path; cross-reference with the per-file version field scan from STEP 2).
- **Gate:** every contributing ticket has `status=done` (per op-guard-10 v2 §1.1).
- **Failure mode:** file a follow-up ticket for any contributing ticket still in `todo` / `running` / `blocked` state. The publish worker does NOT advance until every contributing ticket is `done`.

#### STEP 4 — Verify verifier ran for each contributing ticket

- **Run:** for each contributing ticket, check the `verifier_passed` event in the run history (via `kanban_show` and the events table).
- **Gate:** every contributing ticket has a `verifier_passed` event.
- **Failure mode:** file a verifier ticket for any contributing ticket without `verifier_passed`. The publish worker does NOT advance until every contributing ticket is verifier-passed.

#### STEP 5 — CHANGELOG.md consolidation gate (new in v0.2.0)

- **Run:** read `CHANGELOG.md`; verify a single `## [v<X.Y.Z>]` entry exists. The single-entry invariant is mandatory (one entry per released version per the alice-framework release-prep discipline; multiple `[Unreleased]` blocks are the drift symptom from the v0.1.2 incident).
- **Run (if consolidation needed):** consolidate any `[Unreleased]` blocks into a single `## [v<X.Y.Z>]` entry with sub-sections for each change category (`### Added`, `### Changed`, `### Out of scope`, `### Source`, etc., following the format of the existing v0.1.2 entry).
- **Gate:** `CHANGELOG.md` has a single `## [v<X.Y.Z>]` entry with full source attribution. The bottom `<!-- Link references -->` HTML comment is updated to include the new tag URL.
- **Failure mode:** edit `CHANGELOG.md` to consolidate (with a `git mv CHANGELOG.md CHANGELOG.md.bak` backup before edit, per the alice-publish-intent atomic-constraint; restore from `.bak` if the edit fails).

#### STEP 6 — Per-file version reconciliation + CITATION.cff operator-gate check (new in v0.2.0)

- **Run:** for each file under `methodology/*.md` + `templates/*.md.template` that has a `version:` field, check whether the field matches the release version or is appropriately domain-versioned (e.g., `00-decide-ticket-naming.md` may be at `v0.4.0` for the naming-discipline amendment stream; `04c-decide-master-ticket.md` may be at `v0.1.2` for the master-ticket amendment stream).
- **Reconciliation rule:** a file is reconciled when (a) its `version:` matches the release version, (b) its `version:` is in a domain-specific amendment stream that has already shipped a different version, OR (c) the master ticket body has an explicit reconciliation note naming the file + the version-keep rationale.
- **Run (CITATION.cff operator-gate):** read `CITATION.cff`; check for operator-gate warnings (lines containing `do not edit`, `operator gate`, `re-open`, `[OPERATOR`).
- **Run (if CITATION.cff warnings present):** check the master ticket body for explicit operator authorization to re-open the gate (under `## CITATION.cff gate state` heading).
- **Gate:** every file's `version:` field is reconciled with the release version OR an explicit reconciliation note lives in the master ticket body. CITATION.cff version field matches the release version OR explicit operator authorization is recorded in the master ticket body.
- **Failure mode:** edit the file's `version:` field (atomic; `git mv` backup before edit; restore from `.bak` if the edit fails), OR add a reconciliation note to the master ticket body. The CITATION.cff operator gate is a structural protection: the gate was first opened by `t_4054ccac` + parent `t_968068b0`, re-opened by `t_ecb73d81` Path C (2026-08-08), and any further re-opening is an operator-authorized action. Operator must explicitly authorize the CITATION.cff bump (in chat, in a ticket comment, or in a kanban_comment on the master) before the publish worker proceeds. Without authorization, the publish worker blocks with reason `needs CITATION.cff operator authorization`. The publish worker does NOT advance until every file is reconciled AND the CITATION.cff gate is cleared.

#### STEP 7 — Commit

- **Run:** `git add -A` (or specific files if operator asked for surgical commit). `git commit -m 'ALICE v<X.Y.Z>: <one-line summary>'` followed by body with stats + attribution.
- **Gate:** `git status --short` is clean post-commit (no uncommitted files).
- **Failure mode:** abort the publish worker; surface the conflict to operator via `kanban_block(kind=needs_input)` with reason `<commit conflict: <message>>`.

#### STEP 8 — Push to main

- **Run:** `git push origin main`.
- **Gate:** `git log --oneline -1 origin/main` shows the new commit on remote (verify via `git ls-remote origin main` or `gh api repos/042Trix/Alice/commits/main | jq '.sha'`).
- **Failure mode:** abort + surface the push error to operator. Do NOT tag a release that did not push. If the push fails due to remote-divergence (the rare case where the remote moved between STEP 2 pre-verify and STEP 8), block the publish worker with reason `remote-divergence: pull + re-run STEP 2 pre-verify`.

#### STEP 9 — Tag + push tag + GitHub release + audit-line + Discord delivery + close

- **Run:** `git tag -a v<X.Y.Z> -m 'ALICE v<X.Y.Z>: <one-line summary>'`. `git push origin v<X.Y.Z>`.
- **Run:** `gh release create v<X.Y.Z> --title 'ALICE v<X.Y.Z>' --notes '<notes>'` (notes from STEP 5 + STEP 6 reconciliation notes, with the canonical 5 sections `## What's new` + `## Stats` + `## Source` + `## License` + `## Author`).
- **Run:** append audit line to `~/Documents/HermesVault/log.md` via `vault_log.append_audit_line` (per op-guard-11, append-only).
- **Run:** `python3 ~/.hermes/tools/post_alice_release_discord.py --release-url <url> --version v<X.Y.Z>` to post the topic-only Discord release message (per Output 6). Idempotent on (`version`, `release_url`).
- **Run:** `kanban_complete` with structured handoff carrying the release URL in `--result` field and the artifact path + release URL + contributing ticket ids in `--metadata`.
- **Gate:** `gh release list | head -1` shows v<X.Y.Z> as Latest. `git tag -l` includes `v<X.Y.Z>`. `gh api repos/042Trix/Alice/tags | jq '.[].name'` includes `v<X.Y.Z>`. The audit line exists in `log.md`. The Discord message was delivered (the script's delivery record confirms). The master ticket is closed.
- **Failure mode:** surface the error to operator; do NOT mark the master as done until all 7 steps are verified. If the tag push fails, delete the local tag (`git tag -d v<X.Y.Z>`) and surface; do NOT create a GitHub release until the tag is on remote. If the GitHub release fails after the tag push, surface the error and do NOT advance to audit-line / Discord delivery.

### Field 5 — Skill version (canonical-instance reference)

The canonical-instance implementation of this flow is the `alice-publish` loop at `~/.hermes/loops/hermes.yaml`. The loop and its intent file (`~/.hermes/loops/intents/alice-publish-intent.md`) MUST be amended to reference this doc; the doc is the canonical contract, the loop + intent are the derived instance.

The loop's `worker_prompt` field lists the 7 steps; STEP 0 (version-discipline pre-flight) + STEP 1 (instance-leak gate) + STEP 2 (pre-verify) + STEP 5 (CHANGELOG consolidation) + STEP 6 (per-file version reconciliation + CITATION.cff operator-gate check) are the explicit verifier-gated gates that replace the v0.1.0 implicit "verify before commit" steps. STEP 9 includes the Discord delivery (per Output 6) + the `kanban_complete` master close.

The loop's status field is currently `live-pending-test` (v0.5.1, demoted from `live` on 2026-08-17 per META t_55240868 + op-guard-30 enforcement). Re-promotion to `live` requires the 9-item per-loop audit (`methodology/M-decide-graph-readiness.md` Part 3) to pass — items 1, 4, 8, 9 are the remaining missing items per the v0.5.1 demotion record (`audit_score: 4/9` pre-demotion, `3/9` post-demotion).

### Field 6 — Acceptance criteria (verifier-gated)

The 11 acceptance criteria below are the verifier checks on `kanban_complete`. The verifier runs each check; the ticket closes only when all 11 pass.

1. **Version-discipline gate (STEP 0)** — `check_version_discipline.py` PASSes against the proposed `v<X.Y.Z>`; the computed label matches the operator-supplied one OR the master ticket body carries a `## Operator override (Sequence B)` block with chat citation.
2. **Instance-leak gate (STEP 1)** — `check_instance_leaks.py` exits 0 against `~/Documents/alice-framework/`; no H1-H5 or X1 leaks; the master ticket body carries a `## Verified state (STEP 1)` section with the script's output.
3. **Pre-verify gate (STEP 2)** — the master ticket body contains a `## Verified state` section with `verify_artifact_state.py` output captured at ticket-filing time. The section is non-empty and the timestamp in the captured output is within 24h of the ticket's `created_at`.
4. **Contributing-ticket gates (STEP 3 + STEP 4)** — every contributing ticket has `status=done` + a `verifier_passed` event in the run history. Cross-checked against the `## Contributing tickets` list in the master ticket body.
5. **CHANGELOG gate (STEP 5)** — `CHANGELOG.md` has a single `## [v<X.Y.Z>]` entry (no `[Unreleased]` blocks survive the consolidation). The entry has full source attribution per the alice-framework release-prep discipline.
6. **Per-file version gate (STEP 6, part 1)** — every file's `version:` field is reconciled per the STEP 6 rule; any file not at v<X.Y.Z> has an explicit reconciliation note in the master ticket body.
7. **CITATION.cff gate (STEP 6, part 2)** — `CITATION.cff` version matches the release version OR the master ticket body has explicit operator authorization to re-open the gate.
8. **Commit gate (STEP 7)** — `git status --short` is clean post-commit. The commit message follows the `ALICE v<X.Y.Z>: <one-line summary>` format with body stats + attribution.
9. **Push gate (STEP 8)** — `git log --oneline -1 origin/main` shows the new commit on remote. Verified via `git ls-remote origin main` or `gh api`.
10. **Tag + release + audit-line gate (STEP 9, part 1)** — `git tag -l` includes `v<X.Y.Z>`. `gh api repos/042Trix/Alice/tags | jq '.[].name'` includes `v<X.Y.Z>`. `gh release list | head -1` shows `v<X.Y.Z>` as Latest. Audit line exists in `log.md`.
11. **Discord-delivery gate (STEP 9, part 2)** — the canonical topic-only Discord message (per Output 6) was delivered via `post_alice_release_discord.py`; the script's idempotent delivery record confirms the format + the delivery timestamp. Master ticket is closed with the release URL in `--result`.

A `kanban_complete` call that fails any of the 11 criteria is a verifier-gate failure; the ticket returns to the coder with the failed criteria enumerated in the verifier's `--result`.

## Part 3: Worked example — alice-framework v0.2.0

Below is a worked example of the 6-field flow spec applied to the canonical first publish under this doc: **alice-framework v0.2.0 — the consolidated v0.1.1.1 → v0.1.4 cluster (5 MINOR features + 1 PATCH tooling batched via Path A operator decision).** This example is itself a publish, so it walks through each gate with the expected state for the v0.2.0 ship.

### Goal

Publish alice-framework v0.2.0 (the consolidated v0.1.1.1 → v0.1.4 cluster + the audit-reorg tooling) to GitHub end-to-end. Multi-version cards rejected.

### Inputs

- `version: v0.2.0` in the master ticket body under `## Source`.
- `path: alice-publish` in the master ticket body under `## Source`.
- `## Contributing tickets` — N tickets: `t_d2d0db6a` (master-ticket title shape), `t_42e0c91c` (x-article-review retro-position), `t_7aa96032` (framework-vs-instance distinction), `t_275f1fbf` (06b-recovery promotion), `t_6bbbe1ed` (semver discipline + STEP 0), `t_a7a26ea4` (operator Path A decision), `t_604c0927` (instance-side check_version_discipline.py + 13-case regression suite).
- `## Verified state (STEP 0)` — captured output of `check_version_discipline.py` at ticket-filing time (the Path A override block may be present).
- `## Verified state (STEP 1)` — captured output of `check_instance_leaks.py` at ticket-filing time (exit 0; no H-pattern leaks).
- `## Verified state (STEP 2)` — captured output of `verify_artifact_state.py` at ticket-filing time.
- Operator authorization — `kanban_comment` on the master ticket at operator-authorization time, citing operator's chat timestamp or in-line quote.
- CITATION.cff gate state — at v0.1.4 with operator-gate warning (re-opened by `t_ecb73d81` Path C). Operator authorization to bump to v0.2.0 is recorded in the master ticket body.

### Outputs

- Commit `ALICE v0.2.0: 5 MINOR features + 1 PATCH tooling (audit reorg)` on `main`.
- Tag `v0.2.0` annotated with the same summary.
- GitHub release at `https://github.com/042Trix/Alice/releases/tag/v0.2.0` with the canonical 5-section notes.
- Audit line in `~/Documents/HermesVault/log.md`: `## [2026-08-11T16:50Z] alice-publish — v0.2.0 — result=success — release=https://github.com/042Trix/Alice/releases/tag/v0.2.0`.
- Operator-facing Discord message: `Alice v0.2.0: x-article-review master-title shape, x-article-review retro-position, framework-vs-instance distinction, blocked-ticket recovery procedure, semver discipline, audit-reorg tooling` (6 topic slugs).
- Master ticket `t_b0d6dd79` closed via `kanban_complete` with the release URL.

### Process (7 steps + 6 gates)

The worker runs the 7 steps per Part 2 Field 4. Each gate fires before the worker advances. STEP 0 (version-discipline) accepts the v0.2.0 label via Path A override; STEP 1 (instance-leak) passes (the v0.2.0 cluster reorg-moved the 6 H-classified docs to `~/.hermes/methodology/`); STEP 2 (pre-verify) catches drift before STEP 7's commit; STEP 5 (CHANGELOG consolidation) catches the `[Unreleased]` block drift that the v0.1.2 incident surfaced; STEP 6 (per-file version reconciliation) catches files at v0.4.0 or v0.1.2 that aren't at the v0.2.0 release version; STEP 6 part 2 (CITATION.cff operator-gate check) requires operator authorization to bump the gate.

### Skill version

- Loop: `~/.hermes/loops/hermes.yaml` `alice-publish` v0.5.1 (demoted to `live-pending-test` per op-guard-30 on 2026-08-17; status field is `live-pending-test` until the 9-item audit clears).
- Intent: `~/.hermes/loops/intents/alice-publish-intent.md` v0.5.1 (with `demoted_by_v0_5_1` block + 4 follow-up child tickets on META `t_55240868`).
- Pre-verify: `~/.hermes/tools/verify_artifact_state.py` v0.1.0 (shipped 2026-08-08 per op-guard-19).
- Pre-flight: `~/.hermes/tools/check_version_discipline.py` v0.1.0 (shipped 2026-08-11 per `t_604c0927`).
- Instance-leak: `~/.hermes/tools/check_instance_leaks.py` v0.1.0 (shipped 2026-08-11 per `t_7aa96032`).
- Discord delivery: `~/.hermes/tools/post_alice_release_discord.py` v0.1.0 (shipped 2026-08-09 per `t_a92c1f88`).
- `gh` CLI at v2.x (for STEP 9 tag + release creation).

### Acceptance criteria

All 11 criteria from Part 2 Field 6 apply. The verifier runs each criterion; the master ticket closes only when all 11 pass.

## Part 4: Boundaries

This doc does NOT:

- Modify the alice-publish loop's worker profile assignment (`run_with_profile: coder`). The publish worker is a coder; the doc-writer's role is to maintain this methodology doc + the alice-framework CHANGELOG entry. Per op-guard-16, the doc-writer is spec-only and does not mutate `~/.hermes/loops/` files; the instance amendment ships via a follow-up coder ticket.
- Migrate any other loop to the v0.4.0 / v1.0.0 gate-quality bars. The v0.4.0 amendment is alice-publish-specific; other loops (loop-updater, requirements, stuck-sweep, x-article-review) keep their existing patterns. A future amendment may generalize the gates to other publish-style flows, but that's a separate ticket.
- Re-run `t_ecb73d81` against the new flow spec. The coder is already running on Path C for the v0.1.2 push; the new spec is the durable reference for future publishes (v0.2.0 onward).
- Create the GitHub release automation. STEP 9 still uses `gh release create`; no new tooling is introduced.
- Authorize the CITATION.cff gate re-opening. The CITATION.cff operator gate is a structural protection; only the operator can authorize a gate re-opening, and the authorization is recorded in the master ticket body at publish time, not in this methodology doc.

## Part 5: Maintenance

This methodology doc is the canonical contract for the `alice-publish` flow. Drift in this doc compounds across every Alice release that ships via this flow; the v0.1.2 push incident was the visible failure of v0.1.0 drift.

### Audit cadence

Audit this doc every **90 days** from the date it was last reviewed. The audit walks the 4 checks below; the cadence is a calendar event, not "when I remember."

**When to audit sooner than 90 days:**

- A publish fails on STEP 1 instance-leak gate (the script returns H-pattern hits that the master ticket body missed).
- A publish fails on STEP 0 version-discipline pre-flight (the script refuses the proposed version; the Path A override is missing).
- A publish fails on STEP 2 pre-verify (the script returns a state that doesn't match the master ticket body).
- A publish fails on STEP 5 CHANGELOG consolidation (multiple `[Unreleased]` blocks survive into STEP 7).
- A publish fails on STEP 6 per-file version reconciliation (an unreconciled file ships with the release).
- A publish fails on STEP 6 CITATION.cff operator-gate check (the gate is bumped without operator authorization).
- A new tool, agent, or routing convention changes which gates the dispatcher can satisfy.
- A council review or retro names this doc as a contributing cause of a publish failure.

An audit-on-failure is **added**, not substituted — the 90-day cadence still runs for this doc.

### Quality threshold

This doc passes the maintenance check when **all 6 fields are present, the goal field is one sentence, the 6 NEW gates (STEP 0 / STEP 1 / STEP 2 / STEP 5 / STEP 6 part 1 / STEP 6 part 2) are documented as load-bearing steps, AND the Part 7 node-type discipline section names every graph node + its type + rationale**. The threshold is binary per field; the doc is acceptable when every field passes its own check.

**Per-field pass conditions:**

| Field | Pass condition |
|---|---|
| Goal | Exactly one sentence. Names the artifact (one Alice release published to GitHub). No embedded success criteria. |
| Inputs | At least one item. Each item names the source (path, ticket, or external). `version` / `path` / `## Verified state` / operator authorization / CITATION.cff gate state are present. No unbounded inputs. |
| Outputs | At least one item. Each item names the destination (commit hash, tag, release URL, audit-line path, master ticket transition). No activity-only outputs. |
| Process (7 steps + 6 gates) | 7 numbered steps; the 6 NEW gates (STEP 0 version-discipline, STEP 1 instance-leak, STEP 2 pre-verify, STEP 5 CHANGELOG consolidation, STEP 6 part 1 per-file version reconciliation, STEP 6 part 2 CITATION.cff operator-gate check) are present and load-bearing. Each step has a gate and a failure mode. |
| Skill version | Names the loop version, intent version, pre-verify tool, pre-flight tool, instance-leak tool, Discord-delivery tool, `gh` CLI version. |
| Acceptance criteria | At least 11 criteria. Each criterion is a yes/no check or a numeric threshold. Covers version-discipline, instance-leak, pre-verify, contributing-ticket gates, CHANGELOG gate, per-file version gate, CITATION.cff gate, commit gate, push gate, tag + release + audit-line gate, Discord-delivery gate. |
| Part 7 — Node-type discipline | Names every graph node + its type (Human / Scripts/Code / Agents per `methodology/04a-decide-work-graph.md` Part 4) + the rationale for each type choice. The audit item-9 regex (`(node[- ]type\|node type\|Scripts/Code\|Agents?\s+only\|Human[ -]owned\|deterministic[- ]not[- ]LLM)`) must match at least once in the doc. |

**Acceptance:** this doc, the `alice-publish` loop at v0.5.1 (or whatever the current loop version is at audit time), and the `alice-publish` intent at v0.5.1 (or current) form the canonical publish flow contract; the loop and intent reference this doc; the verifier checks the 11 acceptance criteria on every publish.

### Drift signals

Drift is the difference between this doc and the loop/intent it specifies. The audit catches drift on schedule; the signals catch drift between audits.

**Strong signals (audit immediately):**

- The `alice-publish` loop's `version:` field drifts from this doc's reference (e.g., a coder patches the loop without updating the doc).
- A publish ships with H-pattern instance leaks surviving STEP 1 (the gate fired but didn't enforce).
- A publish ships with multiple `[Unreleased]` blocks surviving STEP 5 (the gate fired but didn't enforce).
- A publish ships with an unreconciled file at v<X.Y.Z> per the STEP 6 rule.
- The CITATION.cff gate is bumped without operator authorization (the STEP 6 part-2 check fired but didn't enforce).
- The alice-framework v0.1.2-style incident recurs (a push with claim-vs-reality gaps).

**Weak signals (audit at next scheduled review):**

- A field has been copy-pasted between flows without re-checking (e.g., the v0.1.0 alice-publish loop's worker_prompt is patched to add a step without this doc being amended).
- A new gate is added to the loop without this doc being amended.

**How signals are detected:**

- Strong signals: surfaced by the dispatcher's per-tick reject log, by `kanban_block` reasons that name a failed gate, and by a one-shot audit script that scans the `alice-publish` loop's `worker_prompt` for steps not documented in this doc.
- Weak signals: surfaced by the audit-cadence calendar event and by the operator's manual review.

When a strong signal fires, **retro the failed publish first**, then audit this doc. The retro names which gate fired and how the failure cascaded; the audit decides whether the doc is structurally wrong or the loop drifted from the doc.

### Fix actions

When drift is detected, the corrective action is named in the audit record. The action is small, scoped, and reversible.

| Verdict | Action |
|---|---|
| All checks pass | Record audit date and next audit date (+90 days). No other action. |
| Goal is too long | Split the doc. Re-write the goal as one sentence and move the rest to the Process or Acceptance criteria. |
| A field is empty | Fill the field. If the field cannot be filled (e.g., a future gate is unknown), the doc is not ready to ship. |
| Process missing a step | Add the missing step. The 7-step + 6-gate structure is the canonical shape; deviations require a doc amendment. |
| Acceptance criteria short | Add the missing criterion. The 11 criteria are the verifier's check list; missing criteria let the verifier pass an under-gated publish. |
| Node-type discipline (Part 7) missing or wrong | Add or fix Part 7. The audit item-9 regex must match; every graph node must be named with its type + rationale. |
| Loop and doc drift | Re-align the loop. The doc is canonical; the loop and intent must reference the doc, not the other way around. |

### Retirement conditions

The doc retires when the `alice-publish` loop is replaced by a different mechanism (e.g., a fully automated GitHub Actions release pipeline that bypasses the dispatcher entirely). Until then, this doc is the canonical reference.

**Conditions that warrant a retirement proposal:**

- A fully-automated release pipeline ships to GitHub Actions and the dispatcher-based `alice-publish` loop is decommissioned.
- The publish flow ships via a different code path (e.g., a direct `gh release create` from the operator's chat) and the loop is unused for 90+ days.
- A council review concludes the doc is over-engineered for the operator's typical publish cadence (releases <1/month).

**Retirement is not deletion.** A retirement proposal is a ticket that:

1. Names this doc as the doc being retired.
2. Cites the evidence — the audit records, the usage decline, the council verdict.
3. Proposes the replacement — the new automation, the simplified flow, or no flow at all (operator-pushes-manually).
4. Names the migration path — how future publishes transition to the replacement.

**The default is to fix, not retire.** Drift in the doc is usually a symptom of an under-specified instance (the loop drifted from the doc). Most maintenance passes end with a verdict of `revise`, not `retire`.

## Part 6: Cross-references

- **Source:** operator 2026-08-08 ("Do we have a flow written for pushing latest changes to GitHub? We should make one so we know it's thorough and consistent.")
- **Source:** `t_ecb73d81` (alice-framework v0.1.2 push incident; 4-gap pre-block audit that motivated this doc).
- **Source:** `t_25edf8f7` (audit-reorg ticket filed as v0.1.7 for PATCH-level work; the v0.1.4 → v0.1.7 skip-pattern that motivated STEP 0 version-discipline pre-flight).
- **Source:** `t_7aa96032` (framework-vs-instance distinction; the audit-reorg that motivated STEP 1 instance-leak gate).
- **Source:** `t_a92c1f88` (Retro-H body observation #3; the operator-facing Discord release message topic-only format).
- **Source:** `t_55240868` (alice-publish demotion per op-guard-30 enforcement; this v1.0.0 promote is item 1 of the per-loop audit's missing-items remediation).
- **Source:** `t_f38bd852` (this doc's canonical-promote ticket; doc-writer; the doc lands via this ticket).
- **Companion:** `op-guard-19-pre-verify-artifact-state-2026-08-08.md` — the pre-verify rule that drives STEP 2.
- **Companion:** `op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md` — alice-first / instance-second / compliance-gate. This doc is the Alice-canonical side; the loop + intent amendments are the instance side.
- **Companion:** `op-guard-11-log-md-append-only-2026-07-22.md` — vault log.md append-only rule. STEP 9's audit-line append uses `vault_log.append_audit_line`.
- **Companion:** `op-guard-10-no-close-on-age-2026-07-27.md` — STEP 3 + STEP 4 verify work + verifier per the closure-evidence rule.
- **Companion:** `op-guard-16-spec-first-flow-2026-08-05.md` — doc-writer is spec-only. This doc IS the doc; the env-mutation follow-ups ship via coder per op-guard-17.
- **Companion:** `op-guard-30-promotion-requires-evidence-or-exemptions-2026-08-17.md` — the rule that demoted `alice-publish` from `live` to `live-pending-test` on 2026-08-17. Re-promotion to `live` requires the 9-item per-loop audit to pass (this doc satisfies items 1 + 9).
- **Companion:** `methodology/04d-decide-flow-spec.md` — the canonical 6-field flow spec this doc adapts.
- **Companion:** `methodology/04a-decide-work-graph.md` Part 4 — the 3-node-type taxonomy (Human / Scripts/Code / Agents) that grounds Part 7 of this doc.
- **Companion:** `methodology/M-decide-graph-readiness.md` Part 3 — the 9-item per-loop audit that this doc satisfies for items 1 + 9. The audit's source-of-truth is `~/.hermes/tools/loop_live_audit.py`.
- **Companion:** `methodology/M-decide-instance-vs-framework.md` v0.1.0 — the framework-vs-instance distinction that motivated STEP 1.
- **Companion:** `methodology/M-decide-versioning-discipline.md` Parts 4, 8.1, 10 — the semver rule that STEP 0 enforces.
- **Companion:** `methodology/04c-decide-master-ticket.md` — the master-ticket pattern. The alice-publish master is a master ticket; Part 8 Master Title Discipline + Part 7.5 done-gate opt-in both apply.
- **Companion:** `methodology/M-decide-x-article-review-flow.md` — sibling flow that also uses a master-ticket + done-gate + verifier pattern. The x-article-review flow is operator-facing (uses Retro-A + Retro-H); the alice-publish flow is git-facing (uses the 7-step publish pipeline + GitHub release). The two flows share the master-ticket substrate.
- **Companion reference:** `references/instance-leak-check-spec.md` — the H1-H5 + X1 pattern catalog that STEP 1 enforces.
- **Companion template:** `templates/check_version_discipline.py.template` — the tool-agnostic template form of the STEP 0 script.
- **Companion template:** `templates/instance-leak-check.py.template` — the tool-agnostic template form of the STEP 1 script.
- **Instance:** `~/.hermes/loops/hermes.yaml` `alice-publish` loop v0.5.1 (demoted to `live-pending-test` on 2026-08-17 per META t_55240868).
- **Instance:** `~/.hermes/loops/intents/alice-publish-intent.md` v0.5.1 (with `demoted_by_v0_5_1` block + 4 follow-up child tickets).
- **Tool:** `~/.hermes/tools/verify_artifact_state.py` v0.1.0 (shipped 2026-08-08 per op-guard-19; the STEP 2 pre-verify gate tooling).
- **Tool:** `~/.hermes/tools/check_version_discipline.py` v0.1.0 (shipped 2026-08-11 per `t_604c0927`; the STEP 0 version-discipline pre-flight tooling).
- **Tool:** `~/.hermes/tools/check_instance_leaks.py` v0.1.0 (shipped 2026-08-11 per `t_7aa96032`; the STEP 1 instance-leak gate tooling).
- **Tool:** `~/.hermes/tools/loops_preflight.py` (the STEP 7 commit + STEP 8 push verification).
- **Tool:** `~/.hermes/tools/post_alice_release_discord.py` v0.1.0 (shipped 2026-08-09 per `t_a92c1f88`; the STEP 9 Discord-delivery tooling per Output 6).
- **Tool:** `~/.hermes/scripts/vault_log.py` (the STEP 9 audit-line append helper, per op-guard-11).

## Part 7: Node-type discipline (per `methodology/04a-decide-work-graph.md` Part 4)

This part is the per-loop-audit item 9 (`methodology/M-decide-graph-readiness.md` Part 3 item 9 + `~/.hermes/tools/loop_live_audit.py::check_item_9` regex `(node[- ]type|node type|Scripts/Code|Agents?\s+only|Human[ -]owned|deterministic[- ]not[- ]LLM)`). Every node in the `alice-publish` graph has exactly one type per `methodology/04a-decide-work-graph.md` Part 4 (Human / Scripts/Code / Agents — LLM-driven Agents for interpretation, Scripts/Code for determinism, Human for operator-owned decisions). The type determines what the node can do, what it can receive as input, and what it can produce as output.

### Graph nodes

| # | Node | Type | Rationale |
|---|------|------|-----------|
| 1 | **Operator files the master ticket** on the alice-framework board with `path: alice-publish` + `version: v<X.Y.Z>` in body | **Human** | The operator owns the publish decision (which version, when to ship). This is the human-owned decision that gates the entire publish flow. No LLM or script can decide "publish now" on the operator's behalf; the operator authorizes. |
| 2 | **Dispatcher routes the master ticket** to the `alice-publish` loop via the `path:` field | **Scripts/Code** | The dispatcher is a deterministic registry lookup (`path: alice-publish` → `id: alice-publish` in `~/.hermes/loops/hermes.yaml`). Same input → same output. No interpretation needed. |
| 3 | **Coder worker spawns** via `run_with_profile: coder` and reads the master ticket body | **Agents** | The coder interprets the master ticket body (the `## Verified state` + `## Contributing tickets` + `## CITATION.cff gate state` blocks), decides how to apply the worker's discipline to this specific release, and produces the structured commit message + release notes. The interpretation is LLM-driven; the same body might produce different release-note phrasing across runs. |
| 4 | **STEP 0 — `check_version_discipline.py`** runs against the proposed `v<X.Y.Z>` | **Scripts/Code** | The script is a deterministic parser of `git log <last-tag>..HEAD` with Conventional Commits prefixes + the proposed version. Same inputs → same computed label. No interpretation. |
| 5 | **STEP 1 — `check_instance_leaks.py`** runs against `~/Documents/alice-framework/` | **Scripts/Code** | The script is a deterministic Frontmatter parser + body-content classifier (H1-H5 + X1 patterns per `references/instance-leak-check-spec.md`). Same repo state → same classification. No interpretation. |
| 6 | **STEP 2 — `verify_artifact_state.py`** runs and captures `git status --short`, `git tag -l`, etc. | **Scripts/Code** | The script is a deterministic git-state capturer + Frontmatter scanner + CITATION.cff parser. No interpretation; the output is the on-disk state verbatim. |
| 7 | **STEP 3 — Work-completion gate** (per contributing ticket, check `status=done` in kanban DB) | **Scripts/Code** | Deterministic DB query per contributing ticket id. Same ticket id → same status. |
| 8 | **STEP 4 — Verifier-ran gate** (per contributing ticket, check `verifier_passed` event in run history) | **Scripts/Code** | Deterministic kanban DB query per contributing ticket id. Same ticket id → same event. |
| 9 | **STEP 5 — CHANGELOG consolidation** (read CHANGELOG.md, verify single `## [v<X.Y.Z>]` entry) | **Scripts/Code** | The script is a deterministic markdown parser (a single regex check for the entry heading + a count of `[Unreleased]` blocks). Same markdown → same classification. |
| 10 | **STEP 6 part 1 — Per-file version reconciliation** (for each `methodology/*.md` + `templates/*.md.template` with `version:` field, check reconciliation) | **Scripts/Code** | The script is a deterministic Frontmatter scanner. Same files → same version fields. |
| 11 | **STEP 6 part 2 — CITATION.cff operator-gate check** (read CITATION.cff, check for operator-gate warnings) | **Scripts/Code** + **Human** authorization | The script is a deterministic parser (read CITATION.cff, grep for `do not edit` / `operator gate` / `re-open`). The Human-typed operator authorization is recorded in `## CITATION.cff gate state` of the master ticket body. The script cannot bypass the operator authorization — the operator owns the gate re-opening decision. |
| 12 | **STEP 7 — `git add -A` + `git commit -m 'ALICE v<X.Y.Z>: ...'`** | **Scripts/Code** | Git operations are deterministic. Same diff → same commit SHA. The commit message is templated by the coder (Agents-typed, per node #3), but the commit operation itself is deterministic. |
| 13 | **STEP 8 — `git push origin main`** | **Scripts/Code** | Git push is deterministic. Same local commit → same remote SHA after push. |
| 14 | **STEP 9 — `git tag -a v<X.Y.Z> -m '...'` + `git push origin v<X.Y.Z>` + `gh release create ...`** | **Scripts/Code** + **Agents** (release notes) | The tag + push are deterministic. The release notes (the body passed to `gh release create --notes`) is Agents-typed (the coder produces them from the CHANGELOG entry per Output 6 — same Agents node as #3). The `gh release create` invocation is Scripts/Code. |
| 15 | **STEP 9 audit-line append** via `vault_log.append_audit_line` | **Scripts/Code** | The helper is a deterministic append-only helper per op-guard-11 (POSIX `O_APPEND` + an advisory `fcntl.flock`). Same input → same audit-line row. |
| 16 | **STEP 9 Discord delivery** via `post_alice_release_discord.py` | **Scripts/Code** | The script reads CHANGELOG.md (deterministic parse), extracts the topic list (deterministic `_extract_topics` regex over `### Added` / `### Changed` / `### Fixed` / `### Out of scope` bullets), and posts the canonical topic-only message via `send_dm_attachments`. The topic extraction is deterministic; the operator-facing message format is enforced by the script. No interpretation. |
| 17 | **`kanban_complete` master close** with structured handoff | **Scripts/Code** | The close transition is a deterministic DB write. Same `--result` + `--summary` + `--metadata` → same DB row. |
| 18 | **Compliance-verifier child ticket** confirms instance conforms to this doc (per op-guard-17) | **Agents** (verifier profile) | The verifier interprets the instance state (loop yaml + intent doc + test suite) against this doc's contract and produces a `compliance: pass | fail` verdict. The interpretation is LLM-driven; same inputs might produce different verifier verdict phrasings, but the verdict field is structured. |

### Node-type discipline summary

The `alice-publish` graph is **predominantly Scripts/Code** — 13 of 18 nodes are deterministic (git operations, script invocations, deterministic parsers). The Agents-typed nodes are the coder's interpretation (nodes 3, 14 partial, 18 — release-notes phrasing, commit-message phrasing, verifier-verdict phrasing). The Human-typed node is the operator's authorization (node 1 — master-ticket filing + node 11 partial — CITATION.cff gate re-opening). No Agents node does work a Scripts/Code node could do deterministically; no Scripts/Code node owns a decision the operator or coder interpretation must make. The graph respects the node-type discipline per `methodology/04a-decide-work-graph.md` Part 4.

The per-loop audit's item 9 (`methodology/M-decide-graph-readiness.md` Part 3) requires this part to name every graph node + its type + the rationale; this section satisfies the audit.

## Part 8: Verification checklist

Before marking any alice-publish loop iteration complete, verify:

- [ ] `methodology/M-decide-alice-publish-flow.md` exists (this doc) with the 6-field spec + 7-step process + 6 gates + 11 acceptance criteria + Part 7 node-type discipline.
- [ ] `~/.hermes/loops/hermes.yaml` `alice-publish` loop version is at the current contract version; `worker_prompt` references this doc.
- [ ] `~/.hermes/loops/intents/alice-publish-intent.md` version is at the current contract version; `amended_by` entry references this doc.
- [ ] `python3 ~/.hermes/tools/loops_preflight.py ~/.hermes/loops/hermes.yaml` exits 0.
- [ ] `python3 -c "import yaml;yaml.safe_load(open('~/.hermes/loops/hermes.yaml'))"` exits 0.
- [ ] `python3 ~/.hermes/tools/check_instance_leaks.py ~/Documents/alice-framework` exits 0 (STEP 1 gate).
- [ ] `python3 ~/.hermes/tools/check_version_discipline.py --proposed-version <X.Y.Z> --last-release <last-tag> --working-tree-changes <diff-stat>` PASSes (STEP 0 gate).
- [ ] `python3 ~/.hermes/tools/verify_artifact_state.py ~/Documents/alice-framework --ticket-id T_xxx` output captured at ticket-filing time, pasted into the master ticket body under `## Verified state (STEP 2)`.
- [ ] `CHANGELOG.md` `## [v<X.Y.Z>]` entry exists (single entry; no `[Unreleased]` blocks survive per STEP 5).
- [ ] Per-file version fields reconciled per STEP 6 part 1 (this doc at the current contract version; loop + intent at the current contract version; sister docs at their existing versions with reconciliation notes if non-matching).
- [ ] CITATION.cff operator gate explicitly authorized for the v<X.Y.Z> bump (in the master ticket body under `## CITATION.cff gate state`).
- [ ] Audit line appended to `~/Documents/HermesVault/log.md` via `vault_log.append_audit_line` (the doc-writer ships the audit-line for the doc-side; the publisher worker ships the audit-line for the publish-side).
- [ ] Operator-facing Discord release message delivered via `post_alice_release_discord.py` (topic-only format per Output 6; idempotent delivery record confirms).
- [ ] Per-loop audit (`python3 ~/.hermes/tools/loop_live_audit.py --loop-id alice-publish`) shows item 1 ✅ AND item 9 ✅ (this doc satisfies both items).

## Anti-patterns to watch for

1. **"The flow is just a goal."** → No. Six fields. Every one is required. A flow with only a goal is a flow with five missing fields; the worker guesses on inputs, outputs, process, skill version, and acceptance criteria.
2. **"The 6 NEW gates are optional."** → No. The 6 NEW gates (STEP 0 version-discipline + STEP 1 instance-leak + STEP 2 pre-verify + STEP 5 CHANGELOG consolidation + STEP 6 part 1 per-file version reconciliation + STEP 6 part 2 CITATION.cff operator-gate check) close the drift surface from the v0.1.2 incident, the v0.1.4 → v0.1.7 skip-pattern, and the framework-vs-instance leak surface. Skipping any gate re-opens one of these failure modes.
3. **"The doc and the loop are independent."** → No. The doc is canonical; the loop is derived. The loop's worker_prompt must reference the doc; the loop cannot drift from the doc without an audit-on-failure signal firing.
4. **"I'll add a step to the loop without amending the doc."** → No. The 7 steps are the doc's contract. The loop's worker_prompt is the doc's instance; the two must agree. Adding a step to the loop without amending the doc is a doc-writer violation per op-guard-16.
5. **"The CITATION.cff gate is just a comment in the file."** → No. The gate is a structural protection. The first gate was opened by `t_4054ccac` + parent `t_968068b0`; the second gate was re-opened by `t_ecb73d81` Path C. Any further re-opening is an operator-authorized action recorded in the master ticket body, not a routine patch.
6. **"The acceptance criteria are the same as the outputs."** → Outputs are the artifacts the flow produces; acceptance criteria are the conditions the verifier checks. A flow that produces a GitHub release (output) is not automatically acceptable; the criteria say what makes the release acceptable (e.g., the verifier confirms `gh release list` shows it as Latest, the audit line exists, the Discord message was delivered, the master is closed).
7. **"The release Discord message names the article author."** (new in v0.3.0, t_a92c1f88) → No. The canonical release-message format is `Alice v<X.Y.Z>: <topic-1>, <topic-2>, ...` — topic-only. The article author/URL/handle stays in the master ticket body's `## Source` block + the CHANGELOG `## Source` sub-section + the GitHub release notes `## Source` section. Attributing the release to the article author misattributes the operator's + council's + Alice team's work to a single data point in the methodology's research. Source: operator 2026-08-09 Retro-H body observation #3.
8. **"I'll add a node to the graph without naming it in Part 7."** → No. Part 7 (node-type discipline per `methodology/04a-decide-work-graph.md` Part 4 + `methodology/M-decide-graph-readiness.md` Part 3 item 9) names every graph node + its type + rationale. Adding a node without amending Part 7 is a doc-writer violation per op-guard-16 + a per-loop-audit item-9 violation.

## Changelog

- **1.0.0** (2026-08-17, META t_55240868 → child t_f38bd852): **canonical-promote** — this doc is promoted from the instance-side path (`~/.hermes/methodology/M-decide-alice-publish-flow.md`) to its canonical alice-framework home (`methodology/M-decide-alice-publish-flow.md`). The instance-side copy is retained as a 1-line pointer to this canonical path. The version bumps `0.4.0 → 1.0.0` because the alice-framework-side is now the canonical surface, not a downstream copy. `links:` field swaps `~/.hermes/...` paths for canonical `methodology/...` paths. **STEP 1 instance-leak gate added** (closes the framework-vs-instance leak surface from `t_7aa96032`; runs `check_instance_leaks.py` as a hard gate before STEP 2 pre-verify). Process now 7 steps + 6 gates; Field 6 acceptance criteria now 11 items (STEP 1 instance-leak + STEP 9 Discord-delivery gate); Part 7 (node-type discipline) added to satisfy per-loop-audit item 9 (every graph node named with its type + rationale per `methodology/04a-decide-work-graph.md` Part 4); Quality-threshold + Drift-signals + Anti-patterns updated to match. Title updated to "7-step pipeline + 6 gates + topic-only release message". `companion:` field added with the 10 cross-reference paths. Source: META t_55240868 (alice-publish demotion per op-guard-30) + t_f38bd852 (this doc's canonical-promote ticket). Per op-guard-5 paired-wiki integrity, the methodology + the CHANGELOG v1.0.0 entry ship in the same change set; the instance-side pointer + the alice-publish loop yaml comment block + the intent-doc `amended_by_v0_2_0_methodology_doc` field are flagged as follow-up coder tickets (out of scope per op-guard-16 + op-guard-17 alice-first/instance-second).
- **0.4.0** (2026-08-11, t_4d9bd6c5): Added STEP 0 — the version-discipline pre-flight (`check_version_discipline.py`) — closing the v0.1.4 → v0.1.7 skip-pattern from `t_25edf8f7` (audit-reorg ticket filed as v0.1.7 for PATCH-level work; operator-corrected 2026-08-11). STEP 0 fires BEFORE STEP 1 (pre-verify); refuses the proposed `v<X.Y.Z>` if it (a) skips the last tag, (b) doesn't match the Conventional Commits-derived change type, or (c) is a MAJOR bump without `[HUMAN ACTION]` operator authorization. Operator override path documented (Sequence B). Process now 7 steps + 6 gates (the "6 gates" includes STEP 0 + the v0.2.0 5 gates); Field 6 acceptance criteria now 10 items; Quality-threshold + Drift-signals + Anti-patterns updated to match. Title bumped to "v0.4.0 with STEP 0 version-discipline pre-flight + 6 gates". `links` field adds `[[~/.hermes/methodology/M-decide-alice-versioning.md]]` (the canonical rule reference for the STEP 0 gate contract). Companion instance changes (per op-guard-16 + op-guard-17 alice-first/instance-second): `~/.hermes/tools/check_version_discipline.py` ships via the coder child ticket `t_604c0927` (B; pre-flight script + 6-case regression suite); the alice-publish loop `worker_prompt` MUST be amended to call STEP 0 before STEP 1 in a follow-up coder ticket. Per op-guard-5 paired-wiki integrity, the methodology + the alice-framework `methodology/00-decide-ticket-naming.md` v0.4.2 (link corrected to instance-side path + `M-decide-instance-vs-framework.md` link added) ship in the same change set. Per op-guard-17 compliance-gate, a verifier child confirms the loop/intent match the doc's STEP 0 contract on first publish under v0.4.0. Source: operator 2026-08-11 corrections ("I don't want to make a 1-off decision. The doc writer should define our rules for semver based on open source repo best practices" + "M-decide-alice-versioning.md being stored as a local implementation and not a part of the Alice framework, correct?" + the Semver.org §10 versions-only-go-up correction that drives v0.1.5 → v0.2.0 strict-semver Path A override).
- **0.3.0** (2026-08-09, t_a92c1f88): Added Output 6 (operator-facing Discord release message) to Field 3. Canonical format: `Alice v<X.Y.Z>: <topic-1>, <topic-2>, ...` (topic-only; no article author/URL/handle). Empty topic list → `Alice v<X.Y.Z>: documentation-only`. New anti-pattern #7 codifies the rule. STEP 9 (then "STEP 10") gained the `post_alice_release_discord.py` invocation after `gh release create`. Companion instance changes (per op-guard-16 + op-guard-17 alice-first/instance-second): `~/.hermes/loops/intents/alice-publish-intent.md` v0.3.0 (intent ships the rule in STEP 9 worker_prompt), `~/.hermes/loops/hermes.yaml` `alice-publish` v0.3.0 (loop yaml ships in a coder child ticket), `~/.hermes/tools/post_alice_release_discord.py` (new; reads CHANGELOG entry, extracts topic list, posts via send_dm_attachments, idempotent on (version, release_url)), `~/.hermes/tests/test_post_alice_release_discord.py` (new; 4-case regression suite). Per op-guard-5 paired-wiki integrity, the methodology + intent + loop-yaml + script + test ship in one change set. Per op-guard-17 compliance-gate, a verifier child confirms the instance matches the methodology rule on first publish under v0.3.0. The canonical rule lives at the x-article-review methodology level (`methodology/M-decide-x-article-review-flow.md` v0.4.0 Part 3 `## Release-message discipline`); this doc is the publisher of the format. Source: operator 2026-08-09 Retro-H body observation #3 (verbatim: *"The message on the push should mention what topic was changed but not the article author, the article was just the inspiration for the change."*).
- **0.2.0** (2026-08-08, t_fb90b479; methodology t_fdba17e5): initial canonical publish-flow spec with pre-verify gate + CHANGELOG consolidation + per-file version reconciliation + CITATION.cff operator-gate check + per-step verifier continuation (5 NEW gates total). The alice-publish loop v0.2.0 in `~/.hermes/loops/hermes.yaml` references this doc; the loop's `status` field is `live`. Paired with `~/.hermes/loops/intents/alice-publish-intent.md` v0.2.0 (intent v0.1.0 → v0.2.0 ships the rule in the intent worker_prompt). Per op-guard-17 alice-first/instance-second, this doc is the alice-first; the loop + intent amendments are the instance children.

## Audit-line

```
## [2026-08-23T15:40Z] doc-writer-amend — methodology/M-decide-alice-publish-flow.md v1.0.1 — wire --allow-deliberate-fixtures into STEP 1 dryrun variant (t_3203ac1f). Adds a one-paragraph note in STEP 1 'Run' section explaining the dryrun/operator-run variance: dryrun (synthetic release pipelines with version string suffix `-dryrun`, e.g. v0.5.1-dryrun per t_ac12ffb3) passes `--allow-deliberate-fixtures`; operator-run stays strict (no flag, exit 1 on any H1). The 2 on-disk H1 deliberate fixtures (M-decide-h1-leak.md + M-decide-instance-leak-test.md) remain at `methodology/` per `references/instance-leak-check-spec.md` Part 2 line 139 + `CHANGELOG.md` line 120. Per op-guard-16 (spec-first flow) the doc-writer's canonical concept note (2-ATOMIC/concepts/h1-deliberate-fixtures-canonical-non-move-2026-08-23.md, t_97f8a7c1) preceded this amendment. Per op-guard-17 (alice-first / instance-second / compliance-gate) the instance-side wire-up (hermes.yaml v0.5.2, intent doc v0.5.2) follows the methodology amendment; the compliance-verifier child ticket will fire on the instance side. Regression: tests/test_check_instance_leaks.py +2 cases. amended_by_v1_0_1: t_3203ac1f. source: t_3203ac1f.

## [2026-08-17T10:35Z] doc-writer-ship — methodology/M-decide-alice-publish-flow.md v1.0.0 — canonical-promote of the alice-publish flow spec from instance-side to alice-framework-side. Adds STEP 1 instance-leak gate (closes the framework-vs-instance leak surface from t_7aa96032), expands Process to 7 steps + 6 gates, Field 6 acceptance criteria to 11 items, and adds Part 7 node-type discipline section (every graph node named with type + rationale per methodology/04a-decide-work-graph.md Part 4; satisfies per-loop-audit item 9). Per-loop audit item 1 flips to ✅ (canonical methodology doc exists at ~/Documents/alice-framework/methodology/M-decide-alice-publish-flow.md with loop-id cross-reference) and item 9 flips to ✅ (node-type discipline phrase present). Source: META t_55240868 (alice-publish demotion per op-guard-30 enforcement) + child t_f38bd852 (this doc's canonical-promote ticket). Follow-ups flagged: instance-side pointer update + alice-publish loop yaml comment block + intent-doc amended_by_v0_2_0_methodology_doc field (out of scope per op-guard-16 + op-guard-17 alice-first/instance-second; ship via coder follow-up).
```