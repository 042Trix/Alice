---
id: alice-methodology-decide-alice-publish-flow
created: 2026-08-08T20:30:00Z
updated: 2026-08-09T20:30:00Z
title: "Methodology — Decide the alice-publish flow (the canonical publish pipeline v0.3.0 with pre-verify + 5 gates + topic-only release message)"
type: methodology
status: draft
source: alice-framework
version: 0.3.0
tags: [kind:methodology, kind:flow-spec, kind:publish, kind:git, kind:gate, kind:pre-verify, kind:release, project:alice]
confidence: 0.95
links: ["[[methodology/04d-decide-flow-spec.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[methodology/04c-decide-master-ticket.md]]", "[[methodology/00-decide-ticket-naming.md]]", "[[methodology/M-decide-x-article-review-flow.md]]", "[[op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md]]", "[[op-guard-19-pre-verify-artifact-state-2026-08-08.md]]", "[[op-guard-16-spec-first-flow-2026-08-05.md]]", "[[~/.hermes/loops/intents/alice-publish-intent.md]]"]
---

# Methodology — Decide the alice-publish flow (the canonical publish pipeline v0.3.0)

> The alice-publish flow is the canonical pipeline for shipping Alice releases to GitHub. v0.2.0 adds the pre-verify gate (op-guard-19), the CHANGELOG consolidation gate, the per-file version reconciliation gate, and the CITATION.cff operator-gate check — five gates total that close the drift surface surfaced by the alice-framework v0.1.2 push incident (`t_ecb73d81`, 2026-08-08).

## Source incident: `t_ecb73d81` alice-framework v0.1.2 push (2026-08-08) — the 4-gap pre-block audit that motivated this doc

The v0.1.2 push ticket was filed on 2026-08-08 with claim-vs-reality gaps:

| Claim in ticket body | Actual on-disk state |
|---|---|
| "CHANGELOG.md v0.1.2 entry" | Four separate `[Unreleased]` sub-sections; no `v0.1.2` entry yet (consolidated in the same ticket before commit) |
| "CITATION.cff at v0.1.1" | At `v0.1.0` with explicit operator-gate warning |
| "Per-file versions aligned" | `00` at `v0.4.0`, `03b` had no version field, `06a` at `v0.1.3` |
| "tag v0.1.2 will be created" | No `v0.1.2` tag existed; coder pre-block audit identified all 4 gaps |

Coder pre-block audit correctly blocked the push (Path C: full release with re-opened CITATION.cff operator gate). The cost of recovering from drift (4-gap pre-block audit + operator re-authorization + manual CHANGELOG consolidation) is more expensive than the upfront gate. v0.2.0 of this flow spec ships those gates as load-bearing steps. The codership cost of recovering from drift is the documented rationale; the v0.1.0 alice-publish loop had the 10-step pipeline but lacked the gate-quality bars the v0.1.2 incident surfaced.

## Part 1: Purpose

The alice-publish flow takes one Alice release version (vX.Y.Z) from "contributing tickets complete on disk" to "GitHub release published + audit line appended + master ticket closed." The flow is the operator's contract for pushing to GitHub: it names the gates, the inputs, the outputs, and the verifier that confirms each step landed.

Without this doc, the alice-publish loop at `~/.hermes/loops/hermes.yaml` is a 10-step pipeline with no gate-quality bars — a worker can run the steps, the verifier can check the steps, but no upstream check exists for "is the work in a state where these steps will succeed?" The v0.1.2 push incident was the failure case: the steps ran in the right order, but the inputs were inconsistent and a pre-flight check would have caught it.

This doc is the canonical reference for the alice-publish flow. The instance side is the `alice-publish` loop v0.2.0 at `~/.hermes/loops/hermes.yaml` (amended from v0.1.0 to reference this doc). The loop and the intent file are derived from this doc; this doc is the contract.

## Part 2: The flow spec (adapted from `methodology/04d-decide-flow-spec.md`)

The canonical 6-field flow spec in `04d-decide-flow-spec.md` is **Goal / Inputs / Outputs / Success Criteria / Retry Parameters / Escalation Process**. The publish flow adapts the pattern to its deterministic git/gh nature: the publish flow's "retry" and "escalation" are uniform across all steps (transient → retry, hard-failure → operator-blocked), so the spec uses **Process (10 steps + 5 gates)** as the structural field and **Skill version** as the canonical-instance reference (instead of the more free-form retry/escalation fields). The verifier-gated acceptance criteria substitute for the canonical "Success Criteria" field with a sharper "the verifier checks these N conditions" framing.

### Field 1 — Goal

Publish one Alice release version (vX.Y.Z) to GitHub end-to-end with verifier-gated gates at every step. **Multi-version cards rejected** (one release per invocation; one version per master ticket).

### Field 2 — Inputs

