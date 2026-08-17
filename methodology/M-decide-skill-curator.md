---
id: alice-methodology-M-decide-skill-curator
created: 2026-08-17T16:00:00Z
updated: 2026-08-17T16:00:00Z
title: "Methodology M — Decide skill-curator (the canonical operator-environment invariant the loop preserves)"
type: methodology
status: draft
source: alice-framework
version: 0.1.0
tags: [kind:methodology, kind:loop-methodology, kind:skill-drift, kind:daily-cron, kind:audit-line-only, project:alice]
confidence: 0.9
alice-ticket: t_ab1f3081
teaching-example: true
companion: ["[[methodology/02-decide-skills.md]]", "[[methodology/M-decide-graph-readiness.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[methodology/M-decide-spec-first-flow.md]]"]
links: ["[[methodology/M-decide-graph-readiness.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[methodology/02-decide-skills.md]]", "[[methodology/M-decide-spec-first-flow.md]]", "[[methodology/05-op-guards.md]]", "[[2-ATOMIC/rules/op-guard-11-log-md-append-only-2026-07-22.md]]", "[[2-ATOMIC/rules/op-guard-9-cron-loop-completeness-2026-07-21.md]]", "[[2-ATOMIC/rules/op-guard-15-must-call-terminal-2026-07-29.md]]", "[[2-ATOMIC/rules/op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md]]"]
source-ticket: t_ab1f3081
parent-meta: t_3d2bdf98
amended_by: []
---

# Methodology M — Decide skill-curator (v0.1.0)

> **A daily 04:00 cron MUST audit every skill under `~/.hermes/skills/` for size + duplicate drift and append an audit-line to the vault log; the loop MUST NOT Discord-DM the operator and MUST NOT touch dorothy / maria-agent / hermes-agent / hermes-operator skill bodies without operator approval.** This methodology is the friend-portable contract for the `skill-curator` loop. Without it, skill drift accumulates silently and the operator catches it by accident; with it, the drift is detected on cadence, surfaced through the audit trail (vault log + `0-INBOX/` markdown), and corrected under explicit constraints.

## Part 1: Why this methodology exists

Skills are the procedural memory of a Hermes-style operator environment. Each skill body (`SKILL.md` + `references/` + `templates/` + `scripts/` + `assets/`) is a load-bearing artifact: agents load skills on cold-start, consult them at decision time, and trust their content to be current. Without periodic curation, three failure modes recur:

1. **Size drift.** A skill grows by accretion (new pitfalls, new commands, new edge cases) past the size budget that the cold-start context loader assumes. The skill starts truncating or pushing other skills out of context. The operator notices the symptom (poor agent performance) but the cause (oversized skill) is invisible.
2. **Duplicate drift.** Two skills cover overlapping territory. An agent following one skill misses what the other would have told it. The duplication surfaces as a verifier-FAIL or a verifier-FAIL-pattern, but the duplicate's existence is not surfaced by routine scans.
3. **Cross-cutting skill body edits.** A curator that auto-merges or auto-splits skills without constraint will cross wires into skills that the operator wants to preserve verbatim (e.g., `dorothy` / `maria-agent` for the second-agent lane; `hermes-agent` / `hermes-operator` for host-level skills the operator curates by hand).

The `skill-curator` loop closes the failure-mode triangle. It runs daily at 04:00, scans every skill under `~/.hermes/skills/` for size + duplicate compliance, makes bounded auto-corrections (auto-split oversized; auto-merge same-category duplicates), and writes a durable audit trail. The contract — *what the loop must and must not do* — is what this methodology pins down so a friend reading Alice can rebuild the loop from the spec.

**Why this is a methodology, not just a yaml entry.** The `skill-curator` loop is the canonical example of an *auditable, deterministic, daily cron with operator-policy gates*. The same shape recurs across the operator's environment (skill-curator, project-state-snapshot, multi-day-status-cron, wiki-lint). The methodology captures the pattern so each instance does not reinvent the gates.

