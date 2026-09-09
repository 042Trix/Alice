---
id: alice-methodology-M-decide-alice-local-align-flow
created: 2026-09-08T15:35:00Z
updated: 2026-09-08T15:35:00Z
title: "Methodology M — Decide the alice-local-align flow (the stage-4 meta-flow that auto-syncs local Hermes with published Alice releases via dual-trigger + mechanical-vs-non-mechanical classifier + lifecycle-phase HITL)"
type: methodology
status: draft
source: alice-framework
version: 0.1.0
tags: [kind:methodology, kind:flow-spec, kind:alice-local-align, kind:auto-sync, kind:meta-flow, kind:cron, kind:on-pass-trigger, kind:mechanical-vs-non-mechanical, kind:idempotency, kind:lifecycle-phase, kind:execution-phase-auto-accept, project:alice, kind:op-guard-35]
confidence: 0.85
alice-ticket: t_f62ed403
parent-meta: t_eb817293
amended_by: []
companion: ["[[methodology/04d-decide-flow-spec.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[methodology/04b-decide-board-routing.md]]", "[[methodology/04c-decide-master-ticket.md]]", "[[methodology/00-decide-ticket-naming.md]]", "[[methodology/M-decide-instance-vs-framework.md]]", "[[methodology/M-decide-alice-publish-flow.md]]", "[[methodology/M-decide-x-article-review-flow.md]]", "[[methodology/M-decide-graph-readiness.md]]", "[[methodology/M-decide-proactive-board-monitoring.md]]", "[[methodology/M-decide-spec-first-flow.md]]", "[[methodology/M-decide-versioning-discipline.md]]", "[[references/instance-leak-check-spec.md]]"]
links: ["[[methodology/04d-decide-flow-spec.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[methodology/04b-decide-board-routing.md]]", "[[methodology/04c-decide-master-ticket.md]]", "[[methodology/M-decide-instance-vs-framework.md]]", "[[methodology/M-decide-alice-publish-flow.md]]", "[[methodology/M-decide-x-article-review-flow.md]]", "[[methodology/M-decide-graph-readiness.md]]", "[[methodology/M-decide-proactive-board-monitoring.md]]", "[[2-ATOMIC/rules/op-guard-16-spec-first-flow-2026-08-05.md]]", "[[2-ATOMIC/rules/op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md]]", "[[2-ATOMIC/rules/op-guard-35-lifecycle-phase-hitl-contract-2026-09-08.md]]"]
teaching-example: true
---

# Methodology M — Decide the alice-local-align flow (v0.1.0)

> **After Alice publishes, the operator's local Hermes auto-syncs with the released canon — mechanical alignments auto-apply; structural changes surface as `[HUMAN ACTION]` children.** This methodology is the canonical contract for stage 4 of the operator's 4-stage meta-flow (Review → Apply to Alice → Publish to GitHub → **Local Hermes align**). The flow takes one published Alice release (vX.Y.Z) from "GitHub release live" to "local Hermes instance conforms to the released canon OR operator has been notified of structural deltas via `[HUMAN ACTION]` child tickets." The mechanical-vs-non-mechanical classifier is the load-bearing decision: skill bodies + intent version bumps + per-board topology annotations are mechanical (auto-apply with `skipped: already at vX.Y.Z` idempotency audit); new methodology gates / structural changes / cross-doc semantic deltas are non-mechanical (surface to operator via `[HUMAN ACTION]` per op-guard-13). The dual-trigger (cron `0 6 * * *` UTC + `on_pass` from alice-publish) closes the "operator forgets to run align after a publish" gap. The lifecycle-phase HITL contract (op-guard-35 / `t_eaba90cb`) governs the per-finding disposition: `status: live` graphs auto-accept mechanical; non-mechanical findings auto-file `[HUMAN ACTION]` per op-guard-3.