- **`version: v<X.Y.Z>`** — required in the master ticket body. The version MUST match the `git tag -l` invariant (no such tag exists pre-publish; tag created during the ship at STEP 9).
- **`path: alice-publish`** — required in the master ticket body. Routes the dispatcher to this loop. Per `methodology/04c-decide-master-ticket.md` Master Title Discipline, the title carries the descriptive topic; the URL/version goes in `## Source` block.
- **`## Contributing tickets`** — every doc-writer + verifier ticket that contributed to this release. Lives in the master ticket body under that heading. The list is the per-step verification scope (STEP 2 + STEP 3 check each ticket).
- **`## Verified state`** — the captured output of `python3 ~/.hermes/tools/verify_artifact_state.py ~/Documents/alice-framework --ticket-id T_xxx` at ticket-filing time. Per op-guard-19, this section is mandatory for every commit/tag/release/push ticket; the alice-publish flow inherits the rule.
- **Operator authorization** — per the strike-8 emergency-exception protocol, every commit/tag/release/push requires operator authorization recorded as `kanban_comment` on the master ticket before the publish worker starts. The comment references the operator's authorization (chat timestamp, ticket comment id, or in-line quote).
- **CITATION.cff operator-gate state** — read CITATION.cff at pre-verify time; record operator-gate warnings in the master ticket body under `## CITATION.cff gate state`.

### Field 3 — Outputs

- **Commit on `main`** with structured message: `ALICE v<X.Y.Z>: <one-line summary>` followed by a body with stats (files changed, tickets closed, line counts) and source attribution.
- **Tag `v<X.Y.Z>`** annotated with release notes summary.
- **GitHub release** at `https://github.com/042Trix/Alice/releases/tag/v<X.Y.Z>` with structured notes (sections: `## What's new` + `## Stats` + `## Source` + `## License` + `## Author`).
- **Audit line** in `~/Documents/HermesVault/log.md`: `## [ISO8601-UTC] alice-publish — v<X.Y.Z> — result=<success|failure|refused> — release=<url>`. Appended via `vault_log.append_audit_line` per op-guard-11 (vault log.md is append-only).
- **Master ticket closed** via `kanban_complete` with the release URL in `--result` field and a structured handoff in `--metadata` (artifact path, release URL, contributing ticket ids).
- **Operator-facing Discord release message** (v0.3.0, t_a92c1f88) — posted to the operator's connected chat channel via `~/.hermes/tools/post_alice_release_discord.py` after STEP 10 completes. The message MUST follow the canonical topic-only format (see `### Output 6 — Operator-facing Discord release message format` below). Format: `Alice v<X.Y.Z>: <topic-1>, <topic-2>, ...`. NO article author, NO article URL, NO source handle. The article (if any) stays in the master ticket body's `## Source` block + the CHANGELOG `## Source` sub-section + the GitHub release notes `## Source` section; the Discord release message is topic-only. Source: operator 2026-08-09 Retro-H body observation #3 (verbatim: *"The message on the push should mention what topic was changed but not the article author, the article was just the inspiration for the change."*).

#### Output 6 — Operator-facing Discord release message format (NEW in v0.3.0)

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

**Implementation.** The Discord delivery is owned by `~/.hermes/tools/post_alice_release_discord.py` (NEW per the v0.3.0 instance work; invoked by STEP 10 after the GitHub release succeeds). The script reads the alice-framework CHANGELOG `## [v<X.Y.Z>]` entry, extracts the topic list (via the standard sub-sections `### Added` + `### Changed` + `### Fixed` + `### Out of scope`), and posts the canonical-format message via `send_dm_attachments`. Idempotent on (`version`, `release_url`): a release that has already produced a Discord message is skipped on re-run. The script header documents the canonical format + the `## Source`-block exception + the audit-line format. The 4-case regression suite at `~/.hermes/tests/test_post_alice_release_discord.py` covers: (1) release with 3 topics → `Alice v0.1.2: scoped node-admission rule, parallel fan-out/fan-in contract, Alice graph definition restatement`; (2) release with 1 topic → `Alice v0.1.2: <topic>`; (3) release with no topics (doc-only) → `Alice v0.1.2: documentation-only`; (4) re-run on already-delivered release → idempotent skip.

**Cross-reference.** The x-article-review flow (`methodology/M-decide-x-article-review-flow.md` v0.4.0 Part 3 `## Release-message discipline`) is the canonical source for the topic-only release-message rule; this Output 6 is the alice-publish-flow's instance-side rule. The rule lives at the x-article-review methodology level because the rule is about cross-flow attribution discipline (the article is the input to multiple flows; the release message format must be consistent across them). This doc (alice-publish) is the publisher of the format; the x-article-review doc is the owner of the rule.

### Field 4 — Process (10 steps + 5 gates)

The 10 steps run in order. Each step is verifier-gated (the gate is the verifier check that runs before the worker advances). The 5 NEW gates in v0.2.0 (STEP 1, STEP 4, STEP 5, STEP 6, plus the per-step verifier continuation in STEPs 7-10) close the drift surface from the v0.1.2 incident.

#### STEP 1 — Pre-verify gate (NEW in v0.2.0)

