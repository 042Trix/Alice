---
id: alice-methodology-M-decide-graph-readiness
created: 2026-08-14T14:30:00Z
updated: 2026-08-17T10:55:00Z
title: "Methodology M — Decide graph readiness (live vs live-pending-test, 9-item promotion criteria)"
type: methodology
status: draft
source: alice-framework
version: 0.1.1
tags: [kind:methodology, kind:graph-readiness, kind:loop-status, kind:meta, project:alice]
confidence: 0.0
links: ["[[methodology/04a-decide-work-graph.md]]", "[[methodology/04c-decide-master-ticket.md]]", "[[methodology/04d-decide-flow-spec.md]]", "[[methodology/05-op-guards.md]]", "[[methodology/M-decide-parity-check.md]]", "[[methodology/M-decide-spec-first-flow.md]]", "[[methodology/M-decide-instance-vs-framework.md]]", "[[methodology/M-decide-alice-publish-flow.md]]", "[[methodology/M-decide-graph-audit-promotion-flow.md]]", "[[templates/parity-check.md.template]]"]
promoted_from: _inbox/M-decide-graph-readiness.md
teaching-example: true
amended_by: ["[[ticket:t_20621854]]", "[[ticket:t_fe0ad797]]", "[[ticket:t_ab1f3081]]", "[[ticket:t_f38bd852]]"]
---

# Methodology M — Decide graph readiness

> A graph (loop or master-ticket flow) is "product-ready" only when it has cleared a documented promotion bar. The `live` vs `live-pending-test` status field in the loop registry is the durable surface; the 9-item promotion criteria below are the rubric the registry's status must satisfy. Without this methodology, the registry's status is heuristic — what looks `live` to one operator is `live-pending-test` to another, and the gap between "shipped" and "trusted" is invisible.

## Part 1: Why this methodology

Alice is methodology that other operators apply to their own setups. If Alice does not define what makes a loop "product-ready," then a friend reading Alice has no way to know whether to trust a `live` label they see in a loop registry, whether to wait for `live-pending-test` to clear, or what work remains between the two. The operator's current environment already has the gap: as of 2026-08-14, of 10 registered loops in `~/.hermes/loops/hermes.yaml`, only 1 (`alice-publish`) carries `status: live`; the other 9 carry `status: live-pending-test`. The status field exists; the rubric that justifies moving from one to the other does not.

This methodology fills the gap with four things:

1. **Semantics** — what `live` and `live-pending-test` actually mean (Part 2)
2. **Promotion criteria** — the 9 items that must be true before promotion (Part 3)
3. **Product-ready inventory** — what a well-defined graph carries (Part 4)
4. **Verification discipline** — how to actually test readiness (Part 5)

The methodology is **tool-agnostic**: any work-graph system with a status field can apply it. The canonical implementation lives in the Hermes loop registry (`~/.hermes/loops/hermes.yaml` + `~/.hermes/loops/intents/<loop-id>-intent.md`) and the per-loop regression suites (`~/.hermes/tests/test_<loop_id>_*.py`). The friend reading Alice later can apply the same 9 items to their own loop registry.

## Parity check

**Pattern this ticket describes:** Graphs (loops and master-ticket flows) carry a `live` / `live-pending-test` status with a 9-item promotion rubric; product-ready graphs have documented triad + test coverage + operational contract + discipline gates + node-type discipline.

**Evidence in operator's environment:**

- `~/.hermes/loops/hermes.yaml` already declares `status:` on every loop entry. Of 10 loops, 9 are `live-pending-test` and 1 (`alice-publish`) is `live`. The status field is real and load-bearing in the dispatcher; the semantics are NOT codified in Alice.
- `~/.hermes/loops/intents/` has 11 intent docs (one per loop + extras); every intent file carries a `version:` field that gets bumped on each `loop-updater` invocation. Item 2 of the promotion criteria is partly parity-supported.
- `~/.hermes/tests/` has `test_loops_preflight_profile_home.py` + `test_post_graph_retro*.py` + `test_x_article_review_loop_v080.py` — partial coverage, not per-loop. Item 4 is partially supported.
- `methodology/04a-decide-work-graph.md` covers work-graph state transitions (ready/running/done/blocked/archived). It does NOT cover the loop-level `live`/`live-pending-test` distinction. The gap this methodology fills is explicit.
- `methodology/04a-decide-work-graph.md` Part 4 (the 3-node-type taxonomy: Human / Scripts/Code / Agents) is the design-time discipline that item 9 of the promotion rubric codifies at promotion-time. Pre-v0.1.1 the taxonomy existed but was unenforced as a `live`-readiness gate.
- `methodology/M-decide-spec-first-flow.md` Part 2 already enumerates the verifier-step pattern (V-1, V-2, V-3) between phases. Items 5–6 reuse that pattern.