## Part 2: The contract — what the loop must preserve

The `skill-curator` loop preserves one invariant: **every skill body in `~/.hermes/skills/` stays within size budget, has no same-category duplicates, and is auditable through the vault log**. The invariant is broken into three sub-invariants the loop enforces each tick:

### Invariant A — size budget compliance

Every `SKILL.md` MUST stay under the canonical size budget (the default is 14 KB; the operator's environment may override per category). Oversized skills are auto-split into a primary + a `references/<topic>.md` companion, with the primary's body rewritten to point at the companion. The split MUST be bounded (max 5 sections extracted per skill per run) to prevent runaway edits.

### Invariant B — duplicate detection + same-category merge

Two skills covering the same territory are a duplicate. The loop MUST detect duplicates within the same category (autonomous-ai-agents with autonomous-ai-agents, etc.) and auto-merge them when the merged body fits the size budget. Cross-category duplicates are surfaced in the audit output as `needs-manual` and NOT auto-merged; the operator decides.

### Invariant C — protected skill bodies

The loop MUST NOT edit the body of any skill in the protected set: `dorothy`, `maria-agent`, `hermes-agent`, `hermes-operator`. These are operator-curated; their body is canonical by operator approval, not by routine scan. The curator scans them (to report size) but does not auto-split or auto-merge them. Edits to these skills require explicit operator approval in the chat transcript.

## Part 3: Inputs (what the curator reads)

| Surface | What the curator reads |
|---|---|
| **Skill manifests** | Every `~/.hermes/skills/<name>/SKILL.md` under `~/.hermes/skills/` (excluding the protected set's bodies — Invariant C). |
| **Skill metadata** | Frontmatter `name`, `description`, `version` fields, the `category` if present, the file size in bytes. |
| **Skill files** | The full body of each `SKILL.md` + the size of every file under the skill's `references/`, `templates/`, `scripts/`, `assets/` subdirectories. |
| **Yesterday's audit summary** | The previous day's `0-INBOX/skill-curator-<date-1>.md` file (if present) for idempotency. The curator reuses yesterday's per-skill size record and only re-scans skills that changed since yesterday. |
| **Protected-set policy** | The literal set `{dorothy, maria-agent, hermes-agent, hermes-operator}`. The curator hardcodes the set as policy; the operator can amend the set by editing the curator's policy block (not by chat message). |
| **Vault log state** | `~/Documents/HermesVault/log.md` for the audit-line append target (per op-guard-11 — append-only via `vault_log.append_audit_line`). |

## Part 4: Outputs (what the curator writes)

The curator writes exactly three artifacts per tick, in this order:

### Output 1 — `0-INBOX/skill-curator-<ISO-date>.md`

The audit markdown for human reading. The file MUST include:

- **Counts line.** `checked: N; in-range: N; too-big: N; merged: N; needs-manual: N` where each count is the per-tick totals.
- **Per-change list.** Each skill the curator touched gets a row with: name, before/after size (KB), what changed (auto-split: section name extracted → references path; auto-merge: merged-into skill name + net size change).
- **Needs-manual list.** Cross-category duplicates, skills past the size budget that no auto-split could fix, skills whose frontmatter is malformed.
- **NO fabricated skill names.** The curator MUST NOT invent skill names that did not appear in the scan; any per-change row references a real, observed skill.

The file MUST be ≥ 1 KB of markdown (a tick that scans zero skills is a tick that didn't fire; the file size gate catches that). File path template: `0-INBOX/skill-curator-<ISO-date>.md` (per the loop yaml entry).

### Output 2 — vault log audit-line (per op-guard-11)

Every tick appends exactly one line to `~/Documents/HermesVault/log.md` via `vault_log.append_audit_line` (the canonical append-only helper; **never** `write_text` or `open("a")` directly — the 2026-07-22 truncation incident (`t_682219d8`) was a curator's read-modify-write on this file). The schema, per the loop yaml:

```
## [ISO8601-UTC] loop-run — skill-curator v<loop_version> @ doc-writer v<profile_version> — result=<success|failure|refused> — artifact=<file-path>
```

`result=success` when the audit file + audit-line both land. `result=failure` when either is missing or malformed (the curator transitions the loop to `failure` per op-guard-9 + op-guard-15 and the next tick retries). `result=refused` when the operator has paused the curator via the gate flag (the loop runs but emits only the audit-line, no audit file).

### Output 3 — NO Discord DM

The loop MUST NOT call `send_dm` / `hermes-send` / any Discord delivery path. This is operator policy, captured in the yaml entry's `worker_prompt` block, and the curator's stop_when condition fails-closed if a Discord delivery is attempted. The operator reads the vault log + the `0-INBOX/` file when they want a picture; they do not want a daily 04:00 DM. (The output the curator produces is `DM: suppressed (operator policy — audit-line only)` — printed to stdout, not delivered.)

## Part 5: Cadence + ownership

| Surface | Cadence | Owner | What it does |
|---|---|---|---|
| `skill-curator` cron | daily 04:00 (`0 4 * * *`) | Scripts/Code (`~/.hermes/tools/skill_curator.py`) | Scan + auto-split/merge + audit file + audit-line. |
| Loop yaml entry | registered at `~/.hermes/loops/hermes.yaml` `id: skill-curator` | Scripts/Code (yaml) | Names the cron schedule, the worker prompt, the audit-line schema, the file path template, the protected-set policy. |
| Loop intent doc | registered at `~/.hermes/loops/intents/skill-curator-intent.md` | Scripts/Code (markdown) | Worker-prompt version pin, dispatcher registration contract. |
| Compliance-verifier child | per `M-decide-spec-first-flow.md` Part 2 — a verifier ticket that runs the 8-check discipline from `M-decide-graph-readiness.md` Part 5 on the loop's current state. | Agents (verifier profile) | Confirms the loop's contract still holds after every env change. |
| Operator review | ad-hoc | Human (operator) | Reads the daily `0-INBOX/skill-curator-<date>.md` + vault log; flags skills for manual curation when the curator cannot auto-fix. |

**Cadence rationale.** Daily 04:00 catches drift within one sleep cycle. The cron is the canonical detect leg (per op-guard-9); the audit-line is the canonical surface (the loop is auditable-but-silent on Discord per operator policy); the operator review is the canonical act leg (the curator cannot decide which skills are "needs-manual" without operator input on cross-category duplicates).

## Part 6: Node-type discipline (item 9 of the 9-item promotion rubric)

Per `methodology/M-decide-graph-readiness.md` v0.1.1 Part 3 item 9 + `methodology/04a-decide-work-graph.md` Part 4, every node in the work graph MUST use its proper type. The `skill-curator` loop has three nodes, one of each canonical type:

- **Scripts/Code node — `~/.hermes/tools/skill_curator.py`.** The curator's scan + auto-split + auto-merge logic is deterministic and idempotent. It walks the filesystem, compares sizes against the budget, runs bounded regex matches against `SKILL.md` bodies, and writes the audit file + audit-line. The Scripts/Code node is the canonical place for the work because the loop's contract is *deterministic checks against a known state* — exactly what scripts are for. An Agent (LLM-driven) interpretation would risk non-determinism (different splits on different runs), which violates the size budget + idempotency invariants.
- **Agents node — `doc-writer` profile (the loop's `run_with_profile`).** The dispatcher registers the curator's tick as a job assigned to the `doc-writer` profile. The Agent node is responsible for the loop-level orchestration: claiming the cron-triggered job, running the worker prompt, and routing the result through `kanban_complete` (per op-guard-15 must-call-terminal). The Agent node does NOT do the scan itself; the scan is delegated to the Scripts/Code node. The Agent's job is the *interpretation of the cron schedule + the dispatcher contract*, which is exactly what Agents are for (judgment + bounded output, deterministic validation in the next node).
- **Human node — operator review.** The operator reviews the daily `0-INBOX/` file + the vault log. The Human node owns cross-category duplicate decisions (the curator surfaces them as `needs-manual`; the operator picks which to merge or keep), edits to protected skill bodies (Invariant C requires operator approval in chat), and the cadence policy itself (daily vs weekly, 04:00 vs other hour). The Human gate is also the failure-recovery surface: when the curator fails a tick, the operator's eyes on the vault log are what decide whether the loop needs a manual recovery or the next scheduled tick will retry naturally.

The canonical phrase per `04a-decide-work-graph.md` Part 4 (Scripts/Code / Agents / Human) is satisfied: each step in the curator's contract maps to exactly one of the three node types, with no Agent node doing deterministic work and no Scripts/Code node doing interpretation.

## Part 7: Failure modes + recovery

| Failure mode | Detect | Act | Verify |
|---|---|---|---|
| **Curator can't find `~/.hermes/skills/`** | The scan raises `FileNotFoundError` at startup. | Curator transitions to `result=failure` per op-guard-15 + op-guard-9; the next cron tick retries. Operator sees the failure via the audit-line. | Next tick succeeds, OR operator manually restores the path. |
| **Audit file < 1 KB (zero-skill scan)** | File size gate catches the missing counts. | Curator rewrites the file with `checked: 0; in-range: 0; too-big: 0; merged: 0; needs-manual: 0` + a `## Zero-skill scan warning` section. Audit-line records `result=success` (the gate is "file exists + ≥ 1 KB"; the warning is in the body). | Operator reads the file + decides whether the missing `~/.hermes/skills/` is a host-level issue. |
| **Audit-line append fails** (e.g., disk full, perms) | `vault_log.append_audit_line` raises. | Curator transitions to `result=failure` per op-guard-11; the loop does not retry the audit-line write until the next tick (audit-line is idempotent on retry — a second `## [...] loop-run` line for the same ISO date is harmless). | Next tick's audit-line includes the prior failure summary. |
| **Auto-split blew the size budget** (split went the wrong direction) | Next tick's size check catches the regression. | Curator auto-undoes the split (re-merges the section back into the primary); audit-line records the rollback. | Size budget compliance restored on next tick. |
| **Cross-category duplicate not auto-merged** | Curator reports `needs-manual` in the audit file. | Operator reviews the file + decides merge / keep. | Operator's decision lands in the next tick's audit (the curator re-reads yesterday's summary and notes which `needs-manual` items the operator resolved). |
| **Curator touched a protected skill body** (Invariant C violation) | The verifier profile (item-5 compliance-verifier child) catches the violation on the next `M-decide-graph-readiness.md` Part 5 check 8 walk. | Curator rollback (re-reads the protected skill's git history or the operator's last-approved snapshot). Audit-line records the rollback. Verifier fails the compliance-verifier child. | Compliance-verifier re-runs; the loop is back at `live-pending-test` after the rollback. |
| **Cron never fires** | `jobs.json` shows `last_run=NULL` for `skill-curator` past 24h. | `kanban_rescue_watcher` + `cron-loop-completeness` chain (op-guard-9) detects the missed tick + files a `[HUMAN ACTION]` ticket to operator-DM. | Operator inspects cron registration; next tick fires. |

The failure-mode table is the canonical surface for retro-action. A new failure mode that recurs 3 times across 3 ticks becomes a new row in the table + a paired-wiki update to the methodology (per op-guard-5).

## Part 8: How to apply this methodology

When onboarding the `skill-curator` loop in a new operator environment:

1. **Copy the script.** Place `~/.hermes/tools/skill_curator.py` from the canonical source (this methodology + the alice-framework repo's `examples/` if available, else hand-port the spec from Part 3 + Part 4).
2. **Register the cron.** Add `0 4 * * *` to the user's crontab (or the equivalent on non-cron platforms), pointing at the script's venv python.
3. **Register the loop entry.** Add `skill-curator` to `~/.hermes/loops/hermes.yaml` with the canonical schema (worker_prompt + audit_line_schema + file_path_template + crontab_entry). Initial status: `live-pending-test`.
4. **Register the intent doc.** Add `~/.hermes/loops/intents/skill-curator-intent.md` (the version-pinned dispatcher contract) per the loop-updater loop's `worker_prompt`.
5. **Run the first tick manually.** `python3 ~/.hermes/tools/skill_curator.py` from the operator shell; verify the audit file + audit-line both land; verify no Discord delivery fires.
6. **Verify the cron.** Wait one day, or trigger the cron manually (`cron <crontab>` after editing the crontab). Verify the 04:00 tick fires.
7. **File the compliance-verifier child.** Per `M-decide-spec-first-flow.md` Part 2 + `M-decide-graph-readiness.md` Part 5, the META ticket that registers the loop has a verifier child that runs the 8-check discipline. The verifier child confirms: scripts/agents/human node types map correctly; size budget is respected; protected-set policy holds; audit-line schema matches; file size gate catches zero-skill scans; no Discord delivery path is invoked.
8. **Promote to `live`.** Once all 9 items of the rubric pass (per `M-decide-graph-readiness.md` Part 3), flip the yaml `status:` field + bump the intent doc `version:` field + append an audit-line per op-guard-11.

A loop that fails any check is `rework`, not shipped (per `M-decide-graph-readiness.md` Part 5 check 8 — node-type discipline is a release-blocker that supersedes items 1–8).

## Part 9: Backwards compatibility

This methodology applies forward from 2026-08-17 (initial ship). Pre-existing skill-curator instances in operator environments are grandfathered: their existing cron schedule + audit file schema + protected-set policy continue to apply. The methodology is the *canonical reference* for what the loop must look like in any Alice-compliant environment; existing instances that already satisfy Part 2 + Part 4 + Part 6 require no changes.

The 2026-07-22 `t_682219d8` incident (the vault log truncation caused by a curator read-modify-write) is the canonical evidence for Output 2's `vault_log.append_audit_line` requirement. Pre-2026-07-22 curators that wrote directly via `open("a")` are grandfathered for forensic traceability, but new code MUST use the helper (per op-guard-11).

The protected-set policy (`{dorothy, maria-agent, hermes-agent, hermes-operator}`) is operator-policy, not a methodology invariant. Different operator environments may have different protected sets; the canonical reference for the operator's environment is the yaml entry's `worker_prompt` block (Part 5 surface). The methodology names the *shape* of the policy (a protected set exists; the curator reads but does not touch); the operator names the *contents*.

## Part 10: Cross-references

- **`methodology/02-decide-skills.md`** — the canonical skills methodology. The curator is the operational surface for the skills discipline; this methodology is the loop that enforces it.
- **`methodology/04a-decide-work-graph.md`** — Part 4 (the 3-node-type taxonomy: Human / Scripts/Code / Agents) is the design-time discipline that Part 6 codifies at promotion-time per `M-decide-graph-readiness.md` Part 3 item 9.
- **`methodology/M-decide-graph-readiness.md`** v0.1.1 — the 9-item promotion rubric that includes the loop's `live`-readiness bar. Part 3 item 1 (methodology doc) + item 9 (node-type discipline) are the two items this doc flips; items 2, 4, 5, 6 are the per-loop promotion path; items 3, 7, 8 are the runtime + operator review gates.
- **`methodology/M-decide-spec-first-flow.md`** — the 4-phase flow + V-1/V-2/V-3 verifier steps. The compliance-verifier child (Part 5 of `M-decide-graph-readiness.md`) is the per-loop application of the V-2 step.
- **`methodology/05-op-guards.md`** — the operational-guard umbrella. Op-guards 9, 11, 15, 17 are the load-bearing rules for this loop; op-guard-5 is the paired-wiki rule that makes the methodology + intent + yaml + tests atomic.
- **`~/.hermes/tools/skill_curator.py`** — the canonical Scripts/Code node. The script's behavior is the spec in code form; the methodology is the spec in prose form.
- **`~/.hermes/loops/hermes.yaml` `id: skill-curator`** — the canonical yaml binding. The yamlet's `worker_prompt` block + `audit_line_schema` are the runtime contract; the methodology is the friend-portable contract.
- **`~/.hermes/loops/intents/skill-curator-intent.md`** — the canonical intent doc (per the loop-updater loop's discipline). The intent doc's `version:` field is bumped per `loop-updater` invocation; the methodology's `version:` field is bumped per amendment.
- **`~/Documents/HermesVault/log.md`** — the canonical vault log. Every curator tick appends exactly one line via `vault_log.append_audit_line` per op-guard-11.
- **`2-ATOMIC/rules/op-guard-9-cron-loop-completeness-2026-07-21.md`** — the detect → surface → act → verify chain that the curator's cron + audit-line + audit-file + operator-review chain honors.
- **`2-ATOMIC/rules/op-guard-11-log-md-append-only-2026-07-22.md`** — the audit-line append-only discipline. The 2026-07-22 truncation incident is the canonical evidence for the helper's existence.
- **`2-ATOMIC/rules/op-guard-15-must-call-terminal-2026-07-29.md`** — the must-call-terminal rule. The curator's tick MUST call `kanban_complete` (or transition to `failure` via `kanban_block`) before the cron returns; exit-without-terminal-call is a protocol violation per the rule.
- **`2-ATOMIC/rules/op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md`** — the alice-first / instance-second / compliance-gate discipline. The compliance-verifier child that Part 5 names IS the op-guard-17 done-gate child for the loop's META.
- **Source tickets:** `t_ab1f3081` (this methodology doc's ship ticket; doc-writer), `t_3d2bdf98` (the per-loop audit + DEFER disposition + 4-children filing META), `t_84ea0218` (the per-loop promotion META parent), `t_682219d8` (the 2026-07-22 vault log truncation incident that motivated op-guard-11).
- **Companion children (per `t_3d2bdf98`):** `t_1d242aef` (coder, item 4 regression suite), `t_a2a03873` (verifier, item 5 compliance-verifier child), `t_87460a77` (jarvis, op-guard-29 HITL gate for operator disposition).

## Audit-line

```
## [2026-08-17T16:00Z] doc-writer-shipped — methodology/M-decide-skill-curator.md v0.1.0 written. Codifies: the skill-curator loop's contract (Part 2 — size budget + duplicate detection + protected skill bodies), inputs (Part 3 — skill manifests + metadata + files + yesterday's summary + protected-set policy + vault log state), outputs (Part 4 — 0-INBOX audit markdown + vault log audit-line + NO Discord DM), cadence (Part 5 — daily 04:00 cron + compliance-verifier child + operator review), node-type discipline (Part 6 — Scripts/Code for the scan/auto-split/auto-merge, Agents for the doc-writer dispatcher, Human for the operator review), failure modes (Part 7 — 7-row recovery table), onboarding (Part 8 — 8-step apply procedure), backwards compat (Part 9 — pre-existing instances grandfathered; protected-set policy is operator-policy not methodology invariant). Flips items 1 + 9 of the 9-item promotion rubric (M-decide-graph-readiness.md v0.1.1 Part 3); the post-write audit-score is ≥ 4/9. Companion: paired-wiki on 04a-decide-work-graph.md (one-line cross-reference to M-decide-skill-curator.md). Source: t_ab1f3081 + parent t_3d2bdf98 (DEFER disposition recommended; the loop is `live-pending-test` v0.1.1 and remains so until items 3, 4, 5 + 7-8 also pass).
```