- **Run:** `python3 ~/.hermes/tools/verify_artifact_state.py ~/Documents/alice-framework --ticket-id T_xxx`
- **Gate:** the master ticket body MUST contain a `## Verified state` section with the script's full output captured at ticket-filing time. Per op-guard-19, this gate is mandatory; a ticket without the section fails pre-verify.
- **Output captured:** `git status --short`, `git tag -l`, `git log --oneline -5`, `git diff --stat HEAD`, `CITATION.cff version` + operator-gate warnings, `CHANGELOG.md` head (30 lines), per-file `version:` field scan across `methodology/*.md` + `templates/*.md.template`.
- **Failure mode:** re-run the script, paste the output into the ticket body under `## Verified state`, file or re-file the ticket. Do NOT advance to STEP 2 without the section present.

#### STEP 2 — Verify work is complete on disk

- **Run:** for each contributing ticket, check the artifact exists at the expected path (read the contributing ticket body for the artifact path; cross-reference with the per-file version field scan from STEP 1).
- **Gate:** every contributing ticket has `status=done` (per op-guard-10 v2 §1.1).
- **Failure mode:** file a follow-up ticket for any contributing ticket still in `todo` / `running` / `blocked` state. The publish worker does NOT advance until every contributing ticket is `done`.

#### STEP 3 — Verify verifier ran for each contributing ticket

- **Run:** for each contributing ticket, check the `verifier_passed` event in the run history (via `kanban_show` and the events table).
- **Gate:** every contributing ticket has a `verifier_passed` event.
- **Failure mode:** file a verifier ticket for any contributing ticket without `verifier_passed`. The publish worker does NOT advance until every contributing ticket is verifier-passed.

#### STEP 4 — CHANGELOG.md consolidation gate (NEW in v0.2.0)

- **Run:** read `CHANGELOG.md`; verify a single `## [v<X.Y.Z>]` entry exists. The single-entry invariant is mandatory (one entry per released version per the alice-framework release-prep discipline; multiple `[Unreleased]` blocks are the drift symptom from the v0.1.2 incident).
- **Run (if consolidation needed):** consolidate any `[Unreleased]` blocks into a single `## [v<X.Y.Z>]` entry with sub-sections for each change category (`### Added`, `### Changed`, `### Out of scope`, `### Source`, etc., following the format of the existing v0.1.2 entry).
- **Gate:** `CHANGELOG.md` has a single `## [v<X.Y.Z>]` entry with full source attribution. The bottom `<!-- Link references -->` HTML comment is updated to include the new tag URL.
- **Failure mode:** edit `CHANGELOG.md` to consolidate (with a `git mv CHANGELOG.md CHANGELOG.md.bak` backup before edit, per the alice-publish-intent v0.1.0 atomic-constraint; restore from `.bak` if the edit fails).

#### STEP 5 — Per-file version reconciliation gate (NEW in v0.2.0)

- **Run:** for each file under `methodology/*.md` + `templates/*.md.template` that has a `version:` field, check whether the field matches the release version or is appropriately domain-versioned (e.g., `00-decide-ticket-naming.md` may be at `v0.4.0` for the naming-discipline amendment stream; `04c-decide-master-ticket.md` may be at `v0.1.2` for the master-ticket amendment stream).
- **Reconciliation rule:** a file is reconciled when (a) its `version:` matches the release version, (b) its `version:` is in a domain-specific amendment stream that has already shipped a different version, OR (c) the master ticket body has an explicit reconciliation note naming the file + the version-keep rationale.
- **Gate:** every file's `version:` field is reconciled with the release version OR an explicit reconciliation note lives in the master ticket body.
- **Failure mode:** edit the file's `version:` field (atomic; `git mv` backup before edit; restore from `.bak` if the edit fails), OR add a reconciliation note to the master ticket body. The publish worker does NOT advance until every file is reconciled.

#### STEP 6 — CITATION.cff operator-gate check (NEW in v0.2.0)

- **Run:** read `CITATION.cff`; check for operator-gate warnings (lines containing `do not edit`, `operator gate`, `re-open`, `[OPERATOR`).
- **Run (if warnings present):** check the master ticket body for explicit operator authorization to re-open the gate (under `## CITATION.cff gate state` heading).
- **Gate:** CITATION.cff version field matches the release version OR explicit operator authorization is recorded in the master ticket body.
- **Failure mode:** operator must explicitly authorize the CITATION.cff bump (in chat, in a ticket comment, or in a kanban_comment on the master) before the publish worker proceeds. Without authorization, the publish worker blocks with reason `needs CITATION.cff operator authorization`. The CITATION.cff operator gate is a structural protection: the gate was first opened by `t_4054ccac` + parent `t_968068b0`, re-opened by `t_ecb73d81` Path C (2026-08-08), and any further re-opening is an operator-authorized action.

#### STEP 7 — Commit