**Verdict:** partially-supported.

**If partially supported — which aspects are aspirational:**

- Items 1, 3, 6, 9: full parity (status field + node-type taxonomy both exist; the rubric codifies what justifies the status).
- Item 2: partial parity (intent docs exist; the `version:` discipline is enforced by `loop-updater` but not by an audit script).
- Item 4: partial parity (regression suites exist for some loops, not all).
- Item 5: partial parity (compliance-verifier children are filed per `M-decide-spec-first-flow.md` for *methodology* changes; the pattern is not yet the default for *loop* promotion).
- Items 7, 8: parity-supported (every loop has Discord DM history + `kanban show` history; the operator reviews runs ad-hoc).

**If aspirational — infrastructure gap:** N/A (partially supported, not aspirational).

**Follow-up tickets:**

- `t_20621854` (this ticket) — methodology doc ships.
- Companion: the bulk-promotion META ticket is filed in phase-3 per `methodology/M-decide-spec-first-flow.md` Part 2 (a non-doc-writer agent — coder or operator — files the 9-loop promotion META citing this doc as the canonical reference).

---

## Part 2: `live` vs `live-pending-test` semantics

### `live`

A graph is at `live` when ALL of the following hold:

- **Contract documented.** The methodology doc + intent doc + yaml entry + tests all agree on what the graph does. Reading any one of them gives the same answer to "what does this graph do and when does it succeed?"
- **Regression suite green.** All test cases pass on the current version. The test surface is in `~/.hermes/tests/test_<loop_id>_*.py` (or equivalent).
- **Production usage proven.** The graph has been used end-to-end at least once with operator-confirmed success (a Discord DM, a kanban comment, a vault audit-line, or a child-ticket handoff).
- **Paired-wiki integrity.** Per op-guard-5 (`2-ATOMIC/rules/op-guard-5-wiki-update-2026-07-21.md`): the methodology doc + intent doc + yaml entry + tests ship in the same change set.
- **Compliance-gate satisfied.** Per op-guard-17 (`2-ATOMIC/rules/op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md`): a verifier child confirms the instance conforms to the Alice doc. For a loop, this is a verifier ticket that runs the regression suite and confirms the dispatcher binding matches the contract.

### `live-pending-test`

A graph is at `live-pending-test` when ALL of the following hold:

- **Works in practice** but the formal verification is incomplete — the loop has been used but the regression suite is partial or the audit hasn't run.
- **Used by the operator** at least once (even informally) — `live-pending-test` is not "draft"; it means "actually works, just not yet proven to the standard of `live`."
- **Has at least one test case green** — a smoke test, a unit test, or a verified end-to-end run. The full regression suite is not required at `live-pending-test`; the loop is honest about what it's verified for.

A graph at `draft` (no production usage) is NOT a graph; it's a spec. Specs live on the `alice-framework` board as methodology docs; loops live on the operator's instance with status `live-pending-test` or `live`. The two states (spec vs loop) are distinct from the two loop states (`live-pending-test` vs `live`).

---

## Part 3: Promotion criteria (9-item table)

A loop is ready to promote from `live-pending-test` → `live` when ALL of the following are true. Each item maps to a verifiable artifact.