> **v0.1.0 canonical-promote (2026-09-08, t_f62ed403):** this doc is the v0.1.0 canonical-promote of the alice-local-align flow spec. Companion intent doc `~/.hermes/loops/intents/alice-local-align-intent.md` ships via coder follow-up `t_d2bf8467` per op-guard-16 spec-first-flow + op-guard-17 alice-first/instance-second/compliance-gate; loop yaml + worker + cron registration ship via `t_a3fc12ee`; the publish→align chain integration ships via `t_ef4fa984`; the test suite ships via `t_ca58cc2a`; the verifier done-gate ships via `t_d0b6ac1b` per op-guard-30. Source ticket: `t_f62ed403` (this doc's canonical-promote ticket); source parent META: `t_eb817293` (the alice-local-align stage-4 decomposition). The instance-side loop entry + intent doc + cron registration are derived from this doc and conform to its contract on the next audit cycle per op-guard-17.

## Part 1: Purpose

The `alice-local-align` flow takes one published Alice release (vX.Y.Z) from "GitHub release published + release URL posted to operator chat" to "local Hermes instance conforms to the released canon OR operator has been notified of structural deltas via `[HUMAN ACTION]` child tickets." The flow is the operator's contract for keeping local in sync with published canon without operator prompting — the dual-trigger (cron + on_pass) closes the "operator forgets to run align" gap; the mechanical-vs-non-mechanical classifier is the load-bearing decision that prevents operator-fatigue from per-finding surface; the lifecycle-phase HITL contract (op-guard-35) governs per-finding disposition.

Without this doc, the operator's local Hermes drifts from published canon: a published `v0.2.0` introduces a new op-guard or refactors a methodology doc, but the local instance's `~/.hermes/profiles/*/SOUL.md`, `~/.hermes/skills/*/SKILL.md`, `~/.hermes/cron/jobs.json`, and `~/.hermes/loops/intents/*-intent.md` remain at the prior version. The drift is silent until the operator runs a per-loop audit and discovers `status: live` graphs referencing op-guard-N that the local SKILL.md bodies do not name. The structural fix is stage 4: after every Alice publish, local Hermes auto-syncs via a deterministic classifier that distinguishes "drop-in version bump" from "operator-decision-required semantic change."

The flow is **operator-local** — it lives on the operator's Hermes instance, not in the Alice framework canon. The methodology doc is the framework-side contract that publishes the canonical pattern; the `alice-local-align` loop at `~/.hermes/loops/hermes.yaml` is the instance-side binding that operationalizes the pattern. The friend reading Alice later applies the dual-trigger + mechanical-vs-non-mechanical pattern to their own tool; the per-tool binding (e.g., a `git pull` + `diff` + classifier for a non-Hermes setup) is their own.

This doc is the canonical reference for the `alice-local-align` flow. The instance side is the `alice-local-align` loop at `~/.hermes/loops/hermes.yaml` (status: `live-pending-test` at ship time; promotion to `live` requires the 9-item per-loop audit per `methodology/M-decide-graph-readiness.md` Part 3) + its intent file at `~/.hermes/loops/intents/alice-local-align-intent.md`; both are derived from this doc and conform to its contract per op-guard-17. The companion alice-publish flow at `methodology/M-decide-alice-publish-flow.md` Part 2 Field 4 STEP 10 (post-publish trigger hook) is the `on_pass` firing surface; this doc is the canonical consumer of that hook.

## Part 2: Position in the 4-stage meta-flow

The operator's meta-flow for an external article ingest is 4 stages, in order:

| Stage | Flow | Source doc | Output |
|-------|------|------------|--------|
| 1 | Review the article (council-verdict + Retro-A + Retro-H) | `methodology/M-decide-x-article-review-flow.md` | Per-finding corrective-action-B children + Retro-H executive report |
| 2 | Apply to Alice (doc-writer amends Alice canon) | `methodology/M-decide-x-article-review-flow.md` Part 4 Phase 4 (post-Retro-H) | `~/Documents/alice-framework/` working-tree changes |
| 3 | Publish to GitHub (alice-publish 7-step + 6-gate pipeline) | `methodology/M-decide-alice-publish-flow.md` | GitHub release + Discord release message + audit-line |
| 4 | **Local Hermes align** (this doc) | `methodology/M-decide-alice-local-align-flow.md` (this doc) | Local instance conforms OR operator notified of structural deltas |

Stages 1–3 are the "outbound" surfaces (article → Alice → GitHub). Stage 4 is the "inbound" surface (GitHub → local instance). Without stage 4, the operator's local drifts from canonical; the operator re-discovers the drift on the next per-loop audit, which is the failure mode the dual-trigger closes.

The flow is invoked AFTER stage 3 completes (the alice-publish flow's STEP 9 audit-line + STEP 10 release-message delivery); the `on_pass` trigger fires the flow at the alice-publish flow's done-gate. The cron `0 6 * * *` UTC fires the flow at 06:00 UTC daily as a safety net for `on_pass` failures (e.g., a missing hook registration, a dropped `on_pass` signal during an alice-publish upgrade). The cron path is the canonical backstop; the `on_pass` path is the canonical primary.

## Part 3: The flow spec (adapted from `methodology/04d-decide-flow-spec.md`)

The canonical 6-field flow spec in `04d-decide-flow-spec.md` is **Goal / Inputs / Outputs / Success Criteria / Retry Parameters / Escalation Process**. The alice-local-align flow adapts the pattern to its dual-trigger + classifier nature: the flow's "retry" and "escalation" are uniform across all steps (transient → retry, hard-failure → operator-blocked), and the "outputs" include `[HUMAN ACTION]` children for non-mechanical findings + a classification-table audit-line. The verifier-gated acceptance criteria substitute for the canonical "Success Criteria" field with a sharper "the verifier checks these N conditions" framing.

### Field 1 — Goal

Sync the local Hermes instance with one published Alice release (vX.Y.Z) end-to-end with the mechanical-vs-non-mechanical classifier gating every per-finding action. Mechanical findings (skill body bumps, intent version bumps, per-board topology annotations) auto-apply with `skipped: already at vX.Y.Z` idempotency audit; non-mechanical findings (new methodology gates, structural changes, cross-doc semantic deltas) surface as `[HUMAN ACTION]` children per op-guard-3 + op-guard-13. **Multi-version cards rejected** (one release per invocation; one version per master ticket). **Stage-4-only-cards rejected** (the flow only fires AFTER stage 3 ships; pre-publish versions are not aligned — they are not yet canonical).

### Field 2 — Inputs

- **`version: v<X.Y.Z>`** — required in the master ticket body. The version MUST match the most recent `git tag -l` in the alice-framework repo (per op-guard-19 pre-verify artifact state); the flow refuses to align a version that has no corresponding GitHub release.
- **`path: alice-local-align`** — required in the master ticket body. Routes the dispatcher to this loop. Per `methodology/04c-decide-master-ticket.md` Master Title Discipline, the title carries the descriptive topic; the version goes in `## Source` block.
- **`## Source` block** — the alice-publish release URL + the GitHub tag name + the version + the canonical-promote commit hash (the `git rev-parse HEAD` at alice-publish STEP 7 commit). The block is the audit-trail pointer; downstream verifiers cross-check the local git fetch against this URL.
- **`## Verified state`** — the captured output of `python3 ~/.hermes/tools/verify_artifact_state.py ~/Documents/alice-framework --ticket-id T_xxx` at ticket-filing time (per op-guard-19). For `on_pass` triggered cards, the verified state is captured by the alice-publish flow's STEP 9 (the audit-line + release-message delivery); the verifier cross-checks the captured state against the actual ship-time state on the alice-publish ticket.
- **Trigger source** — either `trigger=on_pass` (with `parent_ticket=<alice-publish-ticket-id>` referencing the alice-publish master) OR `trigger=cron_daily_06_utc` (no parent ticket). The trigger source is recorded in the master ticket body under `## Trigger`; the cron path records `trigger=cron_daily_06_utc` + `last_alice_publish_version=<vX.Y.Z>` (the version the cron last aligned against, from `~/.hermes/cron/output/alice-local-align/last_aligned_version`).
- **Local git working-tree state** — the cron `verify_local_repo_state` pre-flight checks that `~/Documents/alice-framework/` is on `main` + clean + has no uncommitted changes + has the alice-framework remote configured. A dirty working tree refuses to proceed (the flow does not know which version the dirty tree represents).
- **Operator authorization for force-align** — per the strike-8 emergency-exception protocol, every force-align (the operator manually invokes the flow against a version that was not just published — e.g., re-aligning after a rollback, or aligning a back-ported version) requires operator authorization recorded as `kanban_comment` on the master ticket before the align worker starts. The comment references the operator's chat timestamp + the version under force-align.

### Field 3 — Outputs

- **Mechanical-auto-apply audit line** — appended to `~/Documents/HermesVault/log.md` per op-guard-11: `## [ISO-TIMESTAMP] alice-local-align — version=v<X.Y.Z> trigger=<on_pass|cron_daily_06_utc> — result=<success|skipped|partial|failure|refused> — mechanical_applied=<N> non_mechanical_filed=<M> skipped_idempotent=<K>`. The audit-line carries the per-classify counts (mechanical + non-mechanical + skipped-idempotent) so a future auditor can answer "what did this align do" without re-running the flow.
- **Mechanical-applied diff table** — captured in the master ticket body under `## Mechanical-applied (classifier output)`: a table with one row per finding, columns = `path` + `old_sha256` + `new_sha256` + `classifier_rationale` + `applied_at=<ISO>`. Each row is the audit-trail for one drop-in apply; the verifier cross-checks the diff against the local working-tree after the worker completes.
- **Non-mechanical `[HUMAN ACTION]` children** — for each non-mechanical finding (a structural change, a new methodology gate, a cross-doc semantic delta), the worker files one `[HUMAN ACTION] alice-local-align: <non-mechanical-finding-title>` child ticket. The child ticket's body carries: the finding path + the published Alice diff at that path + the operator's recommended disposition (ACCEPT-and-apply / DEFER-and-keep-local / REJECT-and-pin-to-prior). The parent ticket id (the alice-local-align master) is captured in the child's `parents` array. Per op-guard-3 + op-guard-13, the `[HUMAN ACTION]` prefix is the canonical operator-DM surface.
- **Idempotent-skip audit line** — for each mechanical finding where the local already matches the published canon (sha256 match), the worker appends an audit line per op-guard-11: `## [ISO-TIMESTAMP] alice-local-align-skipped — path=<file> version=v<X.Y.Z> — already at v<X.Y.Z>`. The audit-line is the durable record that the alignment was evaluated but not applied (per the operator's "tell me you checked even when nothing changed" standard).
- **Master ticket closed** via `kanban_complete` with the audit-line id + the per-classify counts + the `[HUMAN ACTION]` child ids in `--metadata` + the result string (`success` / `skipped` / `partial` / `failure` / `refused`) in `--result`. The summary cites the absolute paths of any mechanical-applied files + the `[HUMAN ACTION]` child ticket ids.
- **`last_aligned_version` state file** — written to `~/.hermes/cron/output/alice-local-align/last_aligned_version` per the cron's success path. The file is the canonical "last successful align" record; the cron's next tick reads it to determine if a new alignment is needed (cron path only fires when the alice-publish latest tag is newer than `last_aligned_version`; the `on_pass` path always fires regardless).

### Field 4 — Process (4 steps + 5 gates)

The 4 steps run in order. Each step is verifier-gated (the gate is the verifier check that runs before the worker advances). The 5 NEW gates in v0.1.0 close the dual-trigger surface (STEP 1 + STEP 2) + the classifier discipline (STEP 3) + the HITL contract (STEP 4).

#### STEP 1 — Trigger intake + version resolution

- **Run:** the worker reads the master ticket body + resolves the version source:
  - `trigger=on_pass` → read `parent_ticket=<alice-publish-ticket-id>` from the master body; fetch the alice-publish ticket via `kanban show`; extract the version from the alice-publish master's `--result` field.
  - `trigger=cron_daily_06_utc` → read `last_aligned_version` from `~/.hermes/cron/output/alice-local-align/last_aligned_version`; fetch the alice-framework remote tag list via `git ls-remote --tags https://github.com/042Trix/Alice.git | tail -1`; compare the latest tag against `last_aligned_version`.
- **Gate (canonical rule):** the resolved version MUST exist on the alice-framework remote tag list (the flow refuses to align an unpublished version; pre-publish versions are not yet canonical). The `git rev-parse <version>` succeeds locally after `git fetch` (per the cron's `verify_local_repo_state` pre-flight, which fetches the alice-framework remote before tag resolution).
- **Refuse conditions:** (a) the resolved version has no matching remote tag → `kanban_block --reason="alice-local-align: version v<X.Y.Z> has no matching remote tag on the alice-framework repo; refuse to align unpublished versions"`; (b) the local working tree is dirty → `kanban_block --reason="alice-local-align: local working tree is dirty; refuse to align an unknown state"`; (c) the `on_pass` parent's alice-publish master is not `done` → `kanban_block --reason="alice-local-align: parent alice-publish ticket <id> is not done; refuse to align before publish completes"`.

#### STEP 2 — Diff fetch + finding extraction

- **Run:** the worker runs `git diff <last_aligned_version_or_main>..<version> --stat` to capture the file-list changed; then `git diff <last_aligned_version_or_main>..<version> -- <file>` per changed file to capture the per-file diff. The worker extracts the per-file findings: each file with changed lines becomes a candidate finding with `path` + `old_sha256` + `new_sha256` + the per-line diff body.
- **Gate (canonical rule):** the diff MUST be non-empty (a zero-finding diff means the version has no canonical deltas — the flow exits with `result=skipped` + audit-line `skipped: zero-finding diff between <last_aligned_version_or_main> and <version>`). The per-file diff size MUST be < 1MB (a diff > 1MB triggers a `kanban_block --reason="alice-local-align: diff between <last_aligned_version_or_main> and <version> exceeds 1MB; manual review required"` because mechanical auto-apply of large diffs is a structural-change surface, not a drop-in alignment).

#### STEP 3 — Mechanical-vs-non-mechanical classifier

- **Run:** the worker applies the classifier per finding (Part 4 below). The classifier reads the per-file diff body + applies the pattern match + assigns a class (mechanical / non-mechanical) + writes the finding to the classification table.
- **Gate (canonical rule):** every finding MUST be classified into exactly one of `mechanical` / `non-mechanical` (the classifier is exhaustive — see Part 4). Findings where the pattern match is ambiguous (e.g., a methodology doc with a content change that does NOT match the mechanical pattern set) are classified `non-mechanical` by default (the conservative path — operator review is the safe default; mechanical auto-apply of ambiguous findings is the unsafe default). The classifier rationale is recorded per finding in the classification table.

#### STEP 4 — Auto-apply mechanical + file `[HUMAN ACTION]` for non-mechanical

- **Run:** the worker iterates the classification table:
  - For each `mechanical` finding: apply the diff to the local working tree (per Part 4 §Mechanical auto-apply sub-section); append the idempotent-skip audit line if the local sha256 already matches.
  - For each `non-mechanical` finding: file one `[HUMAN ACTION] alice-local-align: <finding-title>` child ticket (per Part 4 §Non-mechanical `[HUMAN ACTION]` sub-section).
- **Gate (canonical rule, lifecycle-phase HITL per op-guard-35):** the mechanical auto-apply sub-step operates ONLY within `~/Documents/alice-framework/` skill pack (skill bodies, intent version bumps, per-board topology annotations). The auto-apply NEVER touches `~/.hermes/profiles/{dorothy,maria-agent,hermes-agent,hermes-operator}/*` (the operator's per-profile skill packs — the operator explicitly excludes these from mechanical alignment per the constraints in Part 5). The non-mechanical `[HUMAN ACTION]` sub-step fires for any finding outside the mechanical scope (per op-guard-3 + op-guard-13).
- **Audit-line:** the worker appends the per-classify-counts audit line per op-guard-11 + writes the `last_aligned_version` state file on success.

### Field 5 — Skill version (canonical-instance reference)

The canonical-instance implementation of this flow is the `alice-local-align` loop at `~/.hermes/loops/hermes.yaml`. The loop's worker_prompt + the intent file (`~/.hermes/loops/intents/alice-local-align-intent.md`) MUST be amended to reference this doc; the doc is the canonical contract, the loop + intent are the derived instance.

The loop's `status` field is `live-pending-test` at ship time (per `methodology/M-decide-graph-readiness.md` Part 3 + op-guard-30 enforcement). Re-promotion to `live` requires the 9-item per-loop audit to pass — items 1, 2, 5, 6, 8, 9 are the substance gates (item 1 = canonical methodology doc exists + cross-references the loop, item 2 = intent doc mirrored, item 5 = verifier-PASS, item 6 = paired-wiki atomic, item 8 = production run, item 9 = node-type discipline); items 3 + 7 are the promotion gates (item 3 = yaml flip post-PASS, item 7 = operator HITL disposition per op-guard-32). The first production run is the cron tick on the day after the first alice-publish with `on_pass` registered + the first cron tick at 06:00 UTC post-registration; the verifier-runner tickets `t_ca58cc2a` (test suite) + `t_d0b6ac1b` (compliance verifier per op-guard-17) confirm items 5 + 6 + 9 in the same change set.

### Field 6 — Acceptance criteria (verifier-gated)

The 10 acceptance criteria below are the verifier checks on `kanban_complete`. The verifier runs each check; the ticket closes only when all 10 pass.

1. **Trigger-intake gate (STEP 1)** — the resolved version exists on the alice-framework remote tag list (verified via `git ls-remote --tags`). The `## Trigger` section in the master ticket body records the trigger source + the version source.
2. **Local-repo-state gate (STEP 1)** — the local `~/Documents/alice-framework/` is on `main` + clean + has the alice-framework remote configured (verified via `git status` + `git remote -v`). The cron's pre-flight runs this check before tag resolution.
3. **Diff-fetch gate (STEP 2)** — the diff between `last_aligned_version_or_main` and the resolved version is non-empty (a zero-finding diff exits with `result=skipped` + audit-line; the verifier confirms the skip path was taken, not a silent success). The diff size is < 1MB (a >1MB diff triggers a manual-review `kanban_block` per STEP 2 Gate).
4. **Classifier-exhaustiveness gate (STEP 3)** — every finding in the diff is classified into exactly one of `mechanical` / `non-mechanical`. The classification table is captured in the master ticket body under `## Classification (STEP 3)` with one row per finding + the classifier rationale. The classifier rationale for `non-mechanical` defaults (ambiguous findings) is recorded as `ambiguous-default-non-mechanical`.
5. **Mechanical-auto-apply gate (STEP 4)** — for each `mechanical` finding, the local file's sha256 AFTER auto-apply matches the published file's sha256 (the auto-apply is correct; no partial apply). For findings where the local already matched pre-apply, the idempotent-skip audit line exists in `~/Documents/HermesVault/log.md` per op-guard-11 + carries `decision_ref=alice-local-align-idempotent-skip`. The verifier cross-checks the post-apply sha256 against the published sha256 for every mechanical row.
6. **Mechanical-scope gate (STEP 4)** — the auto-apply NEVER touches `~/.hermes/profiles/{dorothy,maria-agent,hermes-agent,hermes-operator}/*`. The verifier enumerates the auto-applied paths + confirms zero entries in the excluded scope. A violation is a hard fail.
7. **Non-mechanical `[HUMAN ACTION]` gate (STEP 4)** — for each `non-mechanical` finding, exactly one `[HUMAN ACTION] alice-local-align: <finding-title>` child ticket exists with `parents=[alice-local-align-master-id]`. The child body carries the path + the published Alice diff at that path + the operator's recommended disposition (ACCEPT-and-apply / DEFER-and-keep-local / REJECT-and-pin-to-prior). Per op-guard-3 + op-guard-13, the `[HUMAN ACTION]` prefix is the canonical operator-DM surface.
8. **Audit-line gate (STEP 4)** — the per-classify-counts audit line exists in `~/Documents/HermesVault/log.md` per op-guard-11 + carries the 4 canonical fields (version + trigger source + result + per-classify counts). The `last_aligned_version` state file is written on success (the verifier confirms the file's mtime is post-ticket-creation).
9. **Lifecycle-phase HITL gate (STEP 4, op-guard-35)** — the per-finding disposition is governed by `lifecycle_phase_gate.should_surface_hitl(loop_id="alice-local-align", graph_status="live-pending-test" or "live", finding_severity="normal", finding_class="reversible-cheap" or "destructive")`. For `live` + `reversible-cheap` + `normal`, the helper returns `should_hitl=False` (auto-accept; the mechanical path); for any other combination, the helper returns `should_hitl=True` (file `[HUMAN ACTION]`; the non-mechanical path). The verifier cross-checks the classification-vs-helper decision per finding.
10. **Idempotency gate (overall)** — re-running the flow on the same version is a no-op (the `last_aligned_version` state file matches the resolved version; the diff is empty; the worker exits with `result=skipped` + audit-line `skipped: already at v<X.Y.Z>`). The verifier runs the flow twice on a fixture release + confirms the second run's audit-line carries `skipped: already at v<X.Y.Z>` + the per-classify counts are zero + no child tickets are filed.

A `kanban_complete` call that fails any of the 10 criteria is a verifier-gate failure; the ticket returns to the worker with the failed criteria enumerated in the verifier's `--result`.

## Part 4: The mechanical-vs-non-mechanical classifier

The classifier is the load-bearing decision of the flow. Every finding (a per-file diff between `last_aligned_version_or_main` and the resolved version) is classified into exactly one of two classes:

- **`mechanical`** — drop-in apply; the local working tree's file can be replaced with the published file's contents without operator review.
- **`non-mechanical`** — operator-decision-required; the local working tree's file has a content change that is not a drop-in replacement, OR the path is outside the mechanical scope, OR the change has cross-doc semantic implications.

The classifier is **exhaustive** (every finding gets exactly one class) and **conservative-by-default** (ambiguous findings default to `non-mechanical` — the operator-review path is safe; mechanical auto-apply of ambiguous findings is unsafe).

### Mechanical-pattern set

A finding classifies as `mechanical` if AND ONLY IF the per-file diff body matches at least one of the following patterns:

1. **Intent version bump** — the file is under `~/.hermes/loops/intents/` + the diff is a single-line `version:` field change (e.g., `version: 0.5.0 → version: 0.6.0`). The intent doc's other fields (worker_prompt, status, etc.) are unchanged. Auto-apply: replace the `version:` line in the local intent doc with the published version.
2. **Skill-body metadata bump** — the file is under `~/.hermes/skills/<name>/SKILL.md` + the diff is a single-line `version:` field change in the frontmatter. The skill's `description` + body + scripts + references are unchanged. Auto-apply: replace the `version:` line in the local SKILL.md frontmatter with the published version.
3. **Per-board topology annotation** — the file is under `~/Documents/alice-framework/concepts/kanban-board-topology-*.md` (the per-instance topology concept note) + the diff is a single-line change to the `## All boards` enumeration (add or remove a board entry). Auto-apply: replace the enumeration line in the local topology note with the published enumeration.
4. **Methodology doc `updated:` field bump** — the file is under `~/Documents/alice-framework/methodology/` + the diff is a single-line `updated:` frontmatter field change (timestamp-only; no body change). Auto-apply: replace the `updated:` line in the local methodology doc with the published timestamp.

A finding with multiple line changes (more than one `version:` line, or a `version:` change plus another field change) is NOT mechanical — it's a structural change (pattern #5 in the non-mechanical set).

### Non-mechanical-pattern set (everything else)

A finding classifies as `non-mechanical` if it does NOT match the mechanical-pattern set, OR if it matches any of these explicit non-mechanical patterns:

5. **Methodology gate introduction** — a new `## Part N: <title>` section in `~/Documents/alice-framework/methodology/*.md` introduces a new gate (e.g., a new verification step, a new acceptance criterion, a new operator-review pattern). The classifier detects a new section header that does not exist in the prior version + the section content references gates / verification / acceptance / HITL.
6. **Cross-doc semantic delta** — the change in one file references a change in another file (e.g., a methodology doc references a new op-guard rule + the op-guard rule file exists at a new path). The classifier detects cross-file references that did not exist in the prior version.
7. **Path outside mechanical scope** — the file is under `~/.hermes/profiles/{dorothy,maria-agent,hermes-agent,hermes-operator}/*` (the operator's per-profile skill packs, explicitly excluded per the constraints in Part 5). ANY change to a path in the excluded scope is non-mechanical regardless of the diff size or shape.
8. **CITATION.cff or CHANGELOG.md mechanical scope expansion** — a CHANGELOG.md or CITATION.cff change that is NOT a single-line `version:` bump is a non-mechanical change (the operator reviews every CHANGELOG entry + every CITATION.cff operator-gate warning). Single-line version bumps in CHANGELOG.md are out of mechanical scope (the mechanical scope is restricted to specific files in the per-instance repo + alice-framework concepts/methodology; CHANGELOG entries are operator-facing summaries).
9. **Template file** — a change to any file under `~/Documents/alice-framework/templates/` is non-mechanical (templates are operator-facing + change-of-shape; auto-apply could overwrite an operator's local customization).
10. **New file** — a file that exists in the published version but not in the local working tree (new file in the canonical) is non-mechanical by default. The operator reviews the new file before it lands locally.

A finding matching multiple patterns (e.g., a new methodology gate that also references a new op-guard rule) is classified by the first matching pattern in the priority order above; the classifier rationale records the matched pattern + the priority-order index.

### Mechanical auto-apply sub-procedure

For each `mechanical` finding, the worker executes the drop-in apply:

1. Read the published file from the alice-framework repo at the resolved version (`git show <version>:<file>`).
2. Compute the published file's sha256.
3. Compute the local file's current sha256.
4. If the sha256s match → skip the apply + append the idempotent-skip audit line + record the row in the classification table as `applied=skipped_idempotent`.
5. If the sha256s do NOT match → apply the published file's contents to the local working tree (the file write is the mechanical change; no operator review).
6. After apply (or skip), re-compute the local file's sha256 + verify it matches the published sha256 (the auto-apply is correct; no partial apply).

The auto-apply NEVER touches `~/.hermes/profiles/{dorothy,maria-agent,hermes-agent,hermes-operator}/*` (the constraints in Part 5); a finding in the excluded scope is re-classified `non-mechanical` regardless of the pattern match (the classifier's mechanical-pattern set is restricted to specific files; paths in the excluded scope are not in the pattern set).

### Non-mechanical `[HUMAN ACTION]` sub-procedure

For each `non-mechanical` finding, the worker files one `[HUMAN ACTION] alice-local-align: <finding-title>` child ticket per op-guard-3 + op-guard-13:

1. **Title:** literal `[HUMAN ACTION] alice-local-align: <finding-title>` prefix. The `finding-title` is a one-line description of the structural change (e.g., `[HUMAN ACTION] alice-local-align: methodology/M-decide-graph-readiness.md v0.3.0 Part 4 §B.5 findings-report contract introduction`).
2. **Body:** the body carries 4 sections — `## Finding path` (the file path) + `## Published diff` (the per-file diff body at the resolved version, formatted as a code block) + `## Operator disposition` (the recommended disposition: ACCEPT-and-apply / DEFER-and-keep-local / REJECT-and-pin-to-prior) + `## Classifier rationale` (the matched non-mechanical pattern + the priority-order index + the rationale for the conservative-default disposition).
3. **`parents` array:** the child ticket's `parents` includes the alice-local-align master ticket id (the structural link is the master → child edge).
4. **`kanban_comment` cross-link:** the worker appends a `kanban_comment` to the master ticket body with the child ticket id + the per-finding classification row from the classification table (the master ticket body's classification table references the child ticket id for every non-mechanical row).

The `[HUMAN ACTION]` child tickets are operator-DM surfaced per op-guard-13; the operator-action-dm cron picks them up on the next tick. The operator dispositions via the standard `[HUMAN ACTION]` child-ticket protocol (per `concepts/human-action-child-pattern.md` + `references/operator-action-disposition--matthew-2026-07-20.md`).

### Lifecycle-phase HITL contract (op-guard-35 / `t_eaba90cb`)

The mechanical-vs-non-mechanical classifier is the canonical application of the lifecycle-phase HITL contract from `methodology/M-decide-graph-readiness.md` Part 3.5 + `2-ATOMIC/rules/op-guard-35-lifecycle-phase-hitl-contract-2026-09-08.md`:

- **`status: live-pending-test` (audit/promotion phase)** — every per-finding finding surfaces to the operator (HITL is required for all classes); the classifier's mechanical-auto-apply is NOT enabled at `live-pending-test` (the loop is not yet promoted; auto-apply would skip the audit surface).
- **`status: live` (execution phase)** — `reversible-cheap` + `normal` findings auto-accept (the mechanical path); `destructive` + `critical` findings surface to the operator (the non-mechanical path). The classifier's mechanical-pattern set maps to `reversible-cheap` + `normal` (drop-in version bumps are reversible-cheap; no destructive action required). The non-mechanical-pattern set maps to `destructive` + `critical` (a new methodology gate is destructive of the prior local state; the operator must approve).
- **Phase-invariant MUST-surface markers** — `severity=critical` + `class=destructive` + profile/lane swap + public surface + paid spend ALWAYS surface to the operator-DM cron regardless of phase (per op-guard-13). The classifier honors these markers: a finding that crosses one of these surfaces (e.g., a change to `~/.hermes/profiles/dorothy/SOUL.md` is a profile/lane-swap marker) is `non-mechanical` regardless of the pattern match.

The lifecycle-phase helper (`~/.hermes/scripts/lifecycle_phase_gate.py::should_surface_hitl`) is the canonical decision table; the classifier defers to the helper per finding. The helper's `decision_ref` is captured in the master ticket body's classification table (per finding + the helper's decision + the rationale).

## Part 5: Constraints

The alice-local-align flow operates under 5 hard constraints. Each constraint is the load-bearing rule that prevents the flow from drifting into operator-hostile behavior; violation of any constraint is a hard fail per the acceptance criteria.

### Constraint 1 — Never touch dorothy / maria-agent / hermes-agent / hermes-operator without operator ack

The auto-apply scope is restricted to `~/Documents/alice-framework/concepts/methodology/templates/references/` + `~/.hermes/loops/intents/` + `~/.hermes/skills/<canonical-name>/SKILL.md` + `~/.hermes/cron/jobs.json` (the alice-framework skill pack on the local instance). The auto-apply NEVER touches `~/.hermes/profiles/{dorothy,maria-agent,hermes-agent,hermes-operator}/*` (the operator's per-profile skill packs for the family agents + the hermes-agent project + the hermes-operator dashboard).

A change to a path in the excluded scope is ALWAYS `non-mechanical` regardless of the diff shape; the worker files a `[HUMAN ACTION]` child ticket for the operator to disposition manually. The operator explicitly chose this restriction (per the standing rules at `2-ATOMIC/rules/operational-defaults-2026-07.md` + the family-architecture boundary at `~/.hermes/profiles/coder/memories/MEMORY.md`); the alice-local-align flow does not override the operator's choice.

### Constraint 2 — Mechanical only within alice-framework skill pack on the local instance

The mechanical auto-apply operates ONLY within the alice-framework skill pack on the local instance. A finding for a file outside the alice-framework skill pack is `non-mechanical` regardless of the diff shape. The boundary is: anything that the alice-publish flow would ship to GitHub (per `methodology/M-decide-alice-publish-flow.md` Field 2 + Field 3) is in scope for mechanical auto-apply on the local instance; anything else is out of scope.

### Constraint 3 — Append + annotate, not destructive overwrite

The mechanical auto-apply is a drop-in replace for files that the classifier has approved. The auto-apply NEVER deletes a local file that exists in the working tree but not in the published version (a deleted file in canonical is a non-mechanical finding; the operator reviews the deletion before the local file is removed). The auto-apply NEVER modifies a local file that has uncommitted changes (a dirty local file is a manual-review surface; the flow refuses to proceed per STEP 1 Refuse condition (b)).

The audit-trail discipline is preserved per op-guard-11 + op-guard-19: every mechanical auto-apply records the per-row `path` + `old_sha256` + `new_sha256` + `applied_at` in the master ticket body's mechanical-applied diff table; every non-mechanical `[HUMAN ACTION]` child ticket records the path + the published diff + the classifier rationale. The audit trail is the durable record; the local working tree is the surface.

### Constraint 4 — Idempotency is mandatory

Re-running the flow on the same version MUST be a no-op (the `last_aligned_version` state file matches the resolved version; the diff is empty; the worker exits with `result=skipped` + audit-line `skipped: already at v<X.Y.Z>`). The cron path uses the `last_aligned_version` state file as the alignment base; the `on_pass` path uses the alice-publish parent's `--result` version as the alignment base.

The idempotency gate (Field 6 acceptance criterion #10) verifies the no-op behavior: the verifier runs the flow twice on a fixture release + confirms the second run's audit-line carries `skipped: already at v<X.Y.Z>` + the per-classify counts are zero + no child tickets are filed. A flow that re-applies (creating duplicate mechanical changes + duplicate `[HUMAN ACTION]` children) is a violation; the flow MUST recognize the `already at v<X.Y.Z>` state and exit cleanly.

### Constraint 5 — Version discipline per `M-decide-versioning-discipline.md`

The flow honors the version-discipline contract from `methodology/M-decide-versioning-discipline.md` Parts 8-10 (Keep a Changelog 1.1.0 + Semver.org 2.0.0 + Conventional Commits 1.0.0 + GitHub Releases docs). The resolved version MUST match a tag on the alice-framework remote (a tag created by the alice-publish flow's STEP 8 per `methodology/M-decide-alice-publish-flow.md` Part 2 Field 3 Output 3); a version that exists as a working-tree commit but not as a tag is REFUSED (the alice-publish flow is the canonical version-discipline surface; the alice-local-align flow consumes that surface).

The version comparison uses Semver 2.0.0 ordering (the resolved version MUST be `>` `last_aligned_version` per the canonical semver order; a version that is `<` `last_aligned_version` is REFUSED — the flow does not downgrade local to an older canonical). A force-align (operator-authorized per Field 2) is the only escape from the version-ordering discipline; the operator's `kanban_comment` records the force-align rationale.

## Part 6: Cross-references + companion flows

### Companion: `methodology/M-decide-alice-publish-flow.md`

The alice-publish flow (stage 3) is the canonical version-discipline surface: the flow's STEP 8 creates the git tag + STEP 9 delivers the audit-line + the `on_pass` trigger fires the alice-local-align flow at the alice-publish flow's done-gate. The `on_pass` trigger is the canonical primary; the cron `0 6 * * *` UTC is the canonical backstop.

### Companion: `methodology/M-decide-x-article-review-flow.md`

The x-article-review flow (stage 1) emits the per-finding corrective-action-B children that drive stages 2–4. The article-driven amendments to Alice canon (stage 2) are the upstream input to alice-publish (stage 3) → alice-local-align (stage 4). The cross-flow topic-only release-message discipline (`methodology/M-decide-x-article-review-flow.md` Part 3 `## Release-message discipline`) is the canonical source for the rule that `methodology/M-decide-alice-publish-flow.md` Part 2 Field 3 Output 6 publishes.

### Companion: `methodology/M-decide-graph-readiness.md` + Part 3.5 lifecycle-phase HITL

The graph-readiness methodology Part 3.5 (the canonical lifecycle-phase HITL contract from `t_eaba90cb`) is the substance the alice-local-align classifier conforms to. The classifier's mechanical-vs-non-mechanical pattern set maps to the helper's `should_surface_hitl` decision table per `2-ATOMIC/rules/op-guard-35-lifecycle-phase-hitl-contract-2026-09-08.md`. The 9-item per-loop audit rubric (Part 3) governs the alice-local-align loop's promotion to `live`.

### Companion: `2-ATOMIC/rules/op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md`

The doc-writer + coder + verifier chain for the alice-local-align flow conforms to op-guard-17 alice-first / instance-second / compliance-gate. This doc is the Alice doc (alice-first); the loop yaml + intent doc + cron registration ship via `t_a3fc12ee` (instance-second); the compliance-verifier `t_d0b6ac1b` confirms the instance conforms to this doc (compliance-gate). The META does not close until the compliance-verifier `done`.

### Companion: `methodology/M-decide-proactive-board-monitoring.md`

The cron `0 6 * * *` UTC trigger for the alice-local-align flow follows the auto-discovered board allow-list pattern from `methodology/M-decide-proactive-board-monitoring.md` Part 2. The alice-local-align cron's `last_aligned_version` state file is at `~/.hermes/cron/output/alice-local-align/last_aligned_version`; the cron's pre-flight (the `verify_local_repo_state` check) follows the proactive-monitor pattern of read-only state checks before action.

### Companion: `methodology/M-decide-versioning-discipline.md`

The version discipline honored by the alice-local-align flow is the canonical version-discipline contract at `methodology/M-decide-versioning-discipline.md` Parts 8-10 (Keep a Changelog 1.1.0 + Semver.org 2.0.0 + Conventional Commits 1.0.0 + GitHub Releases docs). The flow's STEP 1 tag resolution + the version-ordering discipline + the force-align escape are the local-align side of the canonical contract.

### Companion: `methodology/M-decide-instance-vs-framework.md`

The alice-local-align flow is operator-local (instance-side binding of the framework-side pattern). Per `methodology/M-decide-instance-vs-framework.md` v0.1.1 Part 2 decision tree Q3 — the doc describes BOTH the abstract concept (the dual-trigger + classifier pattern, friend-portable) AND the concrete operator binding (the loop + intent + cron registration, hermes-specific). The split is: this doc is the abstract concept; the loop + intent + cron registration are the concrete binding. The split is the canonical application of the instance-vs-framework distinction.

### Companion: `2-ATOMIC/rules/op-guard-35-lifecycle-phase-hitl-contract-2026-09-08.md`

The lifecycle-phase HITL contract (op-guard-35) is the canonical source for the classifier's HITL disposition. The helper `~/.hermes/scripts/lifecycle_phase_gate.py::should_surface_hitl` is the decision table; the classifier defers to the helper per finding. The phase-invariant MUST-surface markers (severity=critical + class=destructive + profile/lane swap + public surface + paid spend) are honored regardless of phase.

## Part 7: Boundaries (what this methodology does NOT cover)

- **The x.com article-review flow (stage 1).** Owned by `methodology/M-decide-x-article-review-flow.md`. The alice-local-align flow does not see x.com content; it sees the published Alice canon.
- **The Alice doc-writer amendments (stage 2).** Owned by the x-article-review flow's Phase 4 + the doc-writer profile. The alice-local-align flow does not write Alice docs; it reads + classifies + auto-applies or surfaces.
- **The alice-publish git push (stage 3).** Owned by `methodology/M-decide-alice-publish-flow.md`. The alice-local-align flow does not push to GitHub; it consumes the `on_pass` trigger + the canonical tag.
- **The operator's per-profile skill packs (dorothy / maria-agent / hermes-agent / hermes-operator).** Owned by the operator per the family-architecture boundary at `~/.hermes/profiles/coder/memories/MEMORY.md`. The alice-local-align flow does not auto-apply changes to these paths (per Constraint 1); non-mechanical `[HUMAN ACTION]` children fire for any change.
- **The Alice framework canon's internal structure (the methodology doc taxonomy, the template shape, the references organization).** Owned by the alice-framework doc-writer + the operator's framework decisions. The alice-local-align flow does not restructure Alice; it consumes the canonical as published.

## Part 8: Maintenance

### When this methodology changes

This doc ships at v0.1.0; future amendments follow the same change-set discipline as `methodology/M-decide-graph-readiness.md`:

1. **Mechanical amendments** (typo fixes, `updated:` field bumps, link additions) ship as PATCH version increments (v0.1.0 → v0.1.1).
2. **Classifier-pattern additions** (a new mechanical pattern, a new non-mechanical pattern) ship as MINOR version increments (v0.1.0 → v0.2.0).
3. **Lifecycle-phase HITL contract changes** (a new phase-invariant marker, a new helper decision-table row, a new arm-hitl override surface) ship as MAJOR version increments (v0.1.0 → v1.0.0) — these changes affect every flow that consumes the helper.

### When the alice-local-align loop changes

Per op-guard-17 + op-guard-5 paired-wiki integrity: every change to the loop yaml + intent doc + cron registration MUST update this doc in the same change set. The `amended_by` array in the frontmatter carries the ticket id of every amendment; the `updated:` frontmatter field is bumped to the change-time timestamp.

### Audit cadence

The per-loop audit (per `methodology/M-decide-graph-readiness.md` Part 3) runs on the canonical cadence (after every amendment to this doc, plus the weekly bulk-audit per the per-loop audit pattern). The audit verifies: (a) this doc exists + cross-references the loop (item 1); (b) the intent doc mirrors this doc's contract (item 2); (c) the regression suite is green (item 4); (d) the verifier-PASS is recorded (item 5); (e) the paired-wiki atomic change set is on disk (item 6); (f) the loop has been used end-to-end at least once with `done` terminal status (item 8); (g) each graph node is named with type + rationale (item 9). Items 3 + 7 are the promotion gates (post-PASS).

## Part 9: Versioning

### v0.1.0 (2026-09-08, t_f62ed403)

Initial canonical-promote of the alice-local-align flow spec. Codifies: dual-trigger (cron `0 6 * * *` UTC + `on_pass` from alice-publish) + mechanical-vs-non-mechanical classifier (4 mechanical patterns + 7 explicit non-mechanical patterns) + lifecycle-phase HITL contract per op-guard-35 (per-finding defer to `lifecycle_phase_gate.should_surface_hitl`) + idempotency (`skipped: already at v<X.Y.Z>` audit line) + version discipline per `methodology/M-decide-versioning-discipline.md` Parts 8-10 + 5 hard constraints (no operator-profile auto-apply + alice-framework scope only + append+annotate not destructive + idempotency + version discipline) + 10 verifier-gated acceptance criteria.

The version starts at `0.1.0` (no prior canonical; this is the first ship). The companion intent doc is `~/.hermes/loops/intents/alice-local-align-intent.md` (shipped via coder follow-up `t_d2bf8467` per op-guard-16 spec-first-flow + op-guard-17 alice-first/instance-second/compliance-gate); the loop + intent + cron registration are the derived instance that conforms to this doc on the next audit cycle per op-guard-17. CITATION.cff is NOT bumped (not operator-authorized in this change set); the doc lands in the canonical home without an accompanying release.

### Source

- Source ticket: `t_f62ed403` (this doc's canonical-promote ticket; doc-writer profile).
- Source parent META: `t_eb817293` (alice-local-align stage-4 decomposition; planner profile).
- Operator directive: 2026-09-05 chat ("after publish, local Hermes auto-syncs with the published canon").
- Companion coder follow-ups: `t_d2bf8467` (intent doc) + `t_a3fc12ee` (yaml + worker + cron) + `t_ef4fa984` (publish→align chain) + `t_ca58cc2a` (tests) + `t_d0b6ac1b` (compliance verifier).
- Companion source META from parent: `t_eb817293` alice-local-align decomposition (Phase 0 PASS + Phase 1.5 headless defaults shipped).