- **Run:** `git add -A` (or specific files if operator asked for surgical commit). `git commit -m 'ALICE v<X.Y.Z>: <one-line summary>'` followed by body with stats + attribution.
- **Gate:** `git status --short` is clean post-commit (no uncommitted files).
- **Failure mode:** abort the publish worker; surface the conflict to operator via `kanban_block(kind=needs_input)` with reason `<commit conflict: <message>>`.

#### STEP 8 — Push to main

- **Run:** `git push origin main`.
- **Gate:** `git log --oneline -1 origin/main` shows the new commit on remote (verify via `git ls-remote origin main` or `gh api repos/042Trix/Alice/commits/main | jq '.sha'`).
- **Failure mode:** abort + surface the push error to operator. Do NOT tag a release that did not push. If the push fails due to remote-divergence (the rare case where the remote moved between STEP 1 pre-verify and STEP 8), block the publish worker with reason `remote-divergence: pull + re-run STEP 1 pre-verify`.

#### STEP 9 — Tag + push tag

- **Run:** `git tag -a v<X.Y.Z> -m 'ALICE v<X.Y.Z>: <one-line summary>'`. `git push origin v<X.Y.Z>`.
- **Gate:** `git tag -l` includes `v<X.Y.Z>`. `gh api repos/042Trix/Alice/tags | jq '.[].name'` includes `v<X.Y.Z>`.
- **Failure mode:** delete the local tag if push fails (`git tag -d v<X.Y.Z>`); surface the error to operator. Do NOT create a GitHub release until the tag is on remote.

#### STEP 10 — GitHub release + audit-line + close

- **Run:** `gh release create v<X.Y.Z> --title 'ALICE v<X.Y.Z>' --notes '<notes>'` (notes from STEP 4 + STEP 5 + STEP 6 reconciliation notes, with the canonical 5 sections `## What's new` + `## Stats` + `## Source` + `## License` + `## Author`).
- **Run:** append audit line to `~/Documents/HermesVault/log.md` via `vault_log.append_audit_line` (per op-guard-11, append-only).
- **Run:** `kanban_complete` with structured handoff carrying the release URL in `--result` field and the artifact path + release URL + contributing ticket ids in `--metadata`.
- **Gate:** `gh release list | head -1` shows v<X.Y.Z> as Latest. The audit line exists in `log.md`. The master ticket is closed.
- **Failure mode:** surface the error to operator; do NOT mark the master as done until all 10 steps are verified.

### Field 5 — Skill version (canonical-instance reference)

The canonical-instance implementation of this flow is the alice-publish loop at `~/.hermes/loops/hermes.yaml`. The loop and its intent file (`~/.hermes/loops/intents/alice-publish-intent.md`) MUST be amended to v0.2.0 to reference this doc; the doc is the canonical contract, the loop + intent are the derived instance.

The loop's worker_prompt field lists the 10 steps; STEP 1 (pre-verify) + STEP 4 (CHANGELOG consolidation) + STEP 5 (per-file version reconciliation) + STEP 6 (CITATION.cff operator-gate check) are NEW in v0.2.0 and replace the v0.1.0 implicit "verify before commit" steps with explicit verifier-gated gates. The loop's status field changes from `live-pending-test` (v0.1.0, awaiting validation) to `live` (v0.2.0, validated by this doc + the v0.1.2 incident retro).

### Field 6 — Acceptance criteria (verifier-gated)

The 9 acceptance criteria below are the verifier checks on `kanban_complete`. The verifier runs each check; the ticket closes only when all 9 pass.

1. **Pre-verify gate (STEP 1)** — the master ticket body contains a `## Verified state` section with `verify_artifact_state.py` output captured at ticket-filing time. The section is non-empty and the timestamp in the captured output is within 24h of the ticket's `created_at`.
2. **Contributing-ticket gates (STEP 2 + STEP 3)** — every contributing ticket has `status=done` + a `verifier_passed` event in the run history. Cross-checked against the `## Contributing tickets` list in the master ticket body.
3. **CHANGELOG gate (STEP 4)** — `CHANGELOG.md` has a single `## [v<X.Y.Z>]` entry (no `[Unreleased]` blocks survive the consolidation). The entry has full source attribution per the alice-framework release-prep discipline.
4. **Per-file version gate (STEP 5)** — every file's `version:` field is reconciled per the STEP 5 rule; any file not at v<X.Y.Z> has an explicit reconciliation note in the master ticket body.
5. **CITATION.cff gate (STEP 6)** — `CITATION.cff` version matches the release version OR the master ticket body has explicit operator authorization to re-open the gate.
6. **Commit gate (STEP 7)** — `git status --short` is clean post-commit. The commit message follows the `ALICE v<X.Y.Z>: <one-line summary>` format with body stats + attribution.
7. **Push gate (STEP 8)** — `git log --oneline -1 origin/main` shows the new commit on remote. Verified via `git ls-remote origin main` or `gh api`.
8. **Tag gate (STEP 9)** — `git tag -l` includes `v<X.Y.Z>`. `gh api repos/042Trix/Alice/tags | jq '.[].name'` includes `v<X.Y.Z>`.
9. **Release gate (STEP 10)** — `gh release list | head -1` shows `v<X.Y.Z>` as Latest. Audit line exists in `log.md`. Master ticket is closed with the release URL in `--result`.