| # | Item | Required? | Source of truth |
|---|------|-----------|-----------------|
| 1 | Methodology doc exists + cross-references the loop | ✅ | `~/Documents/alice-framework/methodology/M-decide-<loop-name>.md` (or generic methodology like `04a-decide-work-graph.md`) with a `links:` field that names the loop id |
| 2 | Intent doc exists + `version:` field is bumped | ✅ | `~/.hermes/loops/intents/<loop-id>-intent.md` — frontmatter `version:` field tracks semver per `loop-updater` discipline |
| 3 | Loop yaml entry has `status: live` | ✅ | `~/.hermes/loops/hermes.yaml` — `loops[].status = "live"` |
| 4 | Regression suite exists + green | ✅ | `~/.hermes/tests/test_<loop_id>_*.py` (or `test_<loop_id>_<aspect>_*.py`); all test cases exit 0; a `pytest -q` run captures the green |
| 5 | Compliance-verifier child confirms instance conforms | ✅ | A verifier ticket (per op-guard-17) on the appropriate board cites this doc + the intent + the yaml + the tests, and transitions to `done` with `--result "compliance: pass"` |
| 6 | Paired-wiki integrity per op-guard-5 | ✅ | The same change set ships methodology doc + intent bump + yaml `status:` flip + regression suite updates; `kanban_complete` carries the file-list as evidence |
| 7 | Operator has reviewed at least one production run | ✅ | A Discord DM, kanban comment, or chat-transcript entry names the loop + a successful run; the operator's review is the trust signal |
| 8 | Loop has been used end-to-end at least once | ✅ | `kanban show <loop_run_card>` (or the per-run audit-line at `~/Documents/HermesVault/log.md`) shows a `loop-run — <loop-id> v<X.Y.Z> — result=success` entry |
| 9 | **Node-type discipline** — every node in the graph uses the appropriate type per `methodology/04a-decide-work-graph.md` Part 4 (Human / Scripts/Code / Agents — LLM-driven Agents for interpretation, Scripts/Code for determinism, Human for operator-owned decisions) + the rationale for each type choice is documented | ✅ | The graph's design doc names each node + its type + the rationale; the verifier profile reads the design doc + walks each node to confirm the type matches the work |

Items 1–6 are machine-verifiable (file exists, file content is correct, ticket exists, tests pass). Items 7–8 are operator-verified (the operator's eye on the run history). Item 9 is partially machine-verifiable (the design doc + the rationale line exist + are parseable) and partially operator-verified (the operator confirms the rationale is honest, not post-hoc rationalization). The split matters: items 1–6 + 9's machine portion can be filed by a verifier profile; items 7–8 + 9's operator portion require operator sign-off.

### Anti-pattern: claiming `live` without items 5, 6, or 9

The `alice-publish` loop was promoted to `live` on 2026-08-11 with items 1–4 + 6–8 satisfied. Items 5 (compliance-verifier child) and 9 (node-type discipline) are newer than the original promotion — op-guard-17 shipped 2026-08-08 (three days before the promotion); item 9 shipped 2026-08-15 (v0.1.1 of this methodology, four days after the promotion). For `alice-publish`, item 5 is backfilled by the 2026-08-15 retrospective verifier ticket; item 9 holds in practice — the publish flow uses Scripts/Code for git operations + verifier gates + minimal Human operator approval per Part 7.5, with no Agent nodes doing deterministic work. Future promotions MUST carry items 5 and 9; pre-2026-08-15 promotions of `live` loops are grandfathered unless they demonstrably violate item 9.

---

## Part 4: Minimum items for a product-ready graph

A "well-defined product-ready graph" carries the following inventory. The inventory is the **artifact list** that a new graph (or a freshly-promoted loop) must have on the day it's labeled `live`. Missing items block `live` until they're added or explicitly deferred with an aspirational-flag.

### A. Documentation triad (paired-wiki per op-guard-5)

- **Methodology doc** — the contract. Names what the graph does, when to use it, what the inputs/outputs are, what the failure modes are. Lives on `alice-framework` board at `~/Documents/alice-framework/methodology/`.
- **Intent doc** — the implementation. Names the `worker_prompt`, the dispatcher registration, the runtime cadence, the verifier hook. Lives at `~/.hermes/loops/intents/<loop-id>-intent.md`.
- **Yaml entry** — the dispatcher binding. Names the loop id, the `register_with_profile` + `run_with_profile`, the `target_board`, the `status`, the `context_as_budget`. Lives at `~/.hermes/loops/hermes.yaml`.

The triad is **atomic for promotion**: a change to any one of the three without the other two is a paired-wiki violation per op-guard-5. The change set carries all three files (plus the regression suite from §B).

### B. Test coverage (compliance-gate per op-guard-17)

- **Unit tests** — individual functions validated. E.g., `test_post_graph_retro_spec_drift.py` validates the spec-drift detector; `test_loops_preflight_profile_home.py` validates the preflight.
- **Integration tests** — end-to-end dispatcher + worker spawn + `kanban_complete` cycle. E.g., `test_x_article_review_loop_v080.py` validates the x-article-review loop end-to-end.
- **Regression suite** — golden cases from verified failure patterns. Each post-mortem (in `2-ATOMIC/post-mortems/`) that motivated a fix should map to a test case in the regression suite.
- **Verifiers** — child tickets that confirm the instance matches the contract. Per op-guard-17, the verifier child is the META's done-gate child.

### C. Operational contract

- **Run history preservation** — `run_history.preserve_done: true` + `run_history.never_archive_loop_runs: true` in the yaml. The loop's run history is durable; archiving it loses the audit trail.
- **Audit-line discipline** — every loop transition appends an audit-line to `~/Documents/HermesVault/log.md` via `vault_log.append_audit_line` per op-guard-11. The audit-line schema is in `run_history.audit_line_schema` on the yaml entry.
- **Operator-facing surface** — Discord DM, kanban comment, or vault copy. The loop's outcome is reachable from chat. The `deliver` field on the cron registration (or the equivalent for on-demand loops) names the surface.
- **Error handling** — `transient` failures retry with backoff; `hard-failure` failures transition to `blocked` for operator review. The protocol-violation retry (per op-guard-15) is the canonical pattern for worker-side failures.

### D. Discipline gates

- **Op-guard-19** (pre-verify artifact state before claiming done) — the verifier profile runs `verify_artifact_state.py` before `kanban_complete`.
- **Op-guard-21** (chat-side orchestrator-only, no direct edits) — the loop's environment mutations go through the worker protocol; the chat-side files tickets, doesn't edit files.
- **Op-guard-22** (canonical handoff write discipline) — when the loop ends a session, both the dated handoff and `session-handoff.md` get written atomically.
- **Node-type discipline** (Part 3 item 9 + Part 4 of `04a-decide-work-graph.md`) — every node uses its proper type (Human / Scripts/Code / Agents). A loop that has an Agent (LLM-driven) node doing work a script could do deterministically is not `live`-ready even if items 1–8 are all green; the item-9 gate violation is a release-blocker that supersedes items 1–8. The x-article-review rebuild (v0.9.7 → v0.9.36, 13 amendments) is the canonical example — each amendment made a previously-Agent-interpreted step a Scripts/Code path; the same discipline must hold forward.

The discipline gates are NOT loop-specific; they're cross-cutting. A loop that violates any of them in its regression suite is not `live`-ready even if items 1–9 are satisfied; the gate violation is a release-blocker.

---

## Part 5: Verification discipline

To verify a graph is `live`-ready (i.e., to run the item-5 compliance-verifier child), the verifier profile walks the loop through 8 checks. Each check maps to a subset of the 9 promotion items.

1. **Run the regression suite.** `pytest -q ~/.hermes/tests/test_<loop_id>_*.py`. All cases exit 0. (Items 4.)
2. **Trigger the loop via dispatcher.** File a master ticket on the loop's `target_board` (or fire the cron if `trigger.kind: cron`). Verify a child ticket lands on the right board with the right body. (Item 8.)
3. **Verify the master ticket's children are filed correctly.** Parent edges, bodies, gates, dispatch profiles all match the contract in the intent doc. (Items 1, 2, 3.)
4. **Verify the worker spawn + run + complete cycle.** A worker profile claims the ticket, runs the worker_prompt, and transitions to `done` via `kanban_complete` with structured handoff. (Items 4, 5.)
5. **Verify the audit-line discipline.** Every transition (`claim`, `complete`, `block`) appends a line to `~/Documents/HermesVault/log.md` per the schema in `run_history.audit_line_schema`. (Operational contract §C.)
6. **Verify the operator-facing surface.** The Discord DM, kanban comment, or vault copy carries the loop's outcome to the operator's chat. (Operational contract §C.)
7. **Verify failure paths.** Inject a transient failure (e.g., a missing file the loop expects) and confirm the loop retries. Inject a hard failure (e.g., a malformed yaml entry) and confirm the loop transitions to `blocked`. (Operational contract §C.)
8. **Verify node-type discipline (NEW).** Walk the graph's design doc (the methodology doc + the intent doc). For each node, confirm:
   - The node's type matches the work it does (Agents only when interpretation is needed, Scripts/Code when determinism is possible, Human only for operator decisions — per Part 4 of `methodology/04a-decide-work-graph.md`).
   - The rationale for the type choice is documented (one line per node, why this type and not another).
   - Under failure injection, the node behaves as expected for its type: Scripts/Code nodes fail loudly with a deterministic error message; Agents produce bounded output that the next validator enforces; Human nodes gate a decision the agent cannot make on the operator's behalf. (Item 9 + Discipline gate §D.)

If a node fails check 8, the verifier does NOT pass the compliance-verifier child. The child transitions to `done` with `--result "compliance: fail — node-type-discipline violation on <node-id>, expected type=<X>, actual type=<Y>, rationale missing or post-hoc"`. The META ticket cannot promote the loop; the design doc must be amended, the rationale rewritten, and check 8 re-run.

If all 8 checks pass, the verifier transitions the compliance-verifier child to `done` with `--result "compliance: pass"`. The META ticket can then promote the loop to `live` by flipping the yaml `status:` field + bumping the intent doc `version:` field + appending an audit-line to `~/Documents/HermesVault/log.md`.

---

## Part 6: How to apply this methodology

When a new loop is added to the operator's environment:

1. **File the spec-first-flow methodology doc** on `alice-framework` (this methodology provides the rubric; the loop's specific doc is filed per `M-decide-spec-first-flow.md` Part 2).
2. **File the intent doc + yaml entry** on the operator's instance (per the loop-updater loop's `worker_prompt` and the canonical `register_with_profile` / `run_with_profile` discipline). Initial status: `live-pending-test`.
3. **File the regression suite** at `~/.hermes/tests/test_<loop_id>_*.py` with at least one test case green. (Items 4, 8.)
4. **Trigger the loop at least once** to produce a run history entry. The run can be operator-driven or cron-driven. (Item 8.)
5. **Wait for the operator to review the run** (Discord DM, kanban comment, or chat). (Item 7.)
6. **File the bulk-promotion META** when items 1–9 are all green. The META's compliance-verifier child (per op-guard-17) runs the 8 checks from Part 5, including check 8 (node-type discipline).
7. **On compliance-verifier pass, flip the yaml `status:` field** to `live` and bump the intent doc `version:` field per semver. Append an audit-line. The loop is now `live`.

A loop that has been at `live-pending-test` for 30+ days without promotion is a signal that either (a) the loop is not worth the work to promote (consider archiving per `04a-decide-work-graph.md` Part 7 — work graph node retirement) or (b) the loop's contract has drifted (re-run items 1, 3, 6).

---

## Part 7: Backwards compatibility

This methodology applies forward from 2026-08-14 (initial ship). The v0.1.1 amendment on 2026-08-15 added item 9 + check 8 (node-type discipline). The 10 existing loops in `~/.hermes/loops/hermes.yaml` carry one of three statuses:

- **`live`** (`alice-publish`) — grandfathered. The original promotion (2026-08-11) was done per the operator's ad-hoc judgment at the time; items 7–8 were satisfied retroactively. Item 5 (compliance-verifier child) is backfilled by the 2026-08-15 retrospective verifier ticket (op-guard-17 was younger than the original promotion). Item 9 holds in practice: the publish flow uses Scripts/Code for git operations + verifier gates + minimal Human operator approval per Part 7.5, with no Agent nodes doing deterministic work. No re-promotion required.
- **`live-pending-test`** (9 loops) — eligible for promotion per the bulk-promotion META. The META is filed in phase-3 per `M-decide-spec-first-flow.md` Part 2; the doc-writer does NOT file it (op-guard-16 + op-guard-21 — doc-writer is spec-only). Pre-v0.1.1 promotions were eligible under items 1–8; post-v0.1.1 promotions MUST satisfy all 9 items including item 9 (node-type discipline). The bulk-promotion META's compliance-verifier child MUST run all 8 checks from Part 5, including check 8; an `alice-publish`-style retroactive verifier does NOT suffice — every loop gets the full v0.1.1 rubric.
- **Missing `status:` field** — N/A. Every loop in the registry as of 2026-08-15 has a `status:` field; this methodology does not address pre-status loops because there are none.

The `draft` status (used for unfinished specs on the `alice-framework` board) is a methodology-board status; it is not a loop-registry status. The two statuses are independent. A loop at `live` can still have an unfinished methodology doc (rare but legal — the doc is the contract, not the loop's runtime status). A loop at `live-pending-test` MUST have a finished intent doc + yaml entry + at least one test case green; it does NOT need a finished Alice methodology doc.

---

## Part 8: Cross-references

- **`methodology/04a-decide-work-graph.md`** — the work-graph state machine (`ready` / `running` / `done` / `blocked` / `archived`). This methodology is a layer above: it governs the **graph-level** lifecycle (`draft` → `live-pending-test` → `live`), not the **work-item** lifecycle. **Part 4 of that doc (the 3-node-type taxonomy: Human / Scripts/Code / Agents) is the design-time discipline that item 9 of this methodology's promotion rubric codifies at promotion-time.**
- **`methodology/04c-decide-master-ticket.md`** — the master-ticket pattern. Master tickets are a kind of graph; their promotion follows the same 9 items.
- **`methodology/04d-decide-flow-spec.md`** — the flow-spec discipline. Flows are graphs; their promotion follows the same 9 items.
- **`methodology/M-decide-parity-check.md`** — the parity-check rule. Every ticket on `alice-framework` carries a parity check; this methodology's promotion criteria are the parity-check verdict for a loop's `live`-readiness.
- **`methodology/M-decide-spec-first-flow.md`** — the 4-phase flow + verifier steps. Part 2's phase-3 ("update environment") is where the bulk-promotion META lives.
- **`methodology/M-decide-instance-vs-framework.md`** — the framework-vs-instance distinction. The 9-item rubric is framework-side; the per-loop promotion META is instance-side.
- **`2-ATOMIC/rules/op-guard-5-wiki-update-2026-07-21.md`** — paired-wiki integrity. Promotion item 6.
- **`2-ATOMIC/rules/op-guard-11-log-md-append-only-2026-07-22.md`** — vault log append-only. Every transition appends an audit-line.
- **`2-ATOMIC/rules/op-guard-15-must-call-terminal-2026-07-29.md`** — workers must call terminal. Verification discipline §C.
- **`2-ATOMIC/rules/op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md`** — alice-first / instance-second / compliance-gate. Promotion item 5.
- **`2-ATOMIC/rules/op-guard-19-pre-verify-artifact-state-2026-08-08.md`** — pre-verify artifact state. Verification discipline §D.
- **`2-ATOMIC/rules/op-guard-21-chat-side-orchestrator-only-no-direct-edit-2026-08-09.md`** — chat-side orchestrator-only. The doc-writer files the methodology doc; the coder files the bulk-promotion META.
- **`2-ATOMIC/rules/op-guard-22-canonical-handoff-write-discipline-2026-08-09.md`** — canonical handoff write. Operational contract §C.
- **`templates/parity-check.md.template`** — the parity-check template; this methodology's Part 1's parity section follows the template's shape.
- **`methodology/M-decide-graph-audit-promotion-flow.md`** (v0.1.0, t_3b49e1bc) — the canonical flow spec that operationalizes this rubric on the operator's `loop-builder` board. The flow fires the 4-stage HITL gates (per op-guard-32) for each registered loop and takes per-loop action (PROMOTE / DEFER / ARCHIVE) per the operator's disposition. Item 8 of the rubric (end-to-end run) is the flow's HITL #2 confirmation; item 5 (compliance-verifier child) is the flow's Compliance-verifier node; the audit-line discipline (per op-guard-11) is the flow's audit-line protocol.

---

## Audit-line

`## [2026-08-14T14:30Z] doc-writer-shipped — methodology/M-decide-graph-readiness.md v0.1.0 written. Codifies live vs live-pending-test semantics (Part 2), 8-item promotion criteria (Part 3), product-ready inventory A-D (Part 4), 7-check verification discipline (Part 5), application procedure (Part 6), backwards compat (Part 7), cross-references (Part 8). Parity-check verdict: partially-supported (status field real in ~/.hermes/loops/hermes.yaml; rubric new in Alice). Source: t_20621854 (operator quote 2026-08-14: "Are you saying Alice does not include the live vs draft status for graphs? Does it specify what items must be in place for a well defined product ready graph?").

`## [2026-08-15T15:00Z] doc-writer-shipped — methodology/M-decide-graph-readiness.md v0.1.1 (item 9 + check 8 amendment). Adds node-type discipline as item 9 of the promotion rubric (Part 3) and as check 8 of the verification discipline (Part 5). Every node in a graph at `live` MUST use its proper type per `methodology/04a-decide-work-graph.md` Part 4 (Human / Scripts/Code / Agents) + the rationale for each choice documented. The discipline gate is codified in §D (Discipline gates). Part 1's 4-bullet list updated (8 → 9 items); Part 3 caption + table extended; Part 4 §D extended; Part 5 caption + checks extended (7 → 8 checks); Part 6 application procedure updated (verify all 9 items, run all 8 checks); Part 7 backwards-compat notes grandfathered `alice-publish` under item 9 (publish flow uses Scripts/Code + verifier gates + minimal Human operator approval, no Agent nodes doing deterministic work) and explicitly says the 9 live-pending-test loops must satisfy the v0.1.1 rubric before bulk-promotion. Parity-check verdict refreshed: items 1, 3, 6, 9 full parity. Source: t_fe0ad797 (operator quote 2026-08-15: "I'm inclined to say Alice should not recommend/allow an improperly build graph to be production ready. Too much?").`