A `kanban_complete` call that fails any of the 9 criteria is a verifier-gate failure; the ticket returns to the coder with the failed criteria enumerated in the verifier's `--result`.

## Part 3: Worked example — alice-framework v0.1.3

Below is a worked example of the 6-field flow spec applied to the canonical first publish under this doc: **alice-framework v0.1.3 — the publish-flow spec doc itself plus the loop/intent v0.2.0 amendments.** This example is itself a publish, so it walks through each gate with the expected state for the v0.1.3 ship.

### Goal

Publish alice-framework v0.1.3 (the alice-publish flow spec doc + the loop/intent amendments + CHANGELOG entry) to GitHub end-to-end. Multi-version cards rejected.

### Inputs

- `version: v0.1.3` in the master ticket body under `## Source`.
- `path: alice-publish` in the master ticket body under `## Source`.
- `## Contributing tickets` — 3 tickets: `t_fdba17e5` (doc-writer; this doc), `t_<coder-instance>` (coder; loop + intent amendments), `t_<verifier-instance>` (verifier; this doc's compliance check).
- `## Verified state` — captured output of `verify_artifact_state.py` at ticket-filing time.
- Operator authorization — `kanban_comment` on the master ticket at operator-authorization time, citing operator's chat timestamp or in-line quote.
- CITATION.cff gate state — at v0.1.2 with operator-gate warning (re-opened by `t_ecb73d81` Path C). Operator authorization to bump to v0.1.3 is recorded in the master ticket body.

### Outputs

- Commit `ALICE v0.1.3: M-decide-alice-publish-flow.md (alice-publish loop v0.2.0 with pre-verify + 5 gates)` on `main`.
- Tag `v0.1.3` annotated with the same summary.
- GitHub release at `https://github.com/042Trix/Alice/releases/tag/v0.1.3` with the canonical 5-section notes.
- Audit line in `~/Documents/HermesVault/log.md`: `## [2026-08-08T20:30Z] alice-publish — v0.1.3 — result=success — release=https://github.com/042Trix/Alice/releases/tag/v0.1.3`.
- Master ticket `t_<master-id>` closed via `kanban_complete` with the release URL.

### Process (10 steps + 5 gates)

The worker runs the 10 steps per Part 2 Field 4. Each gate fires before the worker advances. The pre-verify (STEP 1) catches drift before STEP 7's commit; the CHANGELOG consolidation (STEP 4) catches the `[Unreleased]` block drift that the v0.1.2 incident surfaced; the per-file version reconciliation (STEP 5) catches files at v0.4.0 or v0.1.2 that aren't at the v0.1.3 release version; the CITATION.cff operator-gate check (STEP 6) requires operator authorization to bump the gate.

### Skill version

- Loop: `~/.hermes/loops/hermes.yaml` `alice-publish` v0.2.0 (amended from v0.1.0 to reference this doc; status `live-pending-test` → `live`).
- Intent: `~/.hermes/loops/intents/alice-publish-intent.md` v0.2.0 (amended from v0.1.0 to reference this doc).
- Pre-verify: `~/.hermes/tools/verify_artifact_state.py` v0.1.0 (shipped 2026-08-08 per op-guard-19).
- Pre-flight: `~/.hermes/tools/loops_preflight.py` (for the STEP 7 commit + STEP 8 push verification).
- `gh` CLI at v2.x (for STEP 9 + STEP 10 release creation).

### Acceptance criteria

All 9 criteria from Part 2 Field 6 apply. The verifier runs each criterion; the master ticket closes only when all 9 pass.

## Part 4: Boundaries

This doc does NOT:

- Modify the alice-publish loop's worker profile assignment (`run_with_profile: coder`). The publish worker is a coder; the doc-writer's role is to maintain this methodology doc + the alice-framework CHANGELOG entry. Per op-guard-16, the doc-writer is spec-only and does not mutate `~/.hermes/loops/` files; the instance amendment ships via a follow-up coder ticket.
- Migrate any other loop to the v0.2.0 gate-quality bars. The v0.2.0 amendment is alice-publish-specific; other loops (loop-updater, requirements, stuck-sweep, x-article-review) keep their existing patterns. A future amendment may generalize the gates to other publish-style flows, but that's a separate ticket.
- Re-run `t_ecb73d81` against the new flow spec. The coder is already running on Path C for the v0.1.2 push; the new spec is the durable reference for future publishes (v0.1.3 onward).
- Create the GitHub release automation. STEP 10 still uses `gh release create`; no new tooling is introduced.
- Authorize the CITATION.cff gate re-opening. The CITATION.cff operator gate is a structural protection; only the operator can authorize a gate re-opening, and the authorization is recorded in the master ticket body at publish time, not in this methodology doc.

## Part 5: Maintenance

This methodology doc is the canonical contract for the alice-publish flow. Drift in this doc compounds across every Alice release that ships via this flow; the v0.1.2 push incident was the visible failure of v0.1.0 drift.

### Audit cadence

Audit this doc every **90 days** from the date it was last reviewed. The audit walks the 4 checks below; the cadence is a calendar event, not "when I remember."

**When to audit sooner than 90 days:**

- A publish fails on STEP 1 pre-verify (the script returns a state that doesn't match the master ticket body).
- A publish fails on STEP 4 CHANGELOG consolidation (multiple `[Unreleased]` blocks survive into STEP 7).
- A publish fails on STEP 5 per-file version reconciliation (an unreconciled file ships with the release).
- A publish fails on STEP 6 CITATION.cff operator-gate check (the gate is bumped without operator authorization).
- A new tool, agent, or routing convention changes which gates the dispatcher can satisfy.
- A council review or retro names this doc as a contributing cause of a publish failure.

An audit-on-failure is **added**, not substituted — the 90-day cadence still runs for this doc.

### Quality threshold

This doc passes the maintenance check when **all 6 fields are present, the goal field is one sentence, and the 5 new gates are documented as load-bearing steps.** The threshold is binary per field; the doc is acceptable when every field passes its own check.

**Per-field pass conditions:**

| Field | Pass condition |
|---|---|
| Goal | Exactly one sentence. Names the artifact (one Alice release published to GitHub). No embedded success criteria. |
| Inputs | At least one item. Each item names the source (path, ticket, or external). `version` / `path` / `## Verified state` / operator authorization / CITATION.cff gate state are present. No unbounded inputs. |
| Outputs | At least one item. Each item names the destination (commit hash, tag, release URL, audit-line path, master ticket transition). No activity-only outputs. |
| Process (10 steps + 5 gates) | 10 numbered steps; the 5 NEW gates (STEP 1, STEP 4, STEP 5, STEP 6, plus the per-step verifier continuation) are present and load-bearing. Each step has a gate and a failure mode. |
| Skill version | Names the loop version, intent version, pre-verify tool, pre-flight tool, `gh` CLI version. |
| Acceptance criteria | At least 9 criteria. Each criterion is a yes/no check or a numeric threshold. Covers pre-verify, contributing-ticket gates, CHANGELOG gate, per-file version gate, CITATION.cff gate, commit gate, push gate, tag gate, release gate. |

**Acceptance:** this doc, the alice-publish loop at v0.2.0, and the alice-publish intent at v0.2.0 form the canonical publish flow contract; the loop and intent reference this doc; the verifier checks the 9 acceptance criteria on every publish.

### Drift signals

Drift is the difference between this doc and the loop/intent it specifies. The audit catches drift on schedule; the signals catch drift between audits.

**Strong signals (audit immediately):**

- The alice-publish loop's `version:` field drifts from v0.2.0 (e.g., a coder patches the loop without updating the doc).
- A publish ships with multiple `[Unreleased]` blocks surviving STEP 4 (the gate fired but didn't enforce).
- A publish ships with an unreconciled file at v<X.Y.Z> per the STEP 5 rule.
- The CITATION.cff gate is bumped without operator authorization (the STEP 6 check fired but didn't enforce).
- The alice-framework v0.1.2-style incident recurs (a push with claim-vs-reality gaps).

**Weak signals (audit at next scheduled review):**

- A field has been copy-pasted between flows without re-checking (e.g., the v0.1.0 alice-publish loop's worker_prompt is patched to add a step without this doc being amended).
- A new gate is added to the loop without this doc being amended.

**How signals are detected:**

- Strong signals: surfaced by the dispatcher's per-tick reject log, by `kanban_block` reasons that name a failed gate, and by a one-shot audit script that scans the alice-publish loop's `worker_prompt` for steps not documented in this doc.
- Weak signals: surfaced by the audit-cadence calendar event and by the operator's manual review.

When a strong signal fires, **retro the failed publish first**, then audit this doc. The retro names which gate fired and how the failure cascaded; the audit decides whether the doc is structurally wrong or the loop drifted from the doc.

### Fix actions

When drift is detected, the corrective action is named in the audit record. The action is small, scoped, and reversible.

**Per-verdict actions:**

| Verdict | Action |
|---|---|
| All checks pass | Record audit date and next audit date (+90 days). No other action. |
| Goal is too long | Split the doc. Re-write the goal as one sentence and move the rest to the Process or Acceptance criteria. |
| A field is empty | Fill the field. If the field cannot be filled (e.g., a future gate is unknown), the doc is not ready to ship. |
| Process missing a step | Add the missing step. The 10-step + 5-gate structure is the canonical shape; deviations require a doc amendment. |
| Acceptance criteria short | Add the missing criterion. The 9 criteria are the verifier's check list; missing criteria let the verifier pass an under-gated publish. |
| Loop and doc drift | Re-align the loop. The doc is canonical; the loop and intent must reference the doc, not the other way around. |

### Retirement conditions

The doc retires when the alice-publish loop is replaced by a different mechanism (e.g., a fully automated GitHub Actions release pipeline that bypasses the dispatcher entirely). Until then, this doc is the canonical reference.

**Conditions that warrant a retirement proposal:**

- A fully-automated release pipeline ships to GitHub Actions and the dispatcher-based alice-publish loop is decommissioned.
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
- **Companion:** `op-guard-19-pre-verify-artifact-state-2026-08-08.md` — the pre-verify rule that drives STEP 1.
- **Companion:** `op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md` — alice-first / instance-second / compliance-gate. This doc is the Alice-canonical side; the loop + intent amendments are the instance side.
- **Companion:** `op-guard-11-log-md-append-only-2026-07-22.md` — vault log.md append-only rule. STEP 10's audit-line append uses `vault_log.append_audit_line`.
- **Companion:** `op-guard-10-no-close-on-age-2026-07-27.md` — STEP 2 + STEP 3 verify work + verifier per the closure-evidence rule.
- **Companion:** `methodology/04d-decide-flow-spec.md` — the canonical 6-field flow spec this doc adapts.
- **Companion:** `methodology/04a-decide-work-graph.md` Part 3.5 — the verifier-node contract that grounds the per-step gates.
- **Companion:** `methodology/04c-decide-master-ticket.md` — the master-ticket pattern. The alice-publish master is a master ticket; Part 8 Master Title Discipline + Part 7.5 done-gate opt-in both apply.
- **Companion:** `methodology/M-decide-x-article-review-flow.md` — sibling flow that also uses a master-ticket + done-gate + verifier pattern. The x-article-review flow is operator-facing (uses Retro-A + Retro-H); the alice-publish flow is git-facing (uses the 10-step publish pipeline + GitHub release). The two flows share the master-ticket substrate.
- **Instance:** `~/.hermes/loops/hermes.yaml` `alice-publish` loop v0.1.0 (the existing infra to amend to v0.2.0).
- **Instance:** `~/.hermes/loops/intents/alice-publish-intent.md` v0.1.0 (the existing intent to amend to v0.2.0).
- **Tool:** `~/.hermes/tools/verify_artifact_state.py` (shipped 2026-08-08; the STEP 1 pre-verify gate tooling).
- **Tool:** `~/.hermes/tools/loops_preflight.py` (the STEP 7 commit + STEP 8 push verification).
- **Tool:** `~/.hermes/scripts/vault_log.py` (the STEP 10 audit-line append helper, per op-guard-11).
- **Ticket:** `t_762cba52` (loop-builder; the alice-publish loop builder ticket; will become `live` once v0.2.0 ships).
- **Ticket:** `t_ecb73d81` (alice-framework v0.1.2 push ticket; running under coder via Path C; validates the new flow spec for v0.1.3).
- **Ticket:** `t_fdba17e5` (this doc's amendment ticket; doc-writer; the canonical contract ships via this ticket).

## Part 7: Verification checklist

Before marking the v0.2.0 ship complete, verify:

- [ ] `~/Documents/alice-framework/methodology/M-decide-alice-publish-flow.md` exists with the 6-field spec + 10-step process + 5 gates + 9 acceptance criteria.
- [ ] `~/.hermes/loops/hermes.yaml` `alice-publish` loop version 0.2.0; `worker_prompt` references this doc; status `live-pending-test` → `live`.
- [ ] `~/.hermes/loops/intents/alice-publish-intent.md` version 0.2.0; `amended_by_v0_2_0` entry referencing this doc.
- [ ] `python3 ~/.hermes/tools/loops_preflight.py ~/.hermes/loops/hermes.yaml` exits 0.
- [ ] `python3 -c "import yaml;yaml.safe_load(open('~/.hermes/loops/hermes.yaml'))"` exits 0.
- [ ] `CHANGELOG.md` v0.1.3 (or v0.2.0) entry cites this doc per the release-prep discipline (single `## [v<X.Y.Z>]` entry; no `[Unreleased]` blocks survive).
- [ ] Per-file version fields reconciled per STEP 5 (this doc at v0.2.0; loop + intent at v0.2.0; sister docs at their existing versions with reconciliation notes if non-matching).
- [ ] CITATION.cff operator gate explicitly authorized for the v<X.Y.Z> bump (in the master ticket body under `## CITATION.cff gate state`).
- [ ] `python3 ~/.hermes/tools/verify_artifact_state.py ~/Documents/alice-framework --ticket-id T_xxx` output captured at ticket-filing time, pasted into the master ticket body under `## Verified state`.
- [ ] Audit line appended to `~/Documents/HermesVault/log.md` via `vault_log.append_audit_line` (the doc-writer ships the audit-line for the doc-side; the publisher worker ships the audit-line for the publish-side).

## Anti-patterns to watch for

1. **"The flow is just a goal."** → No. Six fields. Every one is required. A flow with only a goal is a flow with five missing fields; the worker guesses on inputs, outputs, process, skill version, and acceptance criteria.
2. **"The 5 new gates are optional."** → No. The 5 new gates (pre-verify + CHANGELOG consolidation + per-file version reconciliation + CITATION.cff operator-gate check + the per-step verifier continuation) close the drift surface the v0.1.2 incident surfaced. Skipping any gate re-opens the v0.1.2 failure mode.
3. **"The doc and the loop are independent."** → No. The doc is canonical; the loop is derived. The loop's worker_prompt must reference the doc; the loop cannot drift from the doc without an audit-on-failure signal firing.
4. **"I'll add a step to the loop without amending the doc."** → No. The 10 steps are the doc's contract. The loop's worker_prompt is the doc's instance; the two must agree. Adding a step to the loop without amending the doc is a doc-writer violation per op-guard-16.
5. **"The CITATION.cff gate is just a comment in the file."** → No. The gate is a structural protection. The first gate was opened by `t_4054ccac` + parent `t_968068b0`; the second gate was re-opened by `t_ecb73d81` Path C. Any further re-opening is an operator-authorized action recorded in the master ticket body, not a routine patch.
6. **"The acceptance criteria are the same as the outputs."** → Outputs are the artifacts the flow produces; acceptance criteria are the conditions the verifier checks. A flow that produces a GitHub release (output) is not automatically acceptable; the criteria say what makes the release acceptable (e.g., the verifier confirms `gh release list` shows it as Latest, the audit line exists, the master is closed).
7. **"The release Discord message names the article author."** (NEW in v0.3.0, t_a92c1f88) → No. The canonical release-message format is `Alice v<X.Y.Z>: <topic-1>, <topic-2>, ...` — topic-only. The article author/URL/handle stays in the master ticket body's `## Source` block + the CHANGELOG `## Source` sub-section + the GitHub release notes `## Source` section. Attributing the release to the article author misattributes the operator's + council's + Alice team's work to a single data point in the methodology's research. Source: operator 2026-08-09 Retro-H body observation #3.

## Changelog

- **0.3.0** (2026-08-09, t_a92c1f88): Added Output 6 (operator-facing Discord release message) to Field 3. Canonical format: `Alice v<X.Y.Z>: <topic-1>, <topic-2>, ...` (topic-only; no article author/URL/handle). Empty topic list → `Alice v<X.Y.Z>: documentation-only`. New anti-pattern #7 codifies the rule. Companion instance changes (per op-guard-16 + op-guard-17 alice-first/instance-second): `~/.hermes/loops/intents/alice-publish-intent.md` v0.3.0 (intent ships the rule in STEP 10 worker_prompt), `~/.hermes/loops/hermes.yaml` `alice-publish` v0.3.0 (loop yaml ships in a coder child ticket), `~/.hermes/tools/post_alice_release_discord.py` (NEW; reads CHANGELOG entry, extracts topic list, posts via send_dm_attachments, idempotent on (version, release_url)), `~/.hermes/tests/test_post_alice_release_discord.py` (NEW; 4-case regression suite). Per op-guard-5 paired-wiki integrity, the methodology + intent + loop-yaml + script + test ship in one change set. Per op-guard-17 compliance-gate, a verifier child confirms the instance matches the methodology rule on first publish under v0.3.0. The canonical rule lives at the x-article-review methodology level (`methodology/M-decide-x-article-review-flow.md` v0.4.0 Part 3 `## Release-message discipline`); this doc is the publisher of the format. Source: operator 2026-08-09 Retro-H body observation #3 (verbatim: *"The message on the push should mention what topic was changed but not the article author, the article was just the inspiration for the change."*).
- **0.2.0** (2026-08-08, t_fb90b479; methodology t_fdba17e5): initial canonical publish-flow spec with pre-verify gate + CHANGELOG consolidation + per-file version reconciliation + CITATION.cff operator-gate check + per-step verifier continuation (5 NEW gates total). The alice-publish loop v0.2.0 in `~/.hermes/loops/hermes.yaml` references this doc; the loop's `status` field is `live`. Paired with `~/.hermes/loops/intents/alice-publish-intent.md` v0.2.0 (intent v0.1.0 → v0.2.0 ships the rule in the intent worker_prompt). Per op-guard-17 alice-first/instance-second, this doc is the alice-first; the loop + intent amendments are the instance children.

## Audit-line

`## [2026-08-08T20:30Z] alice-publish-flow-v0.2.0 — methodology doc shipped at `~/Documents/alice-framework/methodology/M-decide-alice-publish-flow.md`. Codifies the 6-field spec + 10-step publish pipeline + 5 NEW gates (pre-verify + CHANGELOG consolidation + per-file version reconciliation + CITATION.cff operator-gate check + per-step verifier continuation) + 9 acceptance criteria. Source: `t_ecb73d81` v0.1.2 push incident (4-gap pre-block audit) + operator 2026-08-08 direction to write a flow. Doc-writer amendment ticket: `t_fdba17e5`. Instance amendment (loop v0.1.0 → v0.2.0; intent v0.1.0 → v0.2.0) ships as follow-up coder ticket per op-guard-16 + op-guard-17.`