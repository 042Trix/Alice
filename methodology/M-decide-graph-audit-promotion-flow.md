---
id: alice-methodology-M-decide-graph-audit-promotion-flow
created: 2026-08-17T13:25:00Z
updated: 2026-08-26T16:05Z
title: "Methodology M — Decide the graph-audit-promotion flow (the canonical 2-HITL audit + promotion cycle for the operator's loop registry)"
type: methodology
status: draft
source: alice-framework
version: 0.9.4
amended_by_v0_9_4: t_5e1a285c-doc-writer-audit-run-schema
amended_by_v0_9_4_source: 'doc-writer lane amend-125 (parent Retro-A t_ce82ce87, master t_db5cf6da, V-of-V FAIL t_fac38a2d, fix ticket t_5e1a285c). Codifies the canonical `## Audit run` schema for the per-loop audit ticket body: the doc-writer lane MUST commit a `## Audit run` section to the per-loop audit ticket body regardless of `loop_live_audit.py` exit_code (including `aborted` runs where the tool emits `unknown loop-id ''<X>''`); the schema carries verbatim stdout/stderr + a 9-item scoring table populated from the output (item 1 = FAIL on `unknown loop-id`, items 2-9 INHERIT from abort context); `aborted` is a valid result, NOT replaced by a "no score" line. The amend-125 surfaces the verifier-chain invariant: a verifier MUST be able to read the `## Audit run` section from the per-loop audit ticket body and grade it deterministically. Prior patterns posted the audit output as a `kanban_comment` on the master; the verifier chain (V-1..V-of-V per Part 3) reads the per-loop audit ticket body, NOT the master''s comment thread, so the comment-only pattern produced FAIL on identical evidence 5x (per Retro-A `t_ce82ce87` re-anchor + V-of-V `t_fac38a2d`). The amend-125 codifies the schema that breaks the FAIL-loop. Companion intent doc: `~/.hermes/loops/intents/graph-audit-promotion-intent.md` v0.9.4 (paired-wiki mirror per op-guard-5 — ships via coder follow-up per op-guard-16 spec-first-flow + op-guard-17 alice-first/instance-second/compliance-gate). Companion concept note: `~/Documents/HermesVault/2-ATOMIC/concepts/graph-audit-promotion-doc-writer-audit-run-schema-v0-9-4-2026-08-26.md` (NEW; ships via this ticket). Companion env patch: `~/.hermes/profiles/doc-writer/SOUL.md` + `~/.hermes/skills/autonomous-ai-agents/jarvis-coordinator/SKILL.md` doc-writer ticket body template patched to require the `## Audit run` section per the amend-125 schema — ships via coder follow-up per op-guard-16. Companion tool patch: `~/.hermes/tools/loop_live_audit.py` v0.2.0 — emit JSON envelope `{exit_code, error, items, score}` on errors so the doc-writer lane has a structured abort output to map to the amend-125 stderr → per-item FAIL mapping table; ships via coder follow-up.'
amended_by_v0_9_4_companion_intent_doc: '~/.hermes/loops/intents/graph-audit-promotion-intent.md v0.9.4 (paired-wiki mirror per op-guard-5; v0.9.4 reflects the amend-125 audit-run schema)'
amended_by_v0_9_4_companion_concept_note: '~/Documents/HermesVault/2-ATOMIC/concepts/graph-audit-promotion-doc-writer-audit-run-schema-v0-9-4-2026-08-26.md (NEW; the canonical concept note for amend-125)'
amended_by_v0_9_4_companion_env_patch: '~/.hermes/profiles/doc-writer/SOUL.md + ~/.hermes/skills/autonomous-ai-agents/jarvis-coordinator/SKILL.md (doc-writer ticket body template; ships via coder follow-up per op-guard-16)'
amended_by_v0_9_4_companion_tool_patch: '~/.hermes/tools/loop_live_audit.py v0.2.0 (emit JSON envelope on abort; ships via coder follow-up)'
amended_by_v0_9_3: t_f66a7b5e-verifier-chain-parent-edge-gate
amended_by_v0_9_3_source: 'per Retro-A re-anchor (t_ce82ce87) + V-of-V FAIL finding (t_fac38a2d) + op-guard-32 v3 gate-order enforcement layer. amend-124: the verifier-chain parent-edge gate. The dispatcher's parent-edge gate previously fired on `tasks.status = done` alone, but verifier-chain members emit `result = "compliance: fail"` while still `status = done`. The V-chain gate now requires `parent.status == done AND parent.result starts_with "compliance: pass"`; on FAIL, the child blocks on `kind=dependency` with the canonical reason `gate-order violation: V-(N-1) emitted <result>; verifier chain requires compliance: pass`. Retro-A inherits the same gate (parent V-of-V must emit `compliance: pass`); Retro-H inherits a sibling gate (parent Retro-A must complete normally via `closed_per_decision`, NOT `closed_in_error`). The new Field 5b captures the canonical contract; the companion dispatcher patch + 4-case regression suite at `~/.hermes/tests/test_dispatcher_verifier_chain_gate.py` v0.1.0 ship via coder follow-up per op-guard-16 spec-first-flow + op-guard-17 alice-first/instance-second/compliance-gate. Source ticket: t_f66a7b5e. Companion intent doc: `~/.hermes/loops/intents/graph-audit-promotion-intent.md` v0.9.3 (paired-wiki mirror per op-guard-5). Companion concept note: `~/Documents/HermesVault/2-ATOMIC/concepts/graph-audit-promotion-verifier-chain-parent-edge-gate-v0-9-3-2026-08-26.md`.'
amended_by_v0_9_2: t_cfe657f4-dryrun-placeholder-contract
amended_by_v0_9_2_source: 'doc-writer per_loop_audit child (operator proxy via op-guard-28 fallback 2026-08-26T15:38Z, ticket t_cfe657f4) — amend-123: the dry-run target-loop must be registered before the audit fires. The DRY-RUN master ticket `t_db5cf6da` validated the dispatcher + script + middleware end-to-end by attempting the per-loop audit on `graph-audit-dry-run-test-loop`; the audit tool exited 1 with `# error: unknown loop-id ''graph-audit-dry-run-test-loop''` (the loop was never registered in `~/.hermes/loops/hermes.yaml`). The fix codifies 3 changes: (1) **target-loop pre-flight check** — the canonical child-creation helper `~/.hermes/scripts/file_graph_audit_children.py` MUST pre-flight-verify the target loop is registered in `~/.hermes/loops/hermes.yaml` BEFORE dispatching `per_loop_audit` children; on missing registration, the helper returns rc=2 with a clear `kind=needs_input` reason referencing the dry-run placeholder contract (this amend-123); (2) **dry-run placeholder-loop canonical contract** — dry-run fake loops MAY be registered at `status: live-pending-test` with a tag `kind:dry-run-placeholder` (front-matter tag) + `register_with_profile: doc-writer` + `run_with_profile: doc-writer` + `target_board: loop-builder` + a minimal `worker_prompt` clarifying the loop is not for production use; archive-after-master-close is the canonical termination path; (3) **paired placeholder intent doc** — every dry-run placeholder loop MUST ship a paired `~/.hermes/loops/intents/<loop-id>-intent.md` documenting the fixture contract (1-page stub: goal + scope + stop-condition + archive-after-master-close); the doc MUST be a stub (no Phase-V-HITL-Retro structure) since the loop never fires end-to-end. Companion intent doc: `~/.hermes/loops/intents/graph-audit-promotion-intent.md` v0.9.2 (paired-wiki mirror per op-guard-5). Companion concept note: `~/Documents/HermesVault/2-ATOMIC/concepts/graph-audit-promotion-dry-run-placeholder-contract-v0-9-2-2026-08-26.md`. Companion helper + test patches ship via coder follow-up per op-guard-16 spec-first-flow + op-guard-21 chat-side orchestrator-only (the doc-writer ships the doc-side contract; the coder ships the env mutation per the doc-side contract).'
amended_by_v0_9_2_companion_intent_doc: '~/.hermes/loops/intents/graph-audit-promotion-intent.md v0.9.2 (paired-wiki mirror per op-guard-5; v0.9.2 reflects the dry-run placeholder-loop contract)'
amended_by_v0_9_1: t_d132ad31
amended_by_v0_9_1_source: 'operator 2026-08-26 (chat, verbatim): "Since we have a graph for updating graphs, we should be using that to fix the audit graph instead of you filling tickets directly for agents to work." — amend-122: the dispatcher-integration fix. 6 invariants codified: (1) single source of truth for the path column (write-time backfill recovers body annotation when --path flag omitted); (2) body annotation parser is whitespace-tolerant + trailing-suffix recovery (handles `path: graph-audit-promotion-v6-amend-117` → `graph-audit-promotion`); (3) post-create hook fires iff canonical_path resolves to a registered loop id; (4) LLM worker_prompt is a fallback, NOT a peer (fires only when hook is bypassed or reports hard-fail); (5) compliance-verifier child verifies the dispatcher fired within 10 seconds of master creation; (6) audit-line per op-guard-11 captures the dispatch outcome. Companion intent doc: `~/.hermes/loops/intents/graph-audit-promotion-intent.md` v0.9.1 (paired-wiki mirror per op-guard-5). Companion dispatcher patch + 2 regression suites (`test_extract_flow_path_normalization.py` 6 cases + `test_graph_audit_dispatcher_fires.py` 4 cases) ship via coder follow-up per op-guard-16 spec-first-flow + op-guard-21 chat-side orchestrator-only. Companion concept note: `~/Documents/HermesVault/2-ATOMIC/concepts/graph-audit-promotion-dispatcher-integration-v0-9-1-2026-08-26.md`.'
amended_by_v0_9_0_source: 'operator 2026-08-23 (chat, verbatim): "this is still LLMs deciding what tickets to make. Since the graph is defined, we should create all tasks programatically" — amend-102: the canonical deterministic child-creation contract; the 8 children + the parent-edge chain + the lane assignments + the bodies are all derived from the canonical contract (no LLM); the chat-side files 1 master ticket + the canonical helper `~/.hermes/scripts/file_graph_audit_children.py` v0.1.0 does the rest (idempotent via the canonical `--idempotency-key` shape `graph-audit-promotion/<master_id>/<kind>`). The helper is the analog of `~/.hermes/tools/file_x_article.py v0.9.15 _dispatch_production_children` — both are file-time-deterministic 8-children dispatch helpers; the dispatcher / chat-side LLM has NO role in ticket structure + gate sequence + lane assignments (those are read from the contract, not improvised).'
amended_by_v0_1_0: t_3b49e1bc
amended_by_v0_5_1: t_6ccf4e4b
amended_by_v0_5_1_source: 'operator 2026-08-19 (chat, verbatim) "if the verifier fails, it needs to send the failure back to the doc-writer to address the issue" — amend-88: verifier FAIL routes to original-profile via auto-filed [Fix] child ticket; HITL #2 still BLOCKED until verifier PASS after the fix'
amended_by_v0_2_0: t_0205edd9
amended_by_v0_2_0_source: 'operator 2026-08-18 corrections: (a) 1 graph per run (not all graphs), (b) verifier profile = Compliance-verifier node, (c) HITL #3 = verifier (not operator) — amend-77'
amended_by_v0_2_0_companion_intent_doc: '~/.hermes/loops/intents/graph-audit-promotion-intent.md v0.3.0 (parallel 3 corrections; methodology twin per op-guard-5 paired-wiki integrity)'
amended_by_v0_3_0: t_5ed69888
amended_by_v0_3_0_source: 'operator 2026-08-18: "these changes you are making, are they being applied to the graph audit? We would want these improvements to happen when we run the flow, not just for this specific run" — amend-82 applies the op-guard-32 v4 amend (3-stage HITL pattern) to the actual flow'
amended_by_v0_3_0_companion_intent_doc: '~/.hermes/loops/intents/graph-audit-promotion-intent.md v0.4.0 (paired-wiki mirror per op-guard-5; v0.4.0 reflects the 3-stage pattern)'
amended_by_v0_4_0: t_6621d4d3
amended_by_v0_4_0_source: 'operator 2026-08-18 (chat, verbatim, 3 errors in the Mermaid diagram): (a) "3-stage HITL gates" → "2 HITL gates (HITL #1 + HITL #2)", (b) HITL #4 → HITL #2 renumbering, (c) Rewrite connected to HITL #1 feedback flow — amend-85'
amended_by_v0_4_0_companion_intent_doc: '~/.hermes/loops/intents/graph-audit-promotion-intent.md v0.4.1 (paired-wiki mirror per op-guard-5; v0.4.1 reflects the 2-HITL pattern with verifier as the unnumbered gate between HITL #1 and HITL #2)'
amended_by_v0_5_0: t_3d2a6f70
amended_by_v0_5_0_source: 'operator 2026-08-19 (chat, verbatim) "I approve the final HITL gate for the Audit Graph" — amend-87 marks the audit cycle complete; the certification stamp NOT a contract change'
amended_by_v0_5_0_companion_intent_doc: '~/.hermes/loops/intents/graph-audit-promotion-intent.md v0.5.0 (paired-wiki mirror per op-guard-5; v0.5.0 reflects the audit-cycle-complete marker)'
amended_by_v0_6_0: t_a6701e2b
amended_by_v0_6_0_source: 'operator 2026-08-19 (chat, verbatim) "I am not going to review a manually created PDF. It needs to be done by the flow itself. If the flow is incorrect, fix the flow" — amend-91: flow owns the canonical PDF generation step; master-ticket summary PDF auto-generated + auto-delivered to operator-DM when HITL #2 (Disposition) is ready to fire; chat-side fires clarify referencing the PDF as evidence (not the prior "chat-side manually creates the PDF" pattern that violated op-guard-21)'
amended_by_v0_6_0_companion_intent_doc: '~/.hermes/loops/intents/graph-audit-promotion-intent.md v0.6.0 (paired-wiki mirror per op-guard-5; v0.6.0 reflects the canonical PDF generation step at HITL #2 time)'
amended_by_v0_6_0_companion_helper: '~/.hermes/tools/post_graph_audit_disposition_pdf.py v0.6.0 (NEW, modeled after post_council_verdict_pdf.py + post_executive_report_pdf.py; the canonical PDF generation + Discord delivery + vault copy + audit-line pipeline)'
amended_by_v0_6_0_companion_test: '~/.hermes/tests/test_graph_audit_disposition_pdf.py v0.6.0 (NEW, 8-case regression suite: PDF generated + comment posted + Mermaid renders + idempotency + fail-closed + audit-line + slug derivation + walk-mode discovery)'
amended_by_v0_6_1: t_9a0c1b88
amended_by_v0_6_1_source: 'operator 2026-08-19 (chat, verbatim) "The PDF is incorrect. The mermaid shows a single box that says ''Target Loop: ...''. The entire contents of the HITL2 PDF need to be about the graph that is being audited, not about the audit graph self. Need a durable solution" — amend-92: dynamic Mermaid generation from target loop intent doc. The v0.6.0 extractor was a single regex (``mermaid`` block in the intent doc); it fell back to a labeled placeholder ("Target loop: <loop-id> — no Mermaid in intent doc") when the intent doc structured the flow as prose (e.g. x-article-review uses `### Phase 0..4` + `### V-N` + `### Post-council Retro-A + Retro-H spawn` headings — no Mermaid block). The v0.6.1 dynamic extractor reads the intent doc''s prose structure and renders a `graph TD` Mermaid diagram from the discovered Phases (H2-H4 `Phase N` headings, with anti-patterns/input-set/stop-condition/implementation sub-sub-headings filtered out by the reject-prefix list) + Verifiers (H2-H3 `V-N` headings + inline `V-1 + V-2 + ... + V-of-V` mentions) + HITL gates (inline `HITL-X` mentions) + Retro gates (inline `Retro-X` mentions). The diagram reflects the actual target loop''s flow. Per-loop author never has to write a Mermaid block by hand; loops with hand-authored Mermaid blocks still preserve the v0.6.0 behavior (use the hand-authored block verbatim). Mermaid syntax rules per `~/.hermes/skills/productivity/executive-reporting/references/mermaid-syntax-rules.md` are enforced via `_sanitize_mermaid_label` (`-->` → `→`, `\\n` → `<br/>`, backtick + double-quote escaping, whitespace collapse). Companion helper: `~/.hermes/tools/post_graph_audit_disposition_pdf.py` v0.6.1. Companion test suite: `~/.hermes/tests/test_graph_audit_disposition_pdf.py` v0.1.1 (4 new regression cases: dynamic extraction from x-article-review prose + placeholder when no structure + hand-authored Mermaid preserved + sanitizer + structure extraction) — 12 cases total (8 prior + 4 new)'
amended_by_v0_6_1_companion_intent_doc: '~/.hermes/loops/intents/graph-audit-promotion-intent.md v0.7.0 (paired-wiki mirror per op-guard-5; v0.7.0 reflects the dynamic Mermaid generation contract)'
amended_by_v0_6_1_companion_helper: '~/.hermes/tools/post_graph_audit_disposition_pdf.py v0.6.1 (the v0.6.0 helper is replaced; the dynamic extractor lives alongside the v0.6.0 PDF generation pipeline)'
amended_by_v0_6_1_companion_test: '~/.hermes/tests/test_graph_audit_disposition_pdf.py v0.1.1 (4 new regression cases: dynamic extraction from x-article-review prose + placeholder when no structure + hand-authored Mermaid preserved + sanitizer + structure extraction) — 12 cases total (8 prior + 4 new)'
amended_by_v0_8_0: t_2a6e1656
amended_by_v0_8_0_source: 'operator 2026-08-19 (chat, verbatim, 3 issues with the flow-generated Mermaid diagram for x-article-review): (1) "Each box should show the agent profile, the action they perform, the pass/fail/error edges. Right now it is missing the required details." (2) "The verifiers are not in the critical path and should be" (3) "The master ticket closes once the council delivers their output. I thought we had the master stay open until the retro items were delivered or dispositioned" — amend-93: methodology-doc-derived Mermaid extraction with profile+action boxes (issue 1), verifiers in critical path with PASS/FAIL edges (issue 2, FAIL routes to producer-as-fix-target per v0.9.33 amend-42), and master-close terminal node at end of Retro-H + corrective-B (issue 3, the canonical 10-condition done-gate per v0.9.0 amend-8 + Part 7.5). The v0.6.1 dynamic extractor was reading the intent doc prose; the v0.8.0 extractor reads the methodology doc (the canonical contract per op-guard-17 alice-first/instance-second) which carries the canonical **Assignee:** `<profile>` and **Deliverable:** <action> lines per Phase + the Verifier/Round/HITL/Retro structure. Each Mermaid box now shows `[profile]<br/>action` (the agent profile + the deliverable action) instead of just the phase name; verifiers are in the critical path with PASS → next-phase edges and FAIL → producer-as-fix-target edges; the MasterClose terminal node sits at the end of the Retro-H + corrective-B chain (NOT at Phase 5). The helper applies the new Mermaid structure to all 10+ loops in the registry; loops with hand-authored Mermaid blocks preserve the v0.6.0 behavior (verbatim).'
amended_by_v0_8_0_companion_intent_doc: '~/.hermes/loops/intents/graph-audit-promotion-intent.md v0.8.0 (paired-wiki mirror per op-guard-5; v0.8.0 reflects the methodology-doc-derived Mermaid extraction with profile+action + critical-path verifiers + master-close terminal node)'
amended_by_v0_8_0_companion_helper: '~/.hermes/tools/post_graph_audit_disposition_pdf.py v0.6.2 (the v0.6.1 dynamic extractor is replaced; the methodology-doc-derived extractor lives alongside the v0.6.0 PDF generation pipeline)'
amended_by_v0_8_0_companion_test: '~/.hermes/tests/test_graph_audit_disposition_pdf.py v0.1.2 (5 new regression cases: methodology-doc profile+action boxes + verifiers in critical path + master-close terminal node + master_close flag detection + Assignee/Deliverable delimiter shapes) — 22 cases total (12 prior + 5 new amend-92 + 5 new amend-93)'
alice-ticket: "[[ticket:t_3b49e1bc]]"
companion: ["[[methodology/04d-decide-flow-spec.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[methodology/04c-decide-master-ticket.md]]", "[[methodology/M-decide-graph-readiness.md]]", "[[methodology/M-decide-spec-first-flow.md]]", "[[methodology/M-decide-instance-vs-framework.md]]"]
links: ["[[methodology/04d-decide-flow-spec.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[methodology/04c-decide-master-ticket.md]]", "[[methodology/M-decide-graph-readiness.md]]", "[[methodology/M-decide-spec-first-flow.md]]", "[[methodology/M-decide-instance-vs-framework.md]]", "[[2-ATOMIC/rules/op-guard-29-per-loop-hitl-gate-2026-08-15.md]]", "[[2-ATOMIC/rules/op-guard-30-promotion-requires-evidence-or-exemptions-2026-08-17.md]]", "[[2-ATOMIC/rules/op-guard-31-three-stage-graph-hitl-2026-07-20.md]]", "[[2-ATOMIC/rules/op-guard-32-four-stage-graph-hitl-2026-08-17.md]]", "[[2-ATOMIC/rules/op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md]]", "[[2-ATOMIC/rules/op-guard-11-log-md-append-only-2026-07-22.md]]", "[[2-ATOMIC/rules/op-guard-21-chat-side-orchestrator-only-no-direct-edit-2026-08-09.md]]", "[[2-ATOMIC/rules/op-guard-28-clarify-before-block-needs-input-2026-08-15.md]]"]
teaching-example: true
---

# Methodology M — Decide the graph-audit-promotion flow

> The `graph-audit-promotion` flow runs the per-loop audit (per `M-decide-graph-readiness.md` v0.1.1 9-item rubric) on **a SINGLE target loop** specified by the operator in the master ticket body. The flow is **NOT** a "process all registered loops" pass; if 10 graphs exist in `~/.hermes/loops/hermes.yaml`, the operator runs the audit 10 times — once per graph. Each invocation fires the canonical **2 HITL gates (HITL #1 scope/intent → HITL #2 final disposition)** with the verifier's compliance check as the automated gate between them — for the single target loop, taking per-loop action (PROMOTE / DEFER / ARCHIVE) per the operator's HITL #2 disposition. The flow is the canonical surface for the operator's loop-promotion cycle. Without this flow, the audit is ad-hoc (the proactive board unblocker cron surfaces scores but no operator-decision path handles the per-loop action), and the `live` vs `live-pending-test` distinction is a heuristic rather than a tracked promotion cycle. *(v0.2.0, amend-77: 1 graph per run; verifier profile = Compliance-verifier node. v0.3.0, amend-82: 3-stage HITL pattern per op-guard-32 v4. v0.4.0, amend-85: simplifies to 2 HITL gates per operator 2026-08-18 — verifier is the unnumbered automated gate between HITL #1 and HITL #2; Rewrite is fed from the HITL #1 feedback flow.)*

## Source

Operator 2026-08-17 (chat, verbatim): *"we have a loop builder board. Let's follow our own spec and create a flow on the board which will do the audits and promotion"*

The operator's environment has a `loop-builder` board (canonical for loop registration per `M-decide-loop-catalog.md`) but no flow that does the per-loop audit + promotion work on it. The proactive board unblocker cron (`t_7f1cf929`) surfaces per-loop scores but the operator disposition (HITL #1 + HITL #2) and per-loop action (filing the disposition ticket, flipping yaml, bumping intent) is not wired. The flow codifies that wiring.

### v0.3.0 amendment source (amend-82)

Operator 2026-08-18 (chat, verbatim): *"these changes you are making, are they being applied to the graph audit? We would want these improvements to happen when we run the flow, not just for this specific run"*

The prior v0.4.0 rule update (op-guard-32 v4 amend; source: t_4283ebbd) dropped HITL #2 from the 4-stage HITL pattern, simplifying to 3 stages (HITL #1 + HITL #3 + HITL #4). The rule update landed but the intent doc + methodology doc still described the 4-stage pattern. Future invocations of the flow would have used the v0.3.0 4-stage pattern, not the operator's intended 3-stage pattern. The v0.3.0 amend-82 of this methodology doc applies the 3-stage pattern to the actual flow.

### v0.4.0 amendment source (amend-85, 2026-08-19)

Operator 2026-08-18 (chat, verbatim, 3 errors in the master-ticket summary PDF Mermaid diagram):

1. *"Fires the canonical 3-stage HITL gates (HITL #1 scope/intent → HITL #3 compliance → HITL #4 disposition) — There are 2 HITL gates, not 3. We should also stop referring to this final HITL gate as HITL4, it is HITL2."*
2. *"The diagram shows Operator -> HITL4 -> Verifier Agent Node HITL3 -> HITL3 Pass/Fail. This is incorrect. HITL must run and pass before HITL4 is fired."*
3. *"The rewrite script/code block coming from the operator, where does this originate from? It should be connected to a HITL, not directly to the operator."*

The v0.3.0 amend-82 of this methodology doc applied the 3-stage pattern to the flow but the operator pushed back on the diagram order + numbering. The v0.4.0 amend-85 simplifies to 2 HITL gates (HITL #1 + HITL #2), with the verifier compliance check as the unnumbered automated gate between them.

## Part 1: Why this flow

The 9-item rubric in `M-decide-graph-readiness.md` v0.1.1 Part 3 is the substance; the per-loop disposition is the operator's decision; the per-loop action is the environment mutation. The flow wires the three together:

1. **Substance** — the rubric (machine-verifiable items 1-6 + 9 + operator-verified items 7-8) is captured by `python3 ~/.hermes/tools/loop_live_audit.py`.
2. **Decision** — the operator fires the 2 HITL gates (per v0.4.0, amend-85) and disposes each loop at HITL #1 + HITL #2; the verifier's compliance check is the unnumbered automated gate between them.
3. **Action** — the per-loop disposition ticket fires (PROMOTE → flip yaml + bump intent + audit-line; DEFER → deferral ticket; ARCHIVE → archive ticket).

Without this flow, the loop registry is a static file: every loop ships at `live-pending-test`, the audit is run manually, the operator dispositions ad-hoc, and the audit-line is missed. The flow makes the promotion cycle a first-class, auditable work graph on the `loop-builder` board.

### What this flow is not

- **Not a methodology.** The 9-item rubric lives in `M-decide-graph-readiness.md` v0.1.1 Part 3. This doc is the flow spec (the 6-field template + topology + process), not the rubric itself.
- **Not a "process all loops" pass.** The flow processes a SINGLE target loop per master ticket; the operator runs the audit N times for N graphs (per v0.2.0, amend-77).
- **Not autonomous.** The 2 operator HITL gates (HITL #1 + HITL #2) are operator decisions; the flow does not auto-PROMOTE / auto-ARCHIVE without operator disposition. The verifier compliance check is between HITL #1 and HITL #2 (per v0.4.0, amend-85 — unnumbered in the operator's framing).

## Part 2: Flow spec (per `04d-decide-flow-spec.md` — the 6-field template)

### Field 1 — Goal

Audit the SINGLE target loop (specified by the operator in the master ticket's `target_loop_id` binding) against the 9-item rubric (`M-decide-graph-readiness.md` v0.1.1 Part 3), fire the 2 HITL gates (per v0.4.0, amend-85) for the target loop, and take per-loop action per the operator's disposition. One canonical flow per audit cycle; the operator runs the audit N times for N graphs (per v0.2.0, amend-77). The verifier's compliance check is the automated gate between HITL #1 and HITL #2 (per v0.4.0, amend-85 — unnumbered); the operator owns HITL #1 + HITL #2 (2 HITL gates; HITL #2 was previously HITL #4 in v0.4.0 amend-82, now renamed per operator 2026-08-18). Loops can re-enter the flow on a cron schedule (Phase 2, per the loop-updater trigger pattern).

### Field 2 — Inputs

- `~/.hermes/loops/hermes.yaml` — the loop registry (the canonical loop list; the audit reads it to find the target-loop entry, not to enumerate all loops).
- **Master ticket `target_loop_id: <loop-id>` binding** — the canonical target-loop-id surface (per v0.2.0, amend-77). Without the binding, the master is `blocked kind=needs_input` (per op-guard-28) and re-routed with a `clarify` invocation asking the operator to specify the target.
- `python3 ~/.hermes/tools/loop_live_audit.py` — the audit tool (9-item rubric).
- `python3 ~/.hermes/loops/intents/<target-loop-id>-intent.md` — the per-loop intent doc for the target loop.
- `~/Documents/alice-framework/methodology/M-decide-graph-readiness.md` v0.1.1 — the 9-item rubric.
- `~/.hermes/state/skill_registry.json` — the skill registry (per op-guard-17).
- Operator's `clarify` responses (per op-guard-24 + op-guard-28) — the 2 operator gates (HITL #1 + HITL #2) for the target loop.
- Verifier's `--result "compliance: pass"` (per op-guard-17) — the verifier compliance check between HITL #1 and HITL #2 (unnumbered in v0.4.0 amend-85).

### Field 3 — Outputs

- **Audit run output** — the 9-item score for the SINGLE target loop (JSON, default); surfaced to chat-side via the operator-DM cron. **The audit run output is committed to the per-loop audit ticket body (NOT just to a `kanban_comment` on the master) via the canonical `## Audit run` schema (v0.9.4 amend-125 §"## Audit run schema (v0.9.4 amend-125)" below).** An exit_code != 0 (unknown loop-id / missing intent doc / tool error) is itself a valid audit finding; the per-loop audit ticket body carries the JSON envelope so the verifier chain has a determinate output to grade (per amend-125 — without a committed `## Audit run` section, the verifier emits FAIL on identical evidence N times because there is nothing to verify).
- **Per-loop audit ticket** — `[M] [hermes] [loop-audit] <target-loop-id>` on the `loop-builder` board. Body carries the canonical `## Audit run` JSON (per amend-125 §"## Audit run schema") + the 2-HITL-gate dispositions: 2 `clarify-<timestamp>` decision_refs (operator gates HITL #1 + HITL #2) + 1 `--result "compliance: pass"` decision_ref (verifier compliance check between HITL #1 + HITL #2).
- **Per-loop disposition ticket** — `[M] [hermes] [loop-promotion|deferral|archive] <target-loop-id>` per the operator's HITL #2 choice.
- **Follow-up tickets** for missing rubric items (per op-guard-30) — one ticket per missing item for the target loop.
- **Updated intent doc + methodology doc** if HITL #1 REVISE / PARTIALLY-ACHIEVES — the canonical amendment path for the target loop. The Rewrite node fires when HITL #1 gives feedback (per v0.4.0, amend-85 — the feedback on HITL #1 IS the trigger; the Rewrite edge connects to the HITL #1 disposition, NOT directly to the operator).
- **Updated yaml `status:`** if HITL #2 PROMOTE — flips after compliance-verifier passes (per op-guard-30).
- **Audit-line** to `~/Documents/HermesVault/log.md` per op-guard-11 (`vault_log.append_audit_line`).

#### ## Audit run schema (v0.9.4 amend-125)

The per-loop audit ticket body MUST carry a `## Audit run` section that commits the audit tool output verbatim, regardless of exit_code. The schema is the contract between the doc-writer lane (the producer) and the verifier chain (the consumer); without the schema, the verifier has no determinate output to grade.

**Canonical schema (per amend-124):**

```markdown
## Audit run

**Tool:** `python3 ~/.hermes/tools/loop_live_audit.py --loop-id <target-loop-id> --format json`
**Captured at:** <ISO-8601 UTC timestamp> (run <run-id>)
**Target loop:** <target-loop-id>
**Result:** exit_code=<N> (<succeeded | aborted>)

### Tool output (verbatim)

**stdout:** <verbatim stdout from the audit tool — untruncated>
**stderr:** <verbatim stderr from the audit tool — untruncated>

### Per-item scoring (if score emitted)

| # | Item | Result | Evidence |
|---|------|--------|----------|
| 1 | Loop registered in ~/.hermes/loops/hermes.yaml | PASS / FAIL | <evidence path or stderr excerpt> |
| 2 | Intent doc present + versioned | PASS / FAIL | <evidence> |
| 3 | YAML `status:` field | PASS / FAIL | <evidence> |
| 4 | Regression suite present + green | PASS / FAIL | <evidence> |
| 5 | Compliance-verifier child fired | PASS / FAIL | <evidence> |
| 6 | Paired-wiki integrity (per op-guard-5) | PASS / FAIL | <evidence> |
| 7 | Operator review | PASS / FAIL | <evidence> |
| 8 | End-to-end run | PASS / FAIL | <evidence> |
| 9 | Node-type discipline (Scripts/Code vs Agent vs Human per `04a-decide-work-graph.md` Part 4) | PASS / FAIL | <evidence> |

**Score:** <N>/9 (only emitted when `succeeded`; on `aborted` the score is implicit from the FAIL items in the table)
```

**Schema rules:**

1. **Commit regardless of exit_code.** The doc-writer lane MUST commit the `## Audit run` section to the per-loop audit ticket body even when `loop_live_audit.py` exits non-zero (e.g., `unknown loop-id`). A `kanban_comment` on the master with the verbatim output is NOT a substitute; the verifier chain reads the per-loop audit ticket body, not the master's comment thread.
2. **`aborted` is a valid result.** An exit_code != 0 produces `Result: exit_code=<N> (aborted)`. The doc-writer MUST still fill the per-item scoring table from the stderr/stdout (e.g., `unknown loop-id 'X'` → item 1 = FAIL with evidence `<stderr excerpt>`; items 2-9 inherit from the abort context). The table is NOT replaced by a single "no score" line.
3. **Verbatim stdout/stderr.** The doc-writer MUST capture the audit tool's full stdout and stderr (no truncation, no summarization). The verifier chain reads the verbatim output to detect silent-fallback patterns (amend-122 §"silent-fallback mode" — the LLM worker_prompt runs instead of the audit tool because the hook is bypassed).
4. **Score table populated from output.** When the audit tool emits a JSON envelope with item scores (the canonical success path), the doc-writer copies the items into the table verbatim. When the tool errors (e.g., `unknown loop-id`), the doc-writer scores each item from the stderr/stdout: item 1 = FAIL (loop unregistered) is the canonical mapping for `unknown loop-id`; items 2-9 inherit from the abort context per the amend-125 mapping table below.

**Amend-125 stderr → per-item FAIL mapping table (canonical):**

| stderr / abort signal | Item 1 (registered) | Item 2 (intent doc) | Items 3-6, 9 (yaml/status/tests/verifier/paired-wiki/node-type) | Item 7 (operator review) | Item 8 (end-to-end run) |
|---|---|---|---|---|---|
| `unknown loop-id '<X>'` | FAIL | INHERIT (likely absent) | INHERIT (no entry to inspect) | FAIL (no review possible) | FAIL (no run possible) |
| `intent doc missing at ~/.hermes/loops/intents/<X>-intent.md` | PASS (registered) | FAIL | INHERIT (yaml still readable) | FAIL | FAIL |
| `yaml unparseable` | FAIL | INHERIT | FAIL (yaml malformed) | FAIL | FAIL |
| `tool internal error` | FAIL | INHERIT | INHERIT | FAIL | FAIL |
| `success: 9/9` | PASS | PASS | PASS | PASS | PASS |

(INHERIT = carry forward from any prior committed audit run for the same loop-id; on first run with no prior, INHERIT = FAIL.)

**Worked example — unknown loop-id:**

```markdown
## Audit run

**Tool:** `python3 ~/.hermes/tools/loop_live_audit.py --loop-id graph-audit-dry-run-test-loop --format json`
**Captured at:** 2026-08-26T10:34:12Z (run 5)
**Target loop:** graph-audit-dry-run-test-loop
**Result:** exit_code=1 (aborted)

### Tool output (verbatim)

**stdout:** (empty)
**stderr:** `# error: unknown loop-id 'graph-audit-dry-run-test-loop'`\n\n(loop was intentionally unregistered for the DRY-RUN test fixture; per amend-123 the dry-run placeholder-loop contract is the canonical resolution path)

### Per-item scoring (if score emitted)

| # | Item | Result | Evidence |
|---|------|--------|----------|
| 1 | Loop registered in ~/.hermes/loops/hermes.yaml | FAIL | stderr: `unknown loop-id 'graph-audit-dry-run-test-loop'` |
| 2 | Intent doc present + versioned | FAIL | INHERIT (no entry → no intent doc) |
| 3 | YAML `status:` field | FAIL | INHERIT (no entry to inspect) |
| 4 | Regression suite present + green | FAIL | INHERIT (no entry to inspect) |
| 5 | Compliance-verifier child fired | FAIL | no prior verifier child for this loop-id |
| 6 | Paired-wiki integrity (per op-guard-5) | FAIL | INHERIT (no entry) |
| 7 | Operator review | FAIL | no review possible (loop not registered) |
| 8 | End-to-end run | FAIL | no run possible (loop not registered) |
| 9 | Node-type discipline | FAIL | INHERIT (no design doc) |

**Score:** 0/9 (aborted; per amend-125 stderr → per-item FAIL mapping)
```

**Why this schema is the durable fix.** The amend-125 surfaces the verifier-chain invariant: a verifier MUST be able to read the `## Audit run` section from the per-loop audit ticket body and grade it deterministically. Prior patterns posted the audit tool output as a `kanban_comment` on the master; the verifier chain (V-1..V-of-V per Part 3) reads the per-loop audit ticket body, not the master's comment thread, so the comment-only pattern produced FAIL on identical evidence N times (per Retro-A `t_ce82ce87` re-anchor + V-of-V `t_fac38a2d`). The amend-125 codifies the schema that breaks the FAIL-loop.

### Field 4 — Success criteria

- Master ticket carries a valid `target_loop_id: <loop-id>` binding.
- The SINGLE target loop is audited per the 9-item rubric; the audit run output is committed to the per-loop audit ticket body (NOT just to a `kanban_comment` on the master) via the canonical `## Audit run` schema (per v0.9.4 amend-125 §"## Audit run schema (v0.9.4 amend-125)"). The schema is committed regardless of exit_code; an `aborted` run still produces a populated per-item scoring table.
- The 2 operator HITL gates (HITL #1 + HITL #2) fire for the target loop via `clarify` invocations; the verifier compliance check fires via `--result "compliance: pass"` between the 2 HITL gates (per v0.4.0, amend-85). Skip conditions per op-guard-32 v4 still apply.
- The verifier runs AND passes BEFORE HITL #2 fires (per v0.4.0, amend-85 — order is canonical; HITL #2 is BLOCKED until verifier PASS).
- The Rewrite node fires when HITL #1 REVISE / PARTIALLY-ACHIEVES (per v0.4.0, amend-85 — the feedback on HITL #1 IS the trigger).
- Per-loop disposition ticket filed with operator's `decision_ref` (per op-guard-10 v2 §1.2 `closed_per_decision`) + the verifier's `--result "compliance: pass"` as the compliance check.
- Compliance-verifier child ticket (per op-guard-17) fires after each missing item is addressed; the verifier's PASS is the gate that flips the YAML on PROMOTE.
- YAML `status:` flips to `live` after compliance-verifier passes (per op-guard-30 evidence + compliance-verifier chain).
- Audit-line appended per op-guard-11; the canonical 6-field record includes operator disposition timestamp, compliance-verifier ticket id, evidence files, exemption list, audit score, and flip timestamp.

### Field 5 — Retry parameters

- **Master ticket missing `target_loop_id` binding** — block master `kind=needs_input` and re-route with a `clarify` invocation (per v0.2.0, amend-77). The flow does NOT default to "audit all loops".
- **Transient failures** (audit tool timeout, sqlite readonly, etc.) — retry with backoff (3 attempts, 5s/15s/45s).
- **Hard failures** (yaml unparseable, audit tool missing, methodology doc missing) — route to operator-action-dm cron (per op-guard-13).
- **Operator-decision failures** (clarify timeout, disposition rejected) — re-invoke `clarify` per op-guard-28 (with 24h skip rule per op-guard-29).
- **Compliance-verifier failures (verifier child FAIL, amend-88)** — auto-file a `[Fix] [M] [<loop-id>] Address verifier FAIL findings: <short reason>` child ticket assigned to the original profile (default: `doc-writer`); the body carries the full verifier FAIL comment + the FAIL summary + the recommended fix path + the parent verifier ticket link. The auto-filed child is the canonical place for the fix; the operator does NOT manually file a follow-up. After the original-profile commits the fix, the verifier re-fires (3 attempts, 30s/2m/5m). On third still-FAIL, route to operator-action-dm cron per op-guard-13.
- **Out-of-order HITL fire** (HITL #2 fires before verifier PASS, per v0.4.0 amend-85) — block on `kind=dependency` with the canonical reason per op-guard-32 v3. The chat-side / verifier preflight catches the out-of-order fire.

### Field 5b — Parent-edge gate for the verifier chain (per v0.9.3 amend-124)

The graph-audit-promotion master ticket spawns an in-master verifier chain (V-1 → V-2 → V-3 → V-4 → V-of-V; "of_v" is the per-master `v_chain_size` from `M-decide-graph-readiness.md` v0.1.1 Part 3 / the master ticket's `v_chain_size` binding). Each V-N child carries `parents = [V-(N-1)]` in the master ticket's deterministic child-creation helper (`file_graph_audit_children.py`). The same parent-edge shape applies to the in-master Retro chain: `t_ce82ce87` (Retro-A) carries `parents = [V-of-V]`; `t_777d7abc` (Retro-H) carries `parents = [Retro-A]`.

The dispatcher's parent-edge gate promotes a child from `todo` → `ready` ONLY when ALL of the following hold for every parent:

1. **`parent.status == "done"`** — the parent reached its terminal state.
2. **`parent.result starts_with "compliance: pass"`** — for verifier-chain members, the parent's `result` string carries the `compliance: pass|FAIL` token emitted via `kanban_complete --result "compliance: pass"` (per op-guard-17 + Part 5 "Compliance-verifier child"). For non-verifier parents (e.g., HITL #2 disposition tickets, Retro-A normal completion), the parent's `result` is `closed_per_decision` (per op-guard-10 v2 §1.2) — that disposition IS the gate token, not `compliance: pass`.

When either condition fails for a verifier-chain member, the dispatcher MUST block the child on `kind=dependency` with the canonical reason:

> `gate-order violation: V-(N-1) emitted <parent.result>; verifier chain requires compliance: pass`

For Retro-A / Retro-H children whose parent emitted `closed_in_error` (a non-normal completion disposition per op-guard-10 v2 §1.3), the dispatcher MUST block the child on `kind=dependency` with the canonical reason:

> `gate-order violation: <parent_title> emitted <parent.result>; retro chain requires normal completion (closed_per_decision), not closed_in_error`

The verifier-chain member detection rules:

- **Child title:** matches `^\[V\] \[graph-audit-promotion\] V-N — ...` (the canonical V-N title pattern).
- **Parent title:** matches `^\[V\] \[graph-audit-promotion\] V-(N-1) — ...` (the prior V in the chain).
- **Retro-A detection:** child title starts with `[V] [graph-audit-promotion] Retro-A — ...` AND parent title matches `^\[V\] \[graph-audit-promotion\] V-of-V — ...`.
- **Retro-H detection:** child title starts with `[V] [graph-audit-promotion] Retro-H — ...` AND parent title matches `^\[V\] \[graph-audit-promotion\] Retro-A — ...`.

The V-of-V child itself has `parents = [V-(of_v)]` (the last verifier in the chain); V-of-V is the root of the Retro-chain promotion. V-of-V's parent emits `compliance: pass` to advance Retro-A from `todo` → `ready`; Retro-A's normal `closed_per_decision` completion (NOT `closed_in_error`) advances Retro-H.

This is the canonical parent-edge gate enforcement for the verifier chain. The companion dispatcher patch lives at `~/.hermes/hermes-agent/hermes_cli/kanban_db.py` `_check_parent_edges_ready` / `_promote_parent_edges_to_ready` (per the prior `t_db5cf6da` block reason). The 4-case regression suite at `~/.hermes/tests/test_dispatcher_verifier_chain_gate.py` v0.1.0 enforces the gate end-to-end:

1. Verifier child whose parent emitted FAIL stays in `todo` (not promoted to `ready`).
2. Verifier child whose parent emitted PASS is promoted to `ready`.
3. Retro-A whose parent (V-of-V) emitted FAIL stays in `todo`.
4. Retro-H whose parent (Retro-A) completed via `closed_in_error` disposition stays in `todo`.

### Field 6 — Escalation process

- **24h blocked on any stage** — escalate via operator-action-dm cron + chat-side-board-escalator (per the META `t_71b5d8df`).
- **Compliance-verifier FAIL** — route to the verifier + re-dispatch (per the canonical close-gate pattern).
- **Scope/intent revision that triggers rewrites** — file rewrite tickets + re-run the audit + verifier rechecks (the Rewrite node fires from the HITL #1 feedback flow per v0.4.0, amend-85).
- **Per-loop disposition ticket conflict** (operator disposition + verifier mismatch) — surface to operator-DM cron with the conflict + suggested resolution.

## Part 3: Flow topology (per `04a-decide-work-graph.md` — the 3-node-type taxonomy)

The flow has these nodes, each tagged with its type (per `04a-decide-work-graph.md` Part 4) + rationale:

| Node | Type | Rationale |
|------|------|-----------|
| **Audit** | Scripts/Code | `loop_live_audit.py` is deterministic (stdlib + sqlite + subprocess); no LLM in the loop. Same input → same output. Audits the SINGLE target loop (per v0.2.0, amend-77). |
| **HITL #1 (Scope/intent)** | Human | The operator owns the loop's purpose; the agent cannot decide what the loop is for. |
| **Rewrite (intent + methodology)** | Scripts/Code | Doc edits are deterministic: read intent.md + methodology doc, write the diff. The Rewrite node fires when HITL #1 gives REVISE / PARTIALLY-ACHIEVES feedback (per v0.4.0, amend-85 — the feedback IS the trigger; the Rewrite edge connects to the HITL #1 disposition, NOT directly to the operator). |
| **Compliance-verifier (= `verifier` profile)** | Agent | Per op-guard-17, the verifier is an Agent node: it requires judgment (does the instance conform to the doc?). The verifier is NOT the same agent that produced the loop; it's an independent profile (verifier). The verifier's `--result "compliance: pass"` is the compliance check between HITL #1 and HITL #2 (per v0.4.0, amend-85 — unnumbered; was HITL #3 in v0.4.0 amend-82; was the third gate in v0.3.0 4-stage). The verifier's PASS implicitly confirms intent-achievement (the build matches the spec, the spec describes the intent per v0.3.0, amend-82). |
| **Verifier compliance check (unnumbered in v0.4.0, amend-85)** | **Agent (was Human in v0.1.0; was HITL #3 in v0.4.0 amend-82)** | *(v0.4.0, amend-85)* The verifier profile emits PASS or FAIL via `--result "compliance: pass\|fail"` per op-guard-17. This is NOT an operator `clarify` invocation; the operator surfaces the PASS/FAIL outcome from the verifier's report. The verifier runs BEFORE HITL #2 fires; HITL #2 is BLOCKED until verifier PASS. |
| **HITL #2 (Disposition)** | Human | The operator chooses PROMOTE / DEFER / ARCHIVE based on the verifier's compliance check + the HITL #1 scope/intent decision. The verifier's modifications are visible to the operator before HITL #2 fires (per v0.4.0, amend-85 — the verifier runs BEFORE HITL #2). HITL #2 was previously "HITL #4" in v0.4.0 amend-82; renumbered per operator 2026-08-18 (*"stop referring to this final HITL gate as HITL4, it is HITL2"*). Choices: PROMOTE / DEFER / ARCHIVE. |
| **Action (flip yaml + bump intent + audit-line)** | Scripts/Code | YAML flip + intent version bump + audit-line append are deterministic. The audit-line's `disposition` field carries the operator's HITL #2 choice. |

### Flow graph (v0.4.0, amend-85 — 2-HITL pattern, corrected Mermaid diagram)

```
[Target: <target-loop-id>] → [Audit] → [HITL #1] → (REVISE / PARTIALLY-ACHIEVES)
                                         ↓ (CONFIRM)
                                       [Verifier compliance check]
                                         ↓ (PASS)
                                       [HITL #2] → (PROMOTE) → [Action: flip yaml to live]
                                                  → (DEFER)   → [Action: file deferral ticket]
                                                  → (ARCHIVE) → [Action: file archive ticket]

[HITL #1] → (REVISE / PARTIALLY-ACHIEVES) → [Rewrite] → [Doc-Writer updates intent doc + methodology doc]
                                                  ↓
                                              [HITL #1 re-fire] (per op-guard-32 v2 re-run pattern)
                                                  ↓
                                              [Verifier compliance check re-fire]
                                                  ↓
                                              [HITL #2 fires ONLY after verifier PASS]
                                         → (FAIL) → [Audit re-run] → [Verifier compliance check]
```

(The verifier compliance check is the unnumbered automated gate between HITL #1 and HITL #2 per v0.4.0, amend-85. Rewrites from HITL #1 REVISE / PARTIALLY-ACHIEVES feedback re-fire HITL #1 + the verifier check + HITL #2 — the Rewrite edge connects to the HITL #1 disposition, NOT directly to the operator.)

**Constraint (v0.4.0, amend-85):** 2 HITL gates (HITL #1 + HITL #2). The verifier compliance check is the unnumbered automated gate between them. The verifier runs BEFORE HITL #2 fires; HITL #2 is BLOCKED until verifier PASS. Rewrites are fed from the HITL #1 feedback flow.

### Corrected Mermaid diagram (v0.4.0, amend-85 — the canonical 2-HITL pattern)

```mermaid
graph TD
    %% graph-audit-promotion v0.4.0 (methodology twin) — 2-HITL pattern with Rewrite
    %% Node types per M-decide-work-graph.md Part 4
    %% Edges per operator 2026-08-18 corrections (amend-85)

    Operator((Operator<br/>Human node<br/>profile: operator))
    DocWriter[Doc-Writer<br/>Scripts/Code node<br/>profile: doc-writer<br/>builds intent doc + methodology doc]
    Audit[Audit<br/>Scripts/Code node<br/>profile: doc-writer<br/>runs loop_live_audit.py]
    Verifier[Verifier<br/>Agent node<br/>profile: verifier<br/>compliance: PASS / FAIL]
    Rewrite[Rewrite<br/>Scripts/Code node<br/>profile: doc-writer<br/>updates intent doc + methodology doc]
    Action[Action<br/>Scripts/Code node<br/>profile: doc-writer<br/>YAML flip + intent bump + audit-line]

    %% HITL #1 (operator) — scope/intent disposition
    Operator -->|HITL #1: Scope/intent<br/>CONFIRM / REVISE / ABANDON| DocWriter

    %% Build phase
    DocWriter -->|build the graph| Audit
    Audit -->|9-item rubric score| DocWriter
    DocWriter -->|pass to verifier| Verifier

    %% Verifier compliance check (unnumbered in v0.4.0 amend-85)
    %% The verifier runs BEFORE HITL #2 fires — the operator's HITL #2 is BLOCKED until PASS
    Verifier -->|compliance: PASS / FAIL<br/>(unnumbered automated gate between HITL #1 and HITL #2)| Operator

    %% Rewrite loop — fires when operator gives feedback on HITL #1
    %% The feedback is part of the HITL #1 disposition, NOT a direct operator edge
    Operator -->|REVISE / PARTIALLY-ACHIEVES<br/>(feedback on HITL #1)<br/>trigger for Rewrite| Rewrite
    Rewrite -->|updates intent doc + methodology doc| DocWriter

    %% HITL #2 (operator) — final disposition
    %% ONLY FIRES AFTER verifier PASS
    Operator -->|HITL #2: Disposition<br/>PROMOTE / DEFER / ARCHIVE<br/>only after verifier PASS| Action
    Action -->|YAML flip + intent bump + audit-line| Operator

    %% Color coding
    style Operator fill:#ffd700,stroke:#333,stroke-width:2px
    style DocWriter fill:#87ceeb,stroke:#333,stroke-width:2px
    style Verifier fill:#98fb98,stroke:#333,stroke-width:2px
    style Action fill:#ffb6c1,stroke:#333,stroke-width:2px
    style Audit fill:#dda0dd,stroke:#333,stroke-width:2px
    style Rewrite fill:#f0e68c,stroke:#333,stroke-width:2px
```

### Discipline gate

Per `M-decide-graph-readiness.md` v0.1.1 Part 4 §D + Part 5 check 8, every node uses its proper type:

- **Scripts/Code nodes** (Audit, Rewrite, Action) fail loudly with deterministic error messages; no LLM in the loop.
- **Agent nodes** (Compliance-verifier) produce bounded output that the next validator enforces; the verifier is independent from the loop's producer.
- **Human nodes** (HITL #1 + HITL #2) gate decisions the agent cannot make on the operator's behalf.

A loop that has an Agent (LLM-driven) node doing work a script could do deterministically is not `live`-ready; the item-9 violation is a release-blocker that supersedes items 1–8. The graph-audit-promotion flow itself must conform to this discipline (per the verifier child ticket that confirms the instance conforms per op-guard-17).

## Part 4: Process (step-by-step)

1. **Master ticket** — `[MASTER] Graph audit + promotion cycle v<N> — target: <target-loop-id> (<date>)` filed on `loop-builder` board (the canonical board for loops that operate on other loops per `M-decide-loop-catalog.md`). The master ticket MUST carry the `target_loop_id: <loop-id>` binding (per v0.2.0, amend-77); missing binding → `blocked kind=needs_input` (per op-guard-28) + `clarify` invocation asking the operator to specify the target. **Once the master is filed, the chat-side invokes the canonical deterministic dispatch helper `~/.hermes/scripts/file_graph_audit_children.py` v0.1.0 (per v0.9.0 amend-102) which files all 8 children (per_loop_audit + V-1..V-of-V + Retro-A + Retro-H) deterministically — the chat-side LLM has NO role in ticket structure + gate sequence + lane assignments.** The helper is the analog of `~/.hermes/tools/file_x_article.py v0.9.15 _dispatch_production_children`: both are file-time-deterministic 8-children dispatch helpers that read the canonical contract + emit the canonical chain via subprocess (no LLM improvisation).

   **v0.9.1 amend-122 — canonical-path normalization.** When the operator files the master ticket, the dispatcher invokes the canonical `_canonical_path_from_body(body)` helper to recover the `path` column from the body annotation when the `--path` flag is omitted. The helper is whitespace-tolerant (leading/trailing whitespace ignored) + handles trailing-suffix recovery (`path: graph-audit-promotion-v6-amend-117` → `graph-audit-promotion`). The canonical path resolution handles 4 modes of operator body annotation:
   - **Exact match:** `path: graph-audit-promotion` (canonical, fires the hook directly).
   - **Trailing `-v<N>` suffix:** `path: graph-audit-promotion-v6` (recovered to `graph-audit-promotion` via longest-prefix matching against `_REGISTERED_LOOP_IDS`).
   - **Trailing `-v<N>-amend-<N>` suffix:** `path: graph-audit-promotion-v6-amend-117` (recovered to `graph-audit-promotion` via longest-prefix matching).
   - **No annotation:** `_canonical_path_from_body` returns `None`; the hook is skipped; the LLM worker_prompt is the fallback (per amend-122 invariant 4).

   The companion test `~/.hermes/tests/test_extract_flow_path_normalization.py` v0.9.1 covers all 6 cases (exact match, `-v6` suffix, `-v6-amend-117` suffix, leading whitespace, trailing whitespace, no `path:` annotation). The hook at `hermes_cli/kanban_db.py::create_task` line 4673 is the canonical dispatch surface; the post-create hook fires within the same transaction as `kanban_create`, before the master-worker subprocess spawns.
2. **Audit node** — fires `python3 ~/.hermes/tools/loop_live_audit.py --loop-id <target-loop-id> --format json` for the SINGLE target loop (per v0.2.0, amend-77). Output is committed to the **per-loop audit ticket body** (NOT the master ticket, NOT a `kanban_comment`) as a `## Audit run` section per the canonical schema (per v0.9.4 amend-125 §"## Audit run schema (v0.9.4 amend-125)"). The schema is committed regardless of `loop_live_audit.py` exit_code; an `aborted` audit still produces a populated per-item scoring table. The doc-writer lane is responsible for the commit per amend-125 schema rules (commit regardless of exit_code + verbatim stdout/stderr + score table populated from output).
3. **Per-loop HITL gates** — fire the 2 HITL gates in order for the target loop (skip conditions per op-guard-32 v4):
   - **HITL #1 (Scope/intent)** — `clarify` with the loop's intent summary + observed behavior summary. Choices: CONFIRM / REVISE / ABANDON.
   - **Verifier compliance check** *(unnumbered in v0.4.0 amend-85)* — **verifier profile** reviews the doc-writer's build artifacts + the per-loop intent doc + the methodology doc + the YAML entry, then emits PASS or FAIL via `--result "compliance: pass\|fail"`. The verifier is the canonical gate between HITL #1 and HITL #2 (unnumbered in v0.4.0 amend-85 per operator 2026-08-18). The operator does NOT do the verifier check. The verifier's PASS implicitly confirms intent-achievement (per v0.3.0, amend-82). HITL #2 fires ONLY after verifier PASS.
   - **HITL #2 (Disposition)** — `clarify` with the prior gate decisions + the verifier's compliance check outcome + the canonical PROMOTE / DEFER / ARCHIVE. The operator's HITL #2 is the final word. The verifier's modifications are visible before HITL #2 fires (per v0.4.0, amend-85).
4. **Rewrite nodes** — fire when scope/intent requires graph changes (operator dispositions REVISE / PARTIALLY-ACHIEVES on HITL #1, per v0.4.0 amend-85 — the feedback IS the trigger). The Rewrite node fires intent doc + methodology doc edits, child rewrite tickets for the graph itself. After rewrites, HITL #1 re-fires (per op-guard-32 v2 re-run pattern) + verifier rechecks + HITL #2 fires after verifier PASS.
5. **Compliance-verifier child** — per op-guard-17, fires per loop as the unnumbered verifier gate (between HITL #1 and HITL #2, per v0.4.0 amend-85) and confirms the instance conforms to the methodology doc + intent + yaml + tests. The verifier's `--result "compliance: pass"` is the gate that flips the YAML on PROMOTE. HITL #2 is BLOCKED until verifier PASS.

   **Verifier FAIL → auto-file `[Fix]` child ticket to original-profile (amend-88).** When the verifier emits `--result "compliance: fail"` on the per-loop compliance-verifier child, the flow auto-files a child ticket addressed to the **original profile that produced the artifact** (the profile whose work is being verified). For a methodology/intent-doc/yaml artifact the original profile is `doc-writer`; for a code artifact (e.g., a skill body or a tool) the original profile is `coder`; for a council verdict artifact it is `council`. The original-profile assignment is read from the per-loop audit ticket body's `original_profile` field (the doc-writer writes this when the artifact is shipped) OR falls back to `doc-writer` for the canonical graph-audit-promotion target loop (the doc-writer owns the methodology + intent + yaml chain). The auto-filed child ticket:
   - **Title:** `[Fix] [M] [<loop-id>] Address verifier FAIL findings: <short reason>` (the short reason is the first 80 chars of the verifier's `--result` after `"compliance: fail — "` is stripped).
   - **Body:** the **full verifier comment** (the entire FAIL comment, not just the summary), the FAIL summary, the recommended fix path (per the verifier's "recommended fix" section), and the parent link to the verifier ticket. The body MUST carry the FULL comment — a summary-only child ticket defeats the amend-88 contract.
   - **Assignee:** the original profile (e.g., `doc-writer` for x-article-review). The auto-filed child is the canonical place for the fix.
   - **Parent:** the verifier ticket that emitted FAIL. The parent edge is the per-loop audit chain.
   - **Re-dispatch of the verifier:** after the original-profile addresses the FAIL + commits the fix, the verifier re-fires (the child ticket's terminal transition triggers a fresh verifier child ticket per op-guard-17 + op-guard-30 evidence chain). HITL #2 is STILL BLOCKED until the verifier emits PASS.

   The amend-88 contract replaces the prior "re-fire the verifier child ticket (3 attempts, 30s/2m/5m); on third FAIL, route to operator-action-dm cron per op-guard-13" pattern. The prior pattern made the verifier wait for the operator to manually file a follow-up ticket — fragile because the operator may forget or file in the wrong place. The amend-88 pattern closes the loop automatically: verifier FAIL → `[Fix]` child to original-profile → original-profile commits the fix → verifier re-fires →
6. **Master-ticket summary PDF generation (v0.6.0, amend-91).** When the verifier PASS lands + HITL #2 is ready to fire, the flow auto-generates the master-ticket summary PDF via the canonical helper `~/.hermes/tools/post_graph_audit_disposition_pdf.py` v0.6.0 (modeled after `post_council_verdict_pdf.py` + `post_executive_report_pdf.py`). The helper:
   1. Resolves the master ticket id + target loop id + per-loop audit ticket id + the loop slug (from `target_loop_id: <loop-id>` in the master body per v0.3.0 amend-77).
   2. Locates the master-ticket summary Markdown at one of the canonical paths OR auto-generates the 8-section executive-reporting shape from the per-loop audit ticket body + the target loop's intent doc. Section 3 Mermaid is the TARGET LOOP's Mermaid, NOT the audit flow's Mermaid (per the operator 2026-08-18 review).
   3. Renders the Markdown to PDF at the canonical operator-facing path `~/Documents/HermesVault/0-INBOX/<loop-slug>-master-ticket-summary.pdf` via `md_to_pdf.py`.
   4. Sends the canonical PDF as a Discord attachment via `send_dm_attachments` — **mandatory, fail-closed per op-guard-3**: a failed send blocks the master on `kind=needs_input` + the chat-side files a `[HUMAN ACTION] graph-audit-pdf-delivery-failed` child ticket to surface the failure to the operator-DM cron. The chat-side MUST NOT fire the HITL #2 `clarify` before Discord delivery succeeds (per the v0.6.0 amend-91 fail-closed contract, parallel to Retro-H's fail-closed per op-guard-3).
   5. Posts a master-ticket comment that begins exactly with the literal `Graph audit disposition ready` prefix (parallel to `Council verdict ready` for x-article-review + `Retro-H executive report ready` for Retro-H).
   6. Copies the PDF to the durable audit trail at `~/Documents/HermesVault/2-ATOMIC/decisions/<loop-slug>-master-ticket-summary.pdf`.
   7. Appends a one-line audit-line to `~/Documents/HermesVault/log.md` per op-guard-11 via `vault_log.append_audit_line`.
   8. The chat-side then fires `clarify` referencing the PDF as evidence (per the executive-reporting skill "PDF-first, then `clarify`" pattern + op-guard-24 + op-guard-28). The operator's HITL #2 disposition (PROMOTE / DEFER / ARCHIVE) is captured as the `clarify-<timestamp>-disposition` decision_ref per op-guard-29 + op-guard-10 v2 §1.2.
7. **Action node** — flips YAML `status:` + bumps intent doc `version:` + appends audit-line per the operator's HITL #2 disposition. Per op-guard-30 evidence + compliance-verifier chain.

## Part 5: Bridging the 9-item rubric to the flow

The 9-item rubric (`M-decide-graph-readiness.md` v0.1.1 Part 3) maps to the flow as follows:

| Rubric item | Audit node | Compliance-verifier |
|---|---|---|
| 1. Methodology doc | ✅ machine | Agent re-checks |
| 2. Intent doc | ✅ machine | Agent re-checks |
| 3. Yaml status | ✅ machine | Agent re-checks |
| 4. Regression suite | ✅ machine (count) | Agent runs pytest |
| 5. Compliance-verifier child | (this is what fires) | N/A |
| 6. Paired-wiki integrity | ✅ machine (git log) | Agent re-checks |
| 7. Operator review | ⚠ operator-verified | Operator survey |
| 8. End-to-end run | ✅ machine (log.md) | Agent re-checks |
| 9. Node-type discipline | ✅ machine (regex) | Agent walks the design doc |

Items 1–6 + 9 are machine-verifiable; the Audit node captures them. Item 5 is the compliance-verifier child itself (the per-loop child that fires as the verifier gate per v0.4.0 amend-85). Items 7–8 are operator-verified (the verifier's report + HITL #2 disposition review surfaces them). The Compliance-verifier Agent re-confirms the machine-verifiable items and walks the design doc for item 9.

## Part 6: Per-loop action (the 3 dispositions)

### PROMOTE

Per op-guard-30:

1. **Evidence filing** — file follow-up tickets for any missing items (the 9-item rubric's `❌` items).
2. **Compliance-verifier child** — fires per op-guard-17; transitions to `done` with `--result "compliance: pass"` after the verifier confirms the instance conforms.
3. **YAML flip** — `status: live-pending-test → live` + intent doc `version:` bumped per semver + audit-line appended per op-guard-11.
4. **Audit-line** — captures the 6 fields: operator disposition timestamp, compliance-verifier ticket id, evidence files, exemption list, audit score, flip timestamp.

### DEFER

1. **Per-loop deferral ticket** — `[M] [hermes] [loop-deferral] <loop-id>` filed on `loop-builder` board with `closed_per_decision` metadata (per op-guard-10 v2 §1.2) + the operator's `clarify-<timestamp>` decision_ref.
2. **Re-disposition** — the loop re-enters the flow on a later cycle (24h re-prompt rule per op-guard-29).
3. **Audit-line** — captures the deferral event + the next-cycle planned date.

### ARCHIVE

1. **Per-loop archive ticket** — `[M] [hermes] [loop-archive] <loop-id>` filed on `loop-builder` board with `closed_per_decision` metadata + the operator's `clarify-<timestamp>` decision_ref.
2. **YAML change** — `status: archived` (new status) OR loop entry removed from `~/.hermes/loops/hermes.yaml` per op-guard-10 v2 §1.3 (`closed_in_error`).
3. **Intent doc + methodology doc** — retained for forensic trace (the "no auto-delete" rule per the loop-updater's `no auto-merge or auto-delete` constraint).
4. **Audit-line** — captures the archive event + the operator's rationale.

## Part 7: Application procedure

To set up the flow on the operator's instance:

1. **File the methodology doc** (this doc) on `alice-framework` board at `~/Documents/alice-framework/methodology/M-decide-graph-audit-promotion-flow.md` v0.9.1. (Doc-writer owns this; v0.9.1 ships the amend-122 dispatcher-integration contract per the operator 2026-08-26 quote — canonical-path normalization in Part 4 step 1; dispatcher-integration contract language in Part 7; cross-reference + paired-wiki concept note in Part 8; amendment record appendix in Part 9.)
2. **File the loop entry** in `~/.hermes/loops/hermes.yaml` with `status: live-pending-test`, `register_with_profile: planner`, `run_with_profile: doc-writer`, `target_board: loop-builder`, `path: graph-audit-promotion`. (Coder owns this.)
3. **File the intent doc** at `~/.hermes/loops/intents/graph-audit-promotion-intent.md` v0.9.1. (Coder owns this; v0.9.1 ships the amend-122 dispatcher-integration contract + the paired-wiki concept note mirror.)
4. **File the regression suite** at `~/.hermes/tests/test_graph_audit_promotion_loop.py` v0.4.1 with the test additions for the 2-HITL pattern. (Coder owns this.)
5. **File the compliance-verifier child ticket** on `loop-builder` board that confirms the instance conforms to this doc + the intent doc + the YAML entry + the regression suite. (Verifier profile owns this; the verifier is the canonical gate between HITL #1 and HITL #2 per v0.4.0 amend-85.)
6. **Trigger the master ticket** — the operator (or jarvis on the operator's behalf) files a master ticket on `loop-builder` for the first audit cycle v1, with the `target_loop_id: <target-loop-id>` binding. The master ticket fires the flow per Part 4. The operator runs the audit N times for N graphs.

The instance-side wiring of the flow follows the spec-first-flow rule (per op-guard-16 + op-guard-17): this doc is the alice-first; the loop yaml + intent + tests are the instance-second; the compliance-verifier is the done-gate child.

### Dispatcher-integration contract (v0.9.1 amend-122)

The dispatcher-integration contract codifies 4 modes of operator body annotation that the dispatcher handles deterministically:

- **Mode A — operator typed `--path` flag matching body annotation:** `path: graph-audit-promotion` in the body + `--path graph-audit-promotion` CLI flag. The dispatcher sets the `path` column from the flag (canonical, no backfill needed); the post-create hook fires directly.
- **Mode B — operator typed `--path` flag with trailing suffix:** `--path graph-audit-promotion-v6-amend-117` + body annotation. The dispatcher sets the `path` column from the flag (the operator's value); the canonical resolution at the hook site recovers `graph-audit-promotion` via longest-prefix matching; the hook fires.
- **Mode C — operator omitted `--path` flag, body carries annotation:** `--path` not passed + `path: graph-audit-promotion` in the body. The dispatcher's write-time backfill `_extract_flow_path_from_body(body)` recovers the `path` column from the body annotation (canonical, fires the hook).
- **Mode D — operator omitted both:** `--path` not passed + no body annotation. The write-time backfill returns `None`; the hook is skipped; the LLM worker_prompt is the fallback (per amend-122 invariant 4 — the LLM only fires when the hook is bypassed or reports hard-fail).

The 4 modes are mutually exclusive (one of them applies per master ticket filing). The fallback ladder is documented in the intent doc's "Runtime profile" section: (post-create hook fires) → deterministic 8-children dispatch via `~/.hermes/scripts/file_graph_audit_children.py` v0.1.1 → master closed with audit-line. The LLM worker_prompt only fires when (a) the hook is bypassed (Mode D, or Mode B with non-canonical suffix that the longest-prefix matcher cannot recover) OR (b) the script reports a hard-fail (e.g., `ChildDispatchError` from aggregate-fail).

The companion regression suite `~/.hermes/tests/test_graph_audit_dispatcher_fires.py` v0.9.1 covers the 4 dispatcher-bypass failure modes (hook silently skipped due to Mode B non-recoverable suffix; hook silently skipped due to Mode C backfill failure; LLM worker_prompt firing in Mode D without fallback ladder audit-line; script hard-fail with hook bypassed). The per_loop_audit doc-writer child verifies the dispatcher fired within 10 seconds of master creation via `task_events` inspection (amend-122 invariant 5).

### Target-loop pre-flight + dry-run placeholder-loop contract (v0.9.2 amend-123)

The flow's per-loop audit (item 1 of the 9-item rubric) requires the target loop to be **registered** in `~/.hermes/loops/hermes.yaml`. The amend-123 codifies the pre-flight check + the dry-run placeholder-loop canonical contract that surfaces when this check fails.

**Pre-flight check.** The canonical helper `~/.hermes/scripts/file_graph_audit_children.py` v0.9.2 MUST pre-flight-verify the target loop is registered in `~/.hermes/loops/hermes.yaml` BEFORE dispatching `per_loop_audit` children. The pre-flight resolves `target_loop_id: <loop-id>` from the master body (per v0.2.0, amend-77) and walks the registry; on missing registration, the helper returns rc=2 with a clear `kind=needs_input` reason:

```
file_graph_audit_children: master ticket <t_xxx> targets loop <loop-id> which is NOT registered in ~/.hermes/loops/hermes.yaml. Per amend-123 (v0.9.2), the target loop MUST be registered before the per-loop audit can fire. Two options: (a) Add a placeholder entry for <loop-id> (dry-run placeholder-loop contract — see below), or (b) Revise scope to a registered loop (HITL #1 REVISE). Block the master on kind=needs_input per op-guard-28 and invoke clarify.
```

The pre-flight rc=2 maps to a master `kind=needs_input` block + a `[HUMAN ACTION]` child ticket that surfaces the dry-run placeholder-loop contract + the HITL #1 (scope/intent, op-guard-32 v1) options: CONFIRM (register placeholder per the canonical contract below), REVISE (re-scope to a registered loop), ABANDON (cancel the dry-run).

**Dry-run placeholder-loop canonical contract.** Dry-run fake loops (used to validate the graph-audit-promotion dispatcher + script + middleware end-to-end before running a real loop promotion) MAY be registered at `status: live-pending-test` with a tag `kind:dry-run-placeholder` (in the front-matter `tags:` array, the doc-writer's paired-wiki integrity path) + the canonical fields:

```yaml
- id: <dry-run-loop-id>
  version: 0.0.1-dryrun
  register_with_profile: doc-writer
  run_with_profile: doc-writer
  target_board: loop-builder
  status: live-pending-test
  path: <dry-run-loop-id>
  tags: [kind:dry-run-placeholder]
  context_as_budget:
    max_searches: 0
    max_turns: 1
    max_wallclock_seconds: 60
  trigger:
    kind: none  # dry-run placeholder loops NEVER auto-fire
  worker_prompt: "## Loop: <dry-run-loop-id>\nGoal: DRY-RUN placeholder loop registered ONLY to validate the per-loop audit gate end-to-end. NOT for production use; this loop is a fixture for dispatch testing. Archive after the dry-run master <t_xxx> closes.\nBound: never fires (no trigger).\nverify:\n  - file exists at hermes.yaml with the canonical placeholder fields\nstop_when: never (this loop does not run)."
```

**Paired placeholder intent doc.** Every dry-run placeholder loop MUST ship a paired stub `~/.hermes/loops/intents/<loop-id>-intent.md` documenting the fixture contract:

```
## Goal

DRY-RUN placeholder loop registered to validate the graph-audit-promotion
dispatcher + script + middleware end-to-end. The loop NEVER fires; it exists
ONLY to allow the per-loop audit tool (`~/.hermes/tools/loop_live_audit.py`)
to read its entry from the registry and emit a 0/6 + 0/1 + 0/1 audit score
(all 9 items FAIL — placeholder metadata only).

## Scope

- Registered at `status: live-pending-test`, `path: <loop-id>`, `tags: [kind:dry-run-placeholder]`.
- `worker_prompt` clarifies: NOT for production use; fixture for dispatch testing.
- Paired loop entry in `~/.hermes/loops/hermes.yaml` per the canonical
  dry-run placeholder-loop contract (v0.9.2 amend-123).

## Stop-condition

- Archive this loop + delete its paired intent doc when the dry-run master
  `<t_xxx>` closes (per op-guard-10 v2 §1.3 closed_in_error, rationale:
  "dry-run complete; placeholder loop no longer needed").

## Archive-after-master-close

- The dry-run master ticket owns the archive loop; chat-side fires a
  follow-up `[M] [hermes] [loop-archive] <loop-id>` child on the loop-builder
  board per the canonical contract.
```

The placeholder intent doc is a stub (no Phase / V / HITL / Retro structure) since the loop never fires end-to-end. The stub is the canonical contract for dry-run fixtures; substantive Phase / V / HITL / Retro structure is reserved for real loops that ship audit cycles.

**Why this amendment.** The DRY-RUN master ticket `t_db5cf6da` (per_loop_audit child `t_cfe657f4`) attempted the per-loop audit on `graph-audit-dry-run-test-loop` per the operator's 2026-08-26 chat (verbatim): *"let's verify planner is working first, then dry run graph audit"*. The audit tool exited 1 with `# error: unknown loop-id 'graph-audit-dry-run-test-loop'`. The flow's dispatcher→script chain works (per the op-guard-dispatcher comment on the master at 2026-08-26T10:30:12Z, the helper was invoked + emitted the canonical rc=2 pre-flight message); the helper's "loop must be registered" pre-check is the actual gate catching the dry-run. The amend-123 codifies this pre-flight as a normative check (not just a tool-internal error) + the dry-run placeholder-loop canonical contract that an operator can register to enable the dry-run without re-scoping to a real loop.

## Part 8: Cross-references

- **`methodology/04d-decide-flow-spec.md`** — the 6-field flow spec template that this doc adapts.
- **`methodology/04a-decide-work-graph.md`** — the work-graph state machine + the 3-node-type taxonomy (Part 4) that this flow conforms to.
- **`methodology/04c-decide-master-ticket.md`** — the master-ticket pattern (the master ticket is the orchestration entry; per-loop tickets are the children).
- **`methodology/M-decide-graph-readiness.md` v0.1.1`** — the 9-item rubric + the 2-HITL process (this flow's audit + per-loop action).
- **`methodology/M-decide-spec-first-flow.md`** — the 4-phase flow + verifier steps. Part 2's phase-3 ("update environment") is where this flow ships.
- **`methodology/M-decide-instance-vs-framework.md`** — the framework-vs-instance distinction. This doc is framework-side; the loop entry + intent + tests are instance-side.
- **`2-ATOMIC/rules/op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md`** — alice-first / instance-second. This doc is the alice-first; the loop yaml + intent + tests are the instance-second.
- **`2-ATOMIC/rules/op-guard-29-per-loop-hitl-gate-2026-08-15.md`** — the per-loop HITL gate (PROMOTE / DEFER / ARCHIVE). This flow codifies the gate within the audit cycle.
- **`2-ATOMIC/rules/op-guard-30-promotion-requires-evidence-or-exemptions-2026-08-17.md`** — promotion requires evidence + 4-field exemption schema. The flow's PROMOTE branch follows this rule.
- **`2-ATOMIC/rules/op-guard-31-three-stage-graph-hitl-2026-07-20.md`** — superseded by op-guard-32.
- **`2-ATOMIC/rules/op-guard-32-four-stage-graph-hitl-2026-08-17.md`** v4 amend — the canonical HITL pattern. Under v0.4.0 amend-85, the 4-stage pattern is simplified to 2 HITL gates (HITL #1 + HITL #2) with the verifier compliance check as the unnumbered automated gate between them. This flow is the operational surface for op-guard-32 v4.
- **`amend-88 (verifier FAIL routes to original-profile via auto-filed `[Fix]` child ticket, source `t_6ccf4e4b`)** — when the verifier emits `--result "compliance: fail"` on the per-loop compliance-verifier child, the flow auto-files a `[Fix] [M] [<loop-id>] Address verifier FAIL findings: <short reason>` child ticket assigned to the original profile (default: `doc-writer`). The auto-filed child carries the full verifier FAIL comment + the recommended fix path; the original-profile commits the fix; the verifier re-fires; HITL #2 stays BLOCKED until PASS. The amend-88 contract replaces the prior "re-fire the verifier 3x then route to operator-action-dm" pattern. Companion test: `~/.hermes/tests/test_verifier_fail_routes_to_doc_writer.py` v0.1.0 (5-case regression suite). Companion script: `~/.hermes/tools/verifier_fail_route.py` v0.1.0 (the canonical fail-routing script). Companion intent doc: `~/.hermes/loops/intents/graph-audit-promotion-intent.md` v0.5.1 (paired-wiki mirror per op-guard-5).
- **`amend-91 (flow owns the canonical PDF generation step at HITL #2, source `t_a6701e2b`)** — when the verifier PASS lands + HITL #2 is ready to fire, the flow auto-generates the master-ticket summary PDF via the canonical helper `~/.hermes/tools/post_graph_audit_disposition_pdf.py` v0.6.0 + delivers it to the operator's Discord DM (mandatory, fail-closed per op-guard-3) + posts the `Graph audit disposition ready` master-ticket comment + copies to the vault + appends the audit-line. The chat-side then fires `clarify` referencing the PDF as evidence (per the executive-reporting skill "PDF-first, then `clarify`" pattern). The amend-91 contract replaces the prior "chat-side manually creates the PDF via `md_to_pdf.py` + `send_dm_attachments`" pattern that violated op-guard-21 (chat-side orchestrator-only) + the operator's principle "If the flow is incorrect, fix the flow". Companion helper: `~/.hermes/tools/post_graph_audit_disposition_pdf.py` v0.6.0 (modeled after `post_council_verdict_pdf.py` + `post_executive_report_pdf.py`). Companion test suite: `~/.hermes/tests/test_graph_audit_disposition_pdf.py` v0.6.0 (8-case regression suite: PDF generated + comment posted + Mermaid renders + idempotency + fail-closed + audit-line + slug derivation + walk-mode discovery). Companion intent doc: `~/.hermes/loops/intents/graph-audit-promotion-intent.md` v0.6.0 (paired-wiki mirror per op-guard-5).
- **`amend-102 (canonical deterministic file-time dispatch for the 8 children of a graph-audit-promotion master ticket, source `t_b647f9a6`)** — per operator 2026-08-23 (chat, verbatim): *"this is still LLMs deciding what tickets to make. Since the graph is defined, we should create all tasks programatically"*. The dispatch is a code path, NOT an LLM interpretation. The canonical helper `~/.hermes/scripts/file_graph_audit_children.py` v0.1.0 reads the master ticket body for `target_loop_id: <loop-id>` + builds the canonical 8-children plan (per_loop_audit + V-1..V-of-V + Retro-A + Retro-H) + the deterministic parent-edge chain (master → per_loop_audit → V-1 → V-2 → V-3 → V-4 → V-of-V → Retro-A → Retro-H) + the lane assignments (doc-writer for per_loop_audit + retro_a; verifier for V-1..V-of-V; jarvis for retro_h) + the bodies (deterministic templates per child kind). Idempotent via the canonical `--idempotency-key` shape `graph-audit-promotion/<master_id>/<kind>`. Aggregate-fail contract per the file_x_article.py amendment 30 hard-fail pattern: on per-child subprocess failure, the helper sets a `<FAILED:{kind}>` placeholder in the parent-edge resolver so the next iteration's parent-edge lookup does NOT crash with KeyError. After the loop completes, an aggregate-fail check raises `ChildDispatchError` listing every failed child kind + its per-child stderr; `_main_wrapper` exits non-zero (exit 7). The helper is the analog of `~/.hermes/tools/file_x_article.py v0.9.15 _dispatch_production_children` — both are file-time-deterministic 8-children dispatch helpers that read the canonical contract + emit the canonical chain via subprocess. Companion helper: `~/.hermes/scripts/file_graph_audit_children.py` v0.1.0 (NEW). Companion test suite: `~/.hermes/tests/test_file_graph_audit_children.py` v0.1.0 (NEW, 3-case regression: alice-publish + x-article-review + idempotency). Companion intent doc: `~/.hermes/loops/intents/graph-audit-promotion-intent.md` v0.9.0 (paired-wiki mirror per op-guard-5).
- **`amend-92 (dynamic Mermaid generation from target loop intent doc, source `t_9a0c1b88`)** — the v0.6.0 extractor was a single regex (``mermaid`` block in the target loop's intent doc). It fell back to a labeled placeholder ("Target loop: <loop-id> — no Mermaid in intent doc") when the intent doc structured the flow as prose (e.g. x-article-review uses `### Phase 0..4` + `### V-N` + `### Post-council Retro-A + Retro-H spawn` headings — no Mermaid block). The operator's 2026-08-19 review caught the v0.6.0 PDF rendering a placeholder Mermaid for x-article-review HITL #2 instead of the actual flow: *"The PDF is incorrect. The mermaid shows a single box that says 'Target Loop: ...'. The entire contents of the HITL2 PDF need to be about the graph that is being audited, not about the audit graph self. Need a durable solution"*. The v0.6.1 dynamic extractor reads the intent doc's prose structure and renders a `graph TD` Mermaid diagram from the discovered Phases (H2-H4 `Phase N` headings, with anti-patterns/input-set/stop-condition/implementation sub-sub-headings filtered) + Verifiers (H2-H3 `V-N` headings + inline `V-1 + V-2 + ... + V-of-V` mentions) + HITL gates (inline `HITL-X` mentions) + Retro gates (inline `Retro-X` mentions). The diagram reflects the actual target loop's flow. Per-loop author never has to write a Mermaid block by hand; loops with hand-authored Mermaid blocks still preserve the v0.6.0 behavior (use the hand-authored block verbatim). Mermaid syntax rules per `~/.hermes/skills/productivity/executive-reporting/references/mermaid-syntax-rules.md` are enforced via `_sanitize_mermaid_label` (`-->` → `→`, `\n` → `<br/>`, backtick + double-quote escaping, whitespace collapse). Companion helper: `~/.hermes/tools/post_graph_audit_disposition_pdf.py` v0.6.1. Companion test suite: `~/.hermes/tests/test_graph_audit_disposition_pdf.py` v0.1.1 (4 new regression cases: dynamic extraction from x-article-review prose + placeholder when no structure + hand-authored Mermaid preserved + sanitizer + structure extraction) — 12 cases total. Companion intent doc: `~/.hermes/loops/intents/graph-audit-promotion-intent.md` v0.7.0 (paired-wiki mirror per op-guard-5).
- **`2-ATOMIC/rules/op-guard-11-log-md-append-only-2026-07-22.md`** — audit-line discipline. Every flow transition appends an audit-line via `vault_log.append_audit_line`.
- **`2-ATOMIC/rules/op-guard-10-no-close-on-age-2026-07-27.md`** — closure evidence. The per-loop disposition ticket's `closed_per_decision` metadata carries the `decision_ref` (the operator's `clarify-<timestamp>`).
- **`2-ATOMIC/rules/op-guard-28-clarify-before-block-needs-input-2026-08-15.md`** — the canonical `clarify` before `kanban_block(kind=needs_input)` discipline. The v0.2.0, amend-77 missing-target-loop-id binding falls into this rule: the flow invokes `clarify` to ask the operator to specify the target before blocking the master.
- **`2-ATOMIC/rules/op-guard-16-spec-first-flow-2026-08-05.md`** — doc-writer is spec-only. The v0.9.1 paired-wiki mirrors (intent doc + methodology doc) ship via this ticket (phase 1+2); the dispatcher patch + 2 regression suites ship via coder follow-up tickets (phase 3); the compliance-verifier child is the phase-4 done-gate.
- **`2-ATOMIC/rules/op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md`** — alice-first / instance-second. This methodology doc v0.9.1 is the alice-first; the intent doc v0.9.1 + the dispatcher patch are the instance-second; the compliance-verifier child is the done-gate.
- **`2-ATOMIC/rules/op-guard-19-pre-verify-artifact-state-2026-08-08.md`** — pre-verify artifact state. Every loop-amendment ticket files the "Verified state" section with the captured output from `verify_artifact_state.py` before commit. The v0.9.1 paired-wiki mirrors apply op-guard-19 to both files (intent doc + methodology doc frontmatter state asserted at filing time).
- **`~/.hermes/skills/autonomous-ai-agents/hermes-loop-validate/SKILL.md`** — the canonical end-to-end loop validation skill. The v0.9.1 amend-122 dispatcher-integration contract is consistent with the 7-phase validation discipline (intent-doc-read → methodology-doc-read → loop-yaml-read → regression-suite-run → dispatcher-fires-trace → compliance-verifier-PASS → audit-line-append).
- **`amend-122 (canonical dispatcher-integration contract, source `t_d132ad31`)** — per operator 2026-08-26 (chat, verbatim): *"Since we have a graph for updating graphs, we should be using that to fix the audit graph instead of you filling tickets directly for agents to work."*. The v0.9.1 amend-122 codifies the dispatcher-integration contract: 4 modes of operator body annotation (Mode A: `--path` matches body; Mode B: `--path` carries trailing suffix; Mode C: `--path` omitted, body carries annotation; Mode D: both omitted); canonical resolution via `_canonical_path_from_body` helper (whitespace-tolerant + trailing-suffix recovery); post-create hook fires iff canonical_path resolves; LLM worker_prompt is a fallback (NOT a peer); per_loop_audit child verifies dispatcher fired within 10 seconds of master creation; audit-line per op-guard-11 captures dispatch outcome. The amend-122 codifies the canonical contract between the dispatcher's post-create hook + the write-time backfill + the LLM worker_prompt fallback. Companion concept note: `~/Documents/HermesVault/2-ATOMIC/concepts/graph-audit-promotion-dispatcher-integration-v0-9-1-2026-08-26.md`. Companion intent doc: `~/.hermes/loops/intents/graph-audit-promotion-intent.md` v0.9.1 (paired-wiki mirror per op-guard-5; this ticket's paired-wiki mirror). Companion dispatcher patch: `~/.hermes/hermes-agent/hermes_cli/kanban_db.py` (the canonical `_canonical_path_from_body` helper + the canonical resolution patch; ships via coder ticket per op-guard-21). Companion test suite (1): `~/.hermes/tests/test_extract_flow_path_normalization.py` v0.9.1 (NEW; 6-case regression suite). Companion test suite (2): `~/.hermes/tests/test_graph_audit_dispatcher_fires.py` v0.9.1 (NEW; 4-case regression suite).
- **`amend-123 (target-loop pre-flight check + dry-run placeholder-loop canonical contract, source `t_cfe657f4` via doc-writer per_loop_audit)** — the DRY-RUN master ticket `t_db5cf6da` validated the dispatcher + script + middleware end-to-end by attempting the per-loop audit on `graph-audit-dry-run-test-loop`; the audit tool exited 1 with `# error: unknown loop-id 'graph-audit-dry-run-test-loop'` (the loop was never registered in `~/.hermes/loops/hermes.yaml`). The amend-123 codifies 3 changes: (1) **target-loop pre-flight check** — the canonical helper `~/.hermes/scripts/file_graph_audit_children.py` MUST pre-flight-verify the target loop is registered in `~/.hermes/loops/hermes.yaml` BEFORE dispatching `per_loop_audit` children; on missing registration, the helper returns rc=2 with a clear `kind=needs_input` reason referencing the dry-run placeholder contract; (2) **dry-run placeholder-loop canonical contract** — dry-run fake loops MAY be registered at `status: live-pending-test` with a tag `kind:dry-run-placeholder` + `register_with_profile: doc-writer` + `run_with_profile: doc-writer` + `target_board: loop-builder` + a minimal `worker_prompt` clarifying the loop is not for production use; archive-after-master-close is the canonical termination path; (3) **paired placeholder intent doc** — every dry-run placeholder loop MUST ship a paired `~/.hermes/loops/intents/<loop-id>-intent.md` documenting the fixture contract (1-page stub: goal + scope + stop-condition + archive-after-master-close); the doc MUST be a stub (no Phase-V-HITL-Retro structure) since the loop never fires end-to-end. Companion intent doc: `~/.hermes/loops/intents/graph-audit-promotion-intent.md` v0.9.2 (paired-wiki mirror per op-guard-5). Companion concept note: `~/Documents/HermesVault/2-ATOMIC/concepts/graph-audit-promotion-dry-run-placeholder-contract-v0-9-2-2026-08-26.md`. Companion helper patch + test patches ship via coder follow-up per op-guard-16 spec-first-flow + op-guard-21 chat-side orchestrator-only (the doc-writer ships the doc-side contract; the coder ships the env mutation per the contract).

- **`amend-124 (verifier-chain parent-edge gate, source `t_f66a7b5e`)** — per the V-of-V FAIL finding (`t_fac38a2d`) + op-guard-32 v3 gate-order enforcement layer. The dispatcher's parent-edge gate previously fired on `tasks.status = done` alone, but verifier-chain members (V-1..V-of-V) emit `result = "compliance: fail"` while still `status = done`. The amend-124 extends the parent-edge gate to require `parent.status == done AND parent.result starts_with "compliance: pass"` for verifier-chain children; on FAIL, the child blocks on `kind=dependency` with the canonical reason `gate-order violation: V-(N-1) emitted <result>; verifier chain requires compliance: pass`. Retro-A inherits the same gate (parent V-of-V must emit `compliance: pass`); Retro-H inherits a sibling gate (parent Retro-A must complete normally via `closed_per_decision`, NOT `closed_in_error`). The new Field 5b captures the canonical contract; the companion dispatcher patch + 4-case regression suite at `~/.hermes/tests/test_dispatcher_verifier_chain_gate.py` v0.1.0 ship via coder follow-up per op-guard-16 spec-first-flow + op-guard-17 alice-first/instance-second/compliance-gate. Source ticket: `t_f66a7b5e`. Companion intent doc: `~/.hermes/loops/intents/graph-audit-promotion-intent.md` v0.9.3 (paired-wiki mirror per op-guard-5). Companion concept note: `~/Documents/HermesVault/2-ATOMIC/concepts/graph-audit-promotion-verifier-chain-parent-edge-gate-v0-9-3-2026-08-26.md`.
- **`amend-125 (canonical `## Audit run` schema for the per-loop audit ticket body, source `t_5e1a285c`)** — per the doc-writer per_loop_audit child FAIL-loop surfaced in Retro-A (`t_ce82ce87`) + V-of-V (`t_fac38a2d`). The prior pattern had the doc-writer posting the audit tool output as a `kanban_comment` on the master, which the verifier chain (V-1..V-of-V) couldn't grade because it reads the per-loop audit ticket body, NOT the master's comment thread. The amend-125 codifies the canonical `## Audit run` schema: the doc-writer lane MUST commit the `## Audit run` section to the per-loop audit ticket body regardless of `loop_live_audit.py` exit_code; `aborted` is a valid result with a populated 9-item scoring table derived from the audit tool's stdout/stderr (per the amend-125 stderr → per-item FAIL mapping table — `unknown loop-id '<X>'` → item 1 = FAIL, items 2-9 INHERIT); the schema carries verbatim stdout/stderr + Tool + Captured at + Target loop + Result + score table + Score. Field 3 (Outputs) gains the schema reference + a NEW sub-section "## Audit run schema (v0.9.4 amend-125)"; Field 4 (Success criteria first bullet) + Part 4 step 2 (Audit node sentence) updated to reflect the canonical contract (the prior "Output is committed to the master ticket as a `## Audit run` section" sentence was the doc-writer bug and is corrected to "Output is committed to the per-loop audit ticket body"). Source ticket: `t_5e1a285c`. Companion intent doc: `~/.hermes/loops/intents/graph-audit-promotion-intent.md` v0.9.4 (paired-wiki mirror per op-guard-5 — ships via coder follow-up per op-guard-16). Companion concept note: `~/Documents/HermesVault/2-ATOMIC/concepts/graph-audit-promotion-doc-writer-audit-run-schema-v0-9-4-2026-08-26.md` (NEW; ships via this ticket). Companion env patch: `~/.hermes/profiles/doc-writer/SOUL.md` + `~/.hermes/skills/autonomous-ai-agents/jarvis-coordinator/SKILL.md` doc-writer ticket body template patched to require the `## Audit run` section per the amend-125 schema — ships via coder follow-up per op-guard-16 spec-first-flow + op-guard-21 chat-side orchestrator-only. Companion tool patch: `~/.hermes/tools/loop_live_audit.py` v0.2.0 — emit JSON envelope `{exit_code, error, items, score}` on errors (currently exits 1 with `unknown loop-id` stderr without emitting a JSON envelope; the v0.2.0 tool patch makes the abort output structured so the amend-125 stderr → per-item FAIL mapping is deterministic) — ships via coder follow-up. Compliance-verifier child is the done-gate per op-guard-17.

## Part 9: Audit-line

`## [2026-08-17T13:25Z] doc-writer-shipped — methodology/M-decide-graph-audit-promotion-flow.md v0.1.0 written. Codifies the graph-audit-promotion flow per the 6-field template (04d-decide-flow-spec.md) + the 4-stage HITL gates (op-guard-32) + the 3-node-type topology (Scripts/Code + Agents + Human, per 04a-decide-work-graph.md Part 4). Per-loop action: PROMOTE (evidence + compliance-verifier chain per op-guard-30); DEFER (per-loop deferral ticket with closed_per_decision); ARCHIVE (per-loop archive ticket). Audit-line discipline per op-guard-11. Source: t_3b49e1bc (operator 2026-08-17: "we have a loop builder board. Let's follow our own spec and create a flow on the board which will do the audits and promotion").`

`## [2026-08-18T17:40Z] doc-writer-shipped — methodology/M-decide-graph-audit-promotion-flow.md v0.1.0 → v0.2.0 (amend-77). 3 corrections: (A) 1 graph per run (not all registered loops); master ticket MUST carry `target_loop_id: <loop-id>` binding, missing binding → blocked kind=needs_input per op-guard-28 + clarify invocation. (B) Verifier profile (`/Users/homestead/.hermes/profiles/verifier/SOUL.md`) is the canonical Compliance-verifier node; the verifier's `--result "compliance: pass"` is the gate that flips YAML on PROMOTE per op-guard-17 + op-guard-30. (C) HITL #3 (Compliance) flipped from operator (Human node) to verifier (Agent node); operator owns HITL #1, #2, #4 only; HITL #3 is the verifier's PASS/FAIL via --result. Companion intent doc bumped to v0.3.0 (paired-wiki per op-guard-5); regression suite v0.3.0 with 4 new test cases. Source: t_0205edd9 (operator 2026-08-18 chat). Per `M-decide-graph-readiness.md` v0.1.1 Part 4 §D: 1 graph per run is canonical; verifier-as-HITL#3 enforces the doc-writer/operator/verifier separation of labor.`

`## [2026-08-18T21:00Z] doc-writer-shipped — methodology/M-decide-graph-audit-promotion-flow.md v0.2.0 → v0.3.0 (amend-82). Applies the op-guard-32 v4 amend (3-stage HITL pattern) to the actual flow. Replaces "fires the canonical 4-stage HITL gates" with "fires the canonical 3-stage HITL gates" throughout. Drops HITL #2 (intent-achievement) — the verifier (HITL #3) implicitly confirms intent-achievement via the compliance check; the operator does NOT need a separate gate. The 2 operator gates are HITL #1 + HITL #4; the 1 verifier gate is HITL #3. Per-loop audit ticket body schema carries 3 decision_refs (HITL #1 + HITL #3 + HITL #4) instead of 4 — 2 operator `clarify-<timestamp>` decision_refs + 1 verifier `--result "compliance: pass"` decision_ref. Success criteria + Flow graph + Skip conditions table reflect the 3-stage pattern. The Flow graph now shows HITL #1 (CONFIRM) → HITL #3 (verifier) → HITL #4 (operator), with rewrites from HITL #1 REVISE re-firing HITL #1 + HITL #3 (no HITL #2). The HITL #2 row is removed from the topology table. The verifier's modifications are visible to the operator before HITL #4 fires (per operator 2026-08-18: "maybe we just need HITL1 and 4"). Companion intent doc bumped to v0.4.0 (paired-wiki per op-guard-5); regression suite v0.4.0 with 5 new test cases for the 3-stage pattern. Source: t_5ed69888 (operator 2026-08-18: "these changes you are making, are they being applied to the graph audit? We would want these improvements to happen when we run the flow, not just for this specific run"). Per op-guard-32 v4 amend: the 3-stage pattern is the canonical flow going forward. Per-loop tickets filed under v0.3.0 (4-stage, with HITL #2) are grandfathered. Per op-guard-5 paired-wiki integrity: the methodology doc + intent doc + regression suite ship in the same change set.`

`## [2026-08-19T12:00Z] doc-writer-shipped — methodology/M-decide-graph-audit-promotion-flow.md v0.3.0 → v0.4.0 (amend-85). Applies the operator's 2026-08-18 corrections from the master-ticket summary PDF Mermaid review. Replaces "fires the canonical 3-stage HITL gates" with "fires the canonical 2 HITL gates (HITL #1 + HITL #2)" throughout. Renumbers the final disposition gate from HITL #4 → HITL #2. Unnumbers the verifier compliance check (was HITL #3 in v0.3.0 amend-82; now the unnumbered automated gate between HITL #1 and HITL #2). Reorders the flow chain — HITL #1 → verifier compliance check → HITL #2 (only after verifier PASS). Connects the Rewrite node to the HITL #1 feedback flow (not directly to the operator) — the operator's HITL #1 REVISE / PARTIALLY-ACHIEVES feedback IS the trigger for Rewrite. Adds the corrected Mermaid diagram to Part 3. The Node-type discipline updates: Rewrite fires from the HITL #1 feedback edge (not from a direct operator edge); the verifier compliance check is unnumbered; HITL #2 (renumbered from HITL #4) is BLOCKED until verifier PASS per op-guard-32 v3 amend. Per-loop audit ticket body schema carries 2 operator `clarify-<timestamp>` decision_refs (HITL #1 + HITL #2) + 1 verifier `--result "compliance: pass"` decision_ref. Success criteria + Flow graph + Skip conditions table + Failure modes all reflect the 2-HITL pattern. Out-of-order HITL fire (HITL #2 firing before verifier PASS) blocks on `kind=dependency` per op-guard-32 v3. Companion intent doc bumped to v0.4.1 (paired-wiki per op-guard-5); regression suite v0.4.1 with new test cases for the 2-HITL pattern. Source: t_6621d4d3 (operator 2026-08-18 chat: "There are 2 HITL gates, not 3. We should also stop referring to this final HITL gate as HITL4, it is HITL2" + "HITL must run and pass before HITL4 [now HITL #2] is fired" + "The rewrite script/code block coming from the operator, where does this originate from? It should be connected to a HITL, not directly to the operator"). Per op-guard-32 v3 amend: the gate-order enforcement layer (HITL #2 cannot fire until HITL #1 has an APPROVE-with-no-feedback disposition) is the canonical order. The verifier profile (per op-guard-29) runs a preflight before starting the compliance audit, checking HITL #2's `decision_ref` is recorded AND HITL #1's `feedback_ref == "none"`. Per op-guard-5 paired-wiki integrity: the methodology doc + intent doc + regression suite ship in the same change set. Cross-references: `2-ATOMIC/rules/op-guard-32-four-stage-graph-hitl-2026-08-17.md` v3 amend (the gate-order rule); `~/.hermes/loops/intents/graph-audit-promotion-intent.md` v0.4.1 (companion intent doc); `~/.hermes/tests/test_graph_audit_promotion_loop.py` v0.4.1 (regression suite).`

`## [2026-08-19T13:00Z] doc-writer-shipped — methodology/M-decide-graph-audit-promotion-flow.md v0.4.0 → v0.5.0 (amend-87). Audit-cycle-complete marker. The operator approved the final HITL gate for the Audit Graph (chat 2026-08-19: "I approve the final HITL gate for the Audit Graph"). The 2-HITL audit cycle is closed: HITL #1 (scope/intent = intent v0.3.0 → v0.4.1) APPROVED 2026-08-18; verifier compliance check PASS at t_730a8eda 2026-08-19T11:30:00Z (op-guard-17 compliance-verifier PASS for amend-86 instance-conformance); HITL #2 (final disposition = operator PROMOTE approval 2026-08-19) APPROVED. The YAML `status: live` was already flipped at t_fd2ea7ea 2026-08-19T11:20:14Z (amend-86 PART 2). This amend-87 is the **certification stamp**: the methodology doc + intent doc + YAML entry + regression suite all conform to the canonical 2-HITL contract. No contract change — the v0.5.0 amendment propagates the audit-cycle-complete marker to alice-framework per op-guard-5 paired-wiki integrity. Companion intent doc bumped to v0.5.0 (paired-wiki mirror); YAML version 0.3.0 → 0.4.0 (paired-wiki instance-conformance per op-guard-17 — the YAML field tracks the intent-doc version per the canonical amendment chain); regression suite UNCHANGED at v0.4.1 (87/87 green; the audit cycle's contract was frozen at v0.4.1 amend-85). Source: t_3d2a6f70 (operator 2026-08-19). The operator's HITL #2 approval is captured as the `decision_ref` per op-guard-10 v2 §1.2 `closed_per_decision`; the per-loop disposition ticket body schema carries all 3 decision_refs (HITL #1 + verifier PASS + HITL #2). Paired-wiki: the alice-framework side mirrors the audit-cycle-complete marker; the instance-side verifier + YAML + status are already PASS / live; the next audit cycle (re-audit) is independent and re-enters the flow via the per-loop audit ticket pattern. Per-loop tickets filed under v0.4.0 amend-85 are grandfathered; the v0.5.0 amend-87 certification stamp applies forward from 2026-08-19.`

`## [2026-08-19T14:00Z] doc-writer-shipped — methodology/M-decide-graph-audit-promotion-flow.md v0.5.0 → v0.5.1 (verifier FAIL routes to original-profile via auto-filed `[Fix]` child ticket; amend-88, source t_6ccf4e4b). Part 4 step 5 (Compliance-verifier child) gains the canonical amend-88 contract: when the verifier emits `--result "compliance: fail"`, the flow auto-files a `[Fix] [M] [<loop-id>] Address verifier FAIL findings: <short reason>` child ticket assigned to the original profile (default: `doc-writer`); the body carries the FULL verifier FAIL comment (not just the summary), the FAIL summary, the recommended fix path, and the parent verifier ticket link. The auto-filed child is the canonical place for the fix — the operator does NOT manually file a follow-up. After the original-profile commits the fix, the verifier re-fires (3 attempts, 30s/2m/5m); HITL #2 stays BLOCKED until the verifier emits PASS. Field 5 (Retry parameters) "Compliance-verifier failures" bullet updated to the amend-88 contract (auto-file `[Fix]` child + verifier re-fires + 3rd-still-FAIL escape hatch to operator-action-dm cron per op-guard-13). Part 8 (Cross-references) gains the amend-88 line citing the source ticket + companion test suite (`~/.hermes/tests/test_verifier_fail_routes_to_doc_writer.py` v0.1.0, 5 cases) + companion script (`~/.hermes/tools/verifier_fail_route.py` v0.1.0) + companion intent doc (`~/.hermes/loops/intents/graph-audit-promotion-intent.md` v0.5.1 paired-wiki mirror per op-guard-5). Frontmatter version bumped v0.5.0 → v0.5.1; `updated:` field bumped 2026-08-19T13:00Z → 14:00Z; `amended_by_v0_5_1` added with source t_6ccf4e4b. Source: operator 2026-08-19 (chat, verbatim): "if the verifier fails, it needs to send the failure back to the doc-writer to address the issue". The amend-88 contract replaces the prior "re-fire the verifier child ticket (3 attempts, 30s/2m/5m); on third FAIL, route to operator-action-dm cron per op-guard-13" pattern that made the verifier wait for the operator to manually file a follow-up — fragile because the operator may forget or file in the wrong place. The amend-88 pattern closes the loop automatically: verifier FAIL → `[Fix]` child to original-profile → original-profile commits the fix → verifier re-fires → HITL #2 fires only after PASS. The third-still-FAIL escape hatch is preserved for genuinely unrecoverable FAILs. Per-loop tickets filed under v0.5.0 amend-87 are grandfathered; the amend-88 contract applies forward from 2026-08-19. Paired-wiki: companion intent doc bumped to v0.5.1; companion test suite + script ship via coder follow-up per op-guard-16 spec-first-flow (alice-first, instance-second); the compliance-verifier child ticket per op-guard-17 is the META's done-gate child per op-guard-17 instance-conformance. Cross-references: t_6ccf4e4b (this amendment's source ticket).`

## v0.6.0 amendment record (2026-08-19, source: t_a6701e2b, amend-91)

The v0.6.0 amendment codifies the canonical PDF generation step at HITL #2 (Disposition) per operator 2026-08-19 (chat, verbatim): *"I am not going to review a manually created PDF. It needs to be done by the flow itself. If the flow is incorrect, fix the flow"*.

### What changed

1. **Part 4 step 6 (NEW) — Master-ticket summary PDF generation.** When the verifier PASS lands + HITL #2 is ready to fire, the flow auto-generates the master-ticket summary PDF via the canonical helper `~/.hermes/tools/post_graph_audit_disposition_pdf.py` v0.6.0. The helper:
   1. Resolves the master ticket id + target loop id + per-loop audit ticket id + the loop slug (from `target_loop_id: <loop-id>` in the master body per v0.3.0 amend-77).
   2. Locates the master-ticket summary Markdown at one of the canonical paths OR auto-generates the 8-section executive-reporting shape from the per-loop audit ticket body + the target loop's intent doc. **Section 3 Mermaid is the TARGET LOOP's Mermaid, NOT the audit flow's Mermaid** (per the operator 2026-08-18 review).
   3. Renders the Markdown to PDF at the canonical operator-facing path `~/Documents/HermesVault/0-INBOX/<loop-slug>-master-ticket-summary.pdf` via `md_to_pdf.py`.
   4. Sends the canonical PDF as a Discord attachment via `send_dm_attachments` — **mandatory, fail-closed per op-guard-3**: a failed send blocks the master on `kind=needs_input` + the chat-side files a `[HUMAN ACTION] graph-audit-pdf-delivery-failed` child ticket to surface the failure to the operator-DM cron. The chat-side MUST NOT fire the HITL #2 `clarify` before Discord delivery succeeds.
   5. Posts a master-ticket comment that begins exactly with the literal `Graph audit disposition ready` prefix.
   6. Copies the PDF to the durable audit trail at `~/Documents/HermesVault/2-ATOMIC/decisions/<loop-slug>-master-ticket-summary.pdf`.
   7. Appends a one-line audit-line to `~/Documents/HermesVault/log.md` per op-guard-11 via `vault_log.append_audit_line`.
   8. The chat-side then fires `clarify` referencing the PDF as evidence (per the executive-reporting skill "PDF-first, then `clarify`" pattern + op-guard-24 + op-guard-28).

2. **Part 4 step 7 (renumbered from step 6).** Action node flips YAML `status:` + bumps intent doc `version:` + appends audit-line per the operator's HITL #2 disposition.

3. **Part 8 (Cross-references)** gains the amend-91 line citing the source ticket + companion helper + companion test suite + companion intent doc.

4. **Frontmatter** version bumped v0.5.1 → v0.6.0; `updated:` field bumped 2026-08-19T14:00Z → 15:00Z; `amended_by_v0_6_0` added with source t_a6701e2b; `amended_by_v0_6_0_companion_intent_doc` added; `amended_by_v0_6_0_companion_helper` added; `amended_by_v0_6_0_companion_test` added; `links:` gains `op-guard-21` (the rule the prior pattern violated).

### Why this amendment

The prior pattern had the chat-side manually creating the master-ticket summary PDF via `md_to_pdf.py` + `send_dm_attachments` when HITL #2 was ready to fire. That violated:

- **op-guard-21** (chat-side orchestrator-only — should not directly mutate operator-environment state)
- The operator's principle that the flow itself must own the artifact generation, not the chat-side (per the operator 2026-08-19 quote)

The amend-91 contract codifies the flow as the canonical surface: the flow auto-generates the PDF + delivers to Discord DM + posts the master-ticket comment + copies to the vault + appends the audit-line; the chat-side then fires `clarify` referencing the PDF as evidence. The chat-side cannot bypass the helper by manually calling `md_to_pdf.py` + `send_dm_attachments` — that violates op-guard-21 and the operator's principle.

### Forward compatibility

Per-loop tickets filed under v0.5.1 amend-88 (or any prior version) are grandfathered. The amend-91 contract applies forward from 2026-08-19.

## v0.6.1 amendment record (2026-08-19, source: t_9a0c1b88, amend-92)

The v0.6.1 amendment codifies the dynamic Mermaid generation from the target loop's intent doc prose per operator 2026-08-19 (chat, verbatim): *"The PDF is incorrect. The mermaid shows a single box that says 'Target Loop: ...'. The entire contents of the HITL2 PDF need to be about the graph that is being audited, not about the audit graph self. Need a durable solution"*.

### What changed

1. **`_extract_target_loop_mermaid` (the canonical Mermaid extractor in `~/.hermes/tools/post_graph_audit_disposition_pdf.py` v0.6.1).** The v0.6.0 extractor was a single regex (``mermaid`` block in the intent doc). The v0.6.1 extractor is a 3-step pipeline:
   1. **Hand-authored Mermaid (v0.6.0 preserved).** If the intent doc carries a ````mermaid`` fenced block, use it verbatim. Per-loop authors can still hand-craft a Mermaid diagram if they want.
   2. **Dynamic extraction from prose (v0.6.1 NEW).** Scan the intent doc for canonical graph-topology markers and render a Mermaid `graph TD` from the discovered elements:
      - **Phases**: H2-H4 `### Phase N — <name>` headings (H4 sub-phases like `#### Phase 1.0 — Sentinel-URL recognition` are picked up; sub-sub-headings like `#### Phase 1.4 — Anti-patterns`, `#### Phase N input set`, `#### Phase N stop condition` are filtered out by the reject-prefix list).
      - **Verifiers**: H2-H3 `### V-N` headings + inline `V-1 + V-2 + ... + V-of-V` mentions (the canonical 8-children dispatch in x-article-review lists V-N in prose, not as headings).
      - **HITL gates**: inline `HITL-X` mentions.
      - **Retro gates**: inline `Retro-X` mentions.
   3. **Labeled placeholder (no structure found).** When the intent doc is missing OR the prose has no extractable structure, render a Mermaid box that names the target loop + the reason ("no extractable Phases/Verifiers/HITL/Retro in intent doc prose") so the operator sees a Mermaid block, not a broken diagram.

2. **Edge topology.** Phases flow linearly (`P0 → P1 → P1.0 → ... → Pn`); each phase branches to a verifier (`P<i> → V<i>`); the last numeric verifier connects to `V-of-V`; `V-of-V` (when present) is the canonical "pre-HITL gate" (the automated compliance check); HITL gates flow linearly (`H_A → H_B → ...`); HITL gates lead to retros (`H_last → R_A → R_B`); retro-H (operator disposition) is the terminal node. The 4 node kinds are color-coded via `classDef` (phase=blue, verifier=green, HITL=yellow, retro=red).

3. **`_sanitize_mermaid_label` (Mermaid syntax safety).** The executive-reporting skill `references/mermaid-syntax-rules.md` mandates: simple alphanumeric IDs (no special chars), `<br/>` for line breaks (not `\n`), quoted labels (no `-->` in label text), no backticks. The sanitizer replaces `-->` with `→`, `\n` with `<br/>`, backticks/double-quotes with single quotes, and collapses internal whitespace.

4. **Backward-compatible.** The v0.6.1 helper handles 10+ loops in the registry: x-article-review (full Phase 0..5 + V-1..V-4 + V-of-V + HITL-A + Retro-A + Retro-H, all dynamic), alice-publish (placeholder — no extractable Phase/V/HITL/Retro structure), the 7+ remaining loops (placeholder), and any future loop with a hand-authored Mermaid block (verbatim). The v0.6.0 PDF generation pipeline (Markdown → PDF → Discord → comment → vault → audit-line) is UNCHANGED.

5. **Frontmatter** version bumped v0.6.0 → v0.6.1; `updated:` field bumped 2026-08-19T15:00Z → 15:30Z; `amended_by_v0_6_1` added with source t_9a0c1b88; `amended_by_v0_6_1_companion_intent_doc` / `_companion_helper` / `_companion_test` added.

### Why this amendment

The prior pattern extracted a Mermaid block from the target loop's intent doc via a single regex. Loops that authored the flow as prose (e.g. x-article-review uses `### Phase 0..4` + `### V-N` + `### Post-council Retro-A + Retro-H spawn` headings — no Mermaid block) got a labeled placeholder Mermaid. The operator's review caught the gap: the HITL #2 PDF rendered a placeholder Mermaid instead of the actual x-article-review flow, which meant the operator's PDF review was operating on a generic diagram, not the target loop's actual flow. The amend-92 contract codifies the durable fix: the dynamic extractor reads the intent doc's prose structure and renders the target loop's actual flow. Per-loop authors never have to write a Mermaid block by hand; the diagram always reflects the target loop's actual structure.

### Forward compatibility

Per-loop tickets filed under v0.6.0 amend-91 (or any prior version) are grandfathered. The amend-92 contract applies forward from 2026-08-19. Hand-authored Mermaid blocks (when present) continue to be used verbatim per the v0.6.0 behavior.

### Companion artifacts

- **Companion helper:** `~/.hermes/tools/post_graph_audit_disposition_pdf.py` v0.6.0 (NEW, modeled after `post_council_verdict_pdf.py` + `post_executive_report_pdf.py`; the canonical PDF generation + Discord delivery + vault copy + audit-line pipeline).
- **Companion test suite:** `~/.hermes/tests/test_graph_audit_disposition_pdf.py` v0.6.0 (NEW, 8-case regression suite: PDF generated + comment posted + Mermaid renders + idempotency + fail-closed + audit-line + slug derivation + walk-mode discovery).
- **Companion intent doc:** `~/.hermes/loops/intents/graph-audit-promotion-intent.md` v0.6.0 (paired-wiki mirror per op-guard-5).

`## [2026-08-19T15:00Z] doc-writer-shipped — methodology/M-decide-graph-audit-promotion-flow.md v0.5.1 → v0.6.0 (amend-91, source t_a6701e2b). Flow owns the canonical PDF generation step at HITL #2. Part 4 step 6 (NEW) — Master-ticket summary PDF generation: the flow auto-generates the master-ticket summary PDF via the canonical helper ~/.hermes/tools/post_graph_audit_disposition_pdf.py v0.6.0 when the verifier PASS lands + HITL #2 is ready to fire. The helper: resolves master ticket id + target loop id + per-loop audit ticket id + the loop slug (from target_loop_id: <loop-id> in the master body per v0.3.0 amend-77); locates the master-ticket summary Markdown at one of the canonical paths OR auto-generates the 8-section executive-reporting shape from the per-loop audit ticket body + the target loop's intent doc (Section 3 Mermaid is the TARGET LOOP's Mermaid, NOT the audit flow's Mermaid, per the operator 2026-08-18 review); renders the Markdown to PDF at the canonical operator-facing path ~/Documents/HermesVault/0-INBOX/<loop-slug>-master-ticket-summary.pdf via md_to_pdf.py; sends the canonical PDF as a Discord attachment via send_dm_attachments (mandatory, fail-closed per op-guard-3 — a failed send blocks the master on kind=needs_input + the chat-side files a [HUMAN ACTION] graph-audit-pdf-delivery-failed child ticket to surface the failure to the operator-DM cron; the chat-side MUST NOT fire the HITL #2 clarify before Discord delivery succeeds); posts a master-ticket comment that begins exactly with the literal "Graph audit disposition ready" prefix (parallel to "Council verdict ready" for x-article-review + "Retro-H executive report ready" for Retro-H); copies the PDF to the durable audit trail at ~/Documents/HermesVault/2-ATOMIC/decisions/<loop-slug>-master-ticket-summary.pdf; appends a one-line audit-line to ~/Documents/HermesVault/log.md per op-guard-11 via vault_log.append_audit_line. The chat-side then fires clarify referencing the PDF as evidence (per the executive-reporting skill "PDF-first, then clarify" pattern + op-guard-24 + op-guard-28). The operator's HITL #2 disposition (PROMOTE / DEFER / ARCHIVE) is captured as the clarify-<timestamp>-disposition decision_ref per op-guard-29 + op-guard-10 v2 §1.2. Part 4 step 7 (renumbered from step 6): Action node flips YAML status: + bumps intent doc version: + appends audit-line per the operator's HITL #2 disposition. Part 8 (Cross-references) gains the amend-91 line citing the source ticket + companion helper + companion test suite + companion intent doc. Frontmatter version bumped v0.5.1 → v0.6.0; updated: field bumped 2026-08-19T14:00Z → 15:00Z; amended_by_v0_6_0 added with source t_a6701e2b; amended_by_v0_6_0_companion_intent_doc added; amended_by_v0_6_0_companion_helper added; amended_by_v0_6_0_companion_test added; links: gains op-guard-21 (the rule the prior pattern violated). Source: operator 2026-08-19 (chat, verbatim): "I am not going to review a manually created PDF. It needs to be done by the flow itself. If the flow is incorrect, fix the flow". The amend-91 contract replaces the prior "chat-side manually creates the PDF" pattern that violated op-guard-21 (chat-side orchestrator-only) + the operator's principle. Per op-guard-5 paired-wiki integrity: the methodology doc + intent doc + helper + test suite ship in the same change set. Per op-guard-16 spec-first-flow: the doc-writer ships the methodology doc + intent doc; the coder ships the helper + test suite. Per op-guard-17 instance-conforms: the helper is the alice-first (per the x-article-review pattern); the instance is the per-loop master ticket that invokes the helper. Per op-guard-11 log-md-append-only: the audit-line is appended via vault_log.append_audit_line. Companion test: ~/.hermes/tests/test_graph_audit_disposition_pdf.py v0.6.0 (8-case regression suite). Companion helper: ~/.hermes/tools/post_graph_audit_disposition_pdf.py v0.6.0. Cross-references: t_a6701e2b (this amendment's source ticket); op-guard-21 (the rule the prior pattern violated); op-guard-3 (fail-closed per op-guard-3 for the mandatory Discord delivery).`

---

## v0.9.0 amendment record (2026-08-23, source: t_b647f9a6, amend-102)

The v0.9.0 amendment codifies the canonical deterministic file-time dispatch contract for the 8 children of a graph-audit-promotion master ticket per operator 2026-08-23 (chat, verbatim): *"this is still LLMs deciding what tickets to make. Since the graph is defined, we should create all tasks programatically"*.

### What changed

1. **Part 4 step 1 (Master ticket)** — appended the canonical deterministic dispatch invocation: once the master is filed, the chat-side invokes `~/.hermes/scripts/file_graph_audit_children.py` v0.1.0 (per v0.9.0 amend-102) which files all 8 children (per_loop_audit + V-1..V-of-V + Retro-A + Retro-H) deterministically. The chat-side LLM has NO role in ticket structure + gate sequence + lane assignments — those are read from the canonical contract (this methodology doc + the intent doc), not improvised. The helper is the analog of `~/.hermes/tools/file_x_article.py v0.9.15 _dispatch_production_children`: both are file-time-deterministic 8-children dispatch helpers.
2. **Companion script (`~/.hermes/scripts/file_graph_audit_children.py` v0.1.0) — NEW.** The canonical deterministic dispatch helper. Reads the master ticket body for `target_loop_id: <loop-id>` (per the v0.2.0 amend-77 binding) + builds the canonical 8-children plan + parent-edge chain (deterministic: master → per_loop_audit → V-1 → V-2 → V-3 → V-4 → V-of-V → Retro-A → Retro-H) + lane assignments (doc-writer owns per_loop_audit + retro_a; verifier owns V-1..V-of-V; jarvis owns retro_h) + bodies (deterministic templates per child kind) + initial-status (running for all 8). Fires one `hermes kanban create` subprocess per child in deterministic order.
3. **Companion test (`~/.hermes/tests/test_file_graph_audit_children.py` v0.1.0) — NEW.** 3-case regression suite: (a) alice-publish — 8-children chain + lane assignments + parent edges; (b) x-article-review — same chain for a different target (helper is target-loop agnostic per the methodology doc Part 4 step 1: 1 graph per run); (c) idempotency — the `--idempotency-key` flag is canonical `graph-audit-promotion/<master_id>/<kind>` so re-running on the same master is idempotent.
4. **Idempotency.** Each child carries `--idempotency-key graph-audit-promotion/<master_id>/<kind>` so re-running on the same master returns the existing ticket ids instead of creating duplicates. Verified by case_03_idempotency.
5. **Aggregate-fail contract.** Per the file_x_article.py amendment 30 hard-fail pattern (t_36d6f5d7): on per-child subprocess failure, the helper sets a `<FAILED:{kind}>` placeholder in the parent-edge resolver so the next iteration's parent-edge lookup does NOT crash with KeyError. After the loop completes, an aggregate-fail check raises `ChildDispatchError` listing every failed child kind + its per-child stderr. `_main_wrapper` exits non-zero (exit 7).
6. **Part 8 (Cross-references) gains the amend-102 line citing the source ticket + companion helper + companion test suite.**
7. **Frontmatter** version bumped v0.8.0 → v0.9.0; `updated:` field bumped 2026-08-19T18:30Z → 2026-08-23T18:30Z; `amended_by_v0_9_0` added with source `t_b647f9a6`.

### Why this amendment

The prior pattern had the chat-side / dispatcher (LLM) deciding ticket structure at master-worker-spawn-time: which children to file, in what order, with what parent edges, what lane assignments, what bodies. The graph is defined (per this methodology doc + the 9-item rubric + the 2-HITL pattern); the LLM's decisions were non-deterministic + error-prone (per the verified pattern in x-article-review where the orchestrator's LLM dropped the `--body` flag from the dispatch argv on 4 production runs, t_0478f49f + t_a4364469 + t_3b779d96 + t_3b779d96 run-4).

The v0.9.0 amend-102 codifies the durable fix: the dispatch is a code path, NOT an LLM interpretation. The helper reads the canonical contract (Part 4 step 1 + the intent doc's "How the operator triggers it" section) + fires the 8 children deterministically. The chat-side's LLM has NO role in ticket structure + gate sequence + lane assignments — those are read from the contract, not improvised. The x-article-review companion (`~/.hermes/tools/file_x_article.py v0.9.15 amend-21`) is the analog: the helper pre-builds the canonical 8-children dispatch as a code path, the orchestrator's LLM only owns body access + body update + synthesis (LLM work).

### Forward compatibility

Existing per-loop tickets filed under any prior intent-doc / methodology-doc version are grandfathered. The v0.9.0 amend-102 deterministic child-creation contract applies forward from 2026-08-23. Master tickets filed before 2026-08-23 that have already been dispatched (children filed via the prior LLM-driven pattern) are NOT re-dispatched; the idempotency-key prevents double-dispatch.

### Companion artifacts

- **Companion script:** `~/.hermes/scripts/file_graph_audit_children.py` v0.1.0 (NEW, deterministic dispatch helper; mirror of `file_x_article._dispatch_production_children` v0.9.15 amend-21 contract).
- **Companion test suite:** `~/.hermes/tests/test_file_graph_audit_children.py` v0.1.0 (NEW, 3-case regression suite: alice-publish + x-article-review + idempotency).
- **Companion intent doc:** `~/.hermes/loops/intents/graph-audit-promotion-intent.md` v0.9.0 (paired-wiki mirror per op-guard-5).

### Audit-line per op-guard-11

```
## [2026-08-23T<ship-timestamp>Z] doc-writer-shipped — methodology/M-decide-graph-audit-promotion-flow.md v0.8.0 → v0.9.0 (amend-102, source t_b647f9a6). File-time dispatch via ~/.hermes/scripts/file_graph_audit_children.py v0.1.0; 8 children + parent-edge chain + lane assignments + bodies all derived from the canonical contract (no LLM); idempotent via canonical --idempotency-key format graph-audit-promotion/<master_id>/<kind>. Part 4 step 1 amended to invoke the helper. Companion intent doc v0.9.0 paired-wiki mirror per op-guard-5. Companion test suite v0.1.0 3-case regression (alice-publish + x-article-review + idempotency) all green.
```

---

## v0.9.1 amendment record (2026-08-26, source: t_d132ad31, amend-122)

The v0.9.1 amendment codifies the canonical dispatcher-integration contract per operator 2026-08-26 (chat, verbatim): *"Since we have a graph for updating graphs, we should be using that to fix the audit graph instead of you filling tickets directly for agents to work."*

### What changed

1. **Part 4 step 1 (Master ticket)** — appended the canonical-path normalization language. The dispatcher invokes the canonical `_canonical_path_from_body(body)` helper to recover the `path` column from the body annotation when the `--path` flag is omitted. The helper is whitespace-tolerant + handles trailing-suffix recovery (`path: graph-audit-promotion-v6-amend-117` → `graph-audit-promotion`). The 4 modes of operator body annotation (Mode A: `--path` matches body; Mode B: `--path` carries trailing suffix; Mode C: `--path` omitted + body carries annotation; Mode D: both omitted) are documented explicitly.

2. **Part 7 (Application procedure) — gains the "Dispatcher-integration contract (v0.9.1 amend-122)" sub-section.** Codifies the 4 modes of operator body annotation that the dispatcher handles deterministically + the fallback ladder (post-create hook fires → deterministic 8-children dispatch → master closed with audit-line; LLM worker_prompt only fires when the hook is bypassed or reports hard-fail). The companion regression suite `~/.hermes/tests/test_graph_audit_dispatcher_fires.py` v0.9.1 covers the 4 dispatcher-bypass failure modes.

3. **Part 8 (Cross-references) — gains 6 lines:** (a) op-guard-16 spec-first-flow (the doc-writer is spec-only; the v0.9.1 paired-wiki mirrors ship via phase 1+2; the dispatcher patch + 2 regression suites ship via coder follow-up tickets phase 3; the compliance-verifier child is phase 4), (b) op-guard-17 alice-first / instance-second (this methodology doc v0.9.1 is the alice-first; the intent doc v0.9.1 + the dispatcher patch are the instance-second; the compliance-verifier child is the done-gate), (c) op-guard-19 pre-verify-artifact-state (the v0.9.1 paired-wiki mirrors apply op-guard-19 to both files), (d) `~/.hermes/skills/autonomous-ai-agents/hermes-loop-validate/SKILL.md` (the canonical end-to-end loop validation skill; consistent with the 7-phase validation discipline), (e) amend-122 line citing the source ticket + companion concept note + paired-wiki intent doc mirror + dispatcher patch + 2 regression suites.

4. **Frontmatter** version bumped v0.9.0 → v0.9.1; `updated:` field bumped 2026-08-23T18:30Z → 2026-08-26T11:37Z; `amended_by_v0_9_1` added with source t_d132ad31; `amended_by_v0_9_1_source` added with operator verbatim quote.

5. **6 corrections** codified as the amend-122 invariant set (mirrors the intent doc):
   - **Invariant 1:** single source of truth for the `path` column (write-time backfill recovers body annotation when `--path` flag omitted).
   - **Invariant 2:** whitespace-tolerant parser + trailing-suffix recovery.
   - **Invariant 3:** post-create hook fires iff canonical_path resolves to a registered loop id.
   - **Invariant 4:** LLM worker_prompt is a fallback, NOT a peer.
   - **Invariant 5:** per_loop_audit child verifies the dispatcher fired within 10s.
   - **Invariant 6:** audit-line per op-guard-11 captures the dispatch outcome.

### Why this amendment

The v0.9.0 amend-102 established the deterministic 8-children dispatch (per `~/.hermes/scripts/file_graph_audit_children.py` v0.1.0) but did not codify the seam between the dispatcher's post-create hook + the write-time backfill + the LLM worker_prompt fallback. Two observable failure modes emerged in production (verified at `t_325a7e2a` alice-publish master, v5: silent-fallback mode + hook-bypass mode). Both modes produced the same observable symptom — *HITL #1 never fires on the first child*. The amend-122 fixes the seam: the canonical resolution handles trailing-suffix recovery, the fallback ladder documents when the LLM is allowed to spawn, and the per_loop_audit child verifies the dispatcher fired (the 10-second assertion is the gateway that surfaces the dispatcher-bypass to the operator-DM cron per op-guard-13).

### Forward compatibility

Per-loop tickets filed under v0.9.0 amend-102 (or any prior version) are grandfathered. The v0.9.1 amend-122 dispatcher-integration contract applies forward from 2026-08-26. Master tickets filed before 2026-08-26 that have already been dispatched (children filed via the prior LLM-driven pattern) are NOT re-dispatched; the idempotency-key prevents double-dispatch.

### Companion artifacts

- **Companion concept note:** `~/Documents/HermesVault/2-ATOMIC/concepts/graph-audit-promotion-dispatcher-integration-v0-9-1-2026-08-26.md` (NEW; the canonical concept note for amend-122; paired-wiki source for the intent doc + methodology doc v0.9.1 mirror per op-guard-5).
- **Companion dispatcher patch:** `~/.hermes/hermes-agent/hermes_cli/kanban_db.py` (the canonical `_canonical_path_from_body` helper at line 2525 + the canonical resolution patch at line 4673). Ships via coder ticket per op-guard-21.
- **Companion test suite (1):** `~/.hermes/tests/test_extract_flow_path_normalization.py` v0.9.1 (NEW; 6-case regression suite: exact match, `-v6` suffix, `-v6-amend-117` suffix, leading whitespace, trailing whitespace, no `path:` annotation).
- **Companion test suite (2):** `~/.hermes/tests/test_graph_audit_dispatcher_fires.py` v0.9.1 (NEW; 4-case regression suite: exact-path pass, suffix-path pass via fallback parser, body-without-flag pass, hook-bypass failure modes).
- **Companion intent doc:** `~/.hermes/loops/intents/graph-audit-promotion-intent.md` v0.9.1 (paired-wiki mirror per op-guard-5 + op-guard-17 instance-conformance; this ticket's paired-wiki mirror).
- **Compliance-verifier child ticket:** the per_loop_audit doc-writer child inspects the master's `task_events` for the canonical `[graph-audit-promotion] dispatched 8 children deterministically` comment within 10 seconds of master creation. The dispatcher-fired assertion is the gateway between the dispatcher's post-create hook firing and HITL #1 firing on the first child; missing assertion → `kind=dependency` block + operator-DM cron surface per op-guard-13.

### Audit-line per op-guard-11

```
## [2026-08-26T11:37Z] doc-writer-shipped — methodology/M-decide-graph-audit-promotion-flow.md v0.9.0 → v0.9.1 (amend-122, source t_d132ad31). Dispatcher-integration contract codified: 4 modes of operator body annotation (Mode A: --path matches body; Mode B: --path carries trailing suffix; Mode C: --path omitted + body carries annotation; Mode D: both omitted); canonical resolution via _canonical_path_from_body helper (whitespace-tolerant + trailing-suffix recovery); post-create hook fires iff canonical_path resolves; LLM worker_prompt is a fallback, NOT a peer; per_loop_audit child verifies the dispatcher fired within 10 seconds of master creation; audit-line per op-guard-11 captures dispatch outcome. Part 4 step 1 amended with canonical-path normalization language; Part 7 gains the dispatcher-integration contract sub-section; Part 8 gains 6 cross-reference lines (op-guard-16 + op-guard-17 + op-guard-19 + hermes-loop-validate skill + amend-122 line + paired-wiki concept note); frontmatter version bumped v0.9.0 → v0.9.1; updated: field bumped 2026-08-23T18:30Z → 2026-08-26T11:37Z; amended_by_v0_9_1 added with source t_d132ad31. Companion intent doc v0.9.1 paired-wiki mirror per op-guard-5. Companion dispatcher patch + 2 regression suites ship via coder follow-up per op-guard-16 spec-first-flow. Source: t_d132ad31 (operator 2026-08-26: "Since we have a graph for updating graphs, we should be using that to fix the audit graph instead of you filling tickets directly for agents to work").
```


## v0.9.2 amendment record (2026-08-26, source: t_cfe657f4 via doc-writer per_loop_audit, amend-123)

The v0.9.2 amendment codifies the **target-loop pre-flight check** + **dry-run placeholder-loop canonical contract** per the DRY-RUN master ticket `t_db5cf6da` finding (per the per_loop_audit child `t_cfe657f4`).

### What changed

1. **Part 7 — gains the "Target-loop pre-flight + dry-run placeholder-loop contract (v0.9.2 amend-123)" sub-section.** Codifies the pre-flight check (`~/.hermes/scripts/file_graph_audit_children.py` MUST pre-flight-verify the target loop is registered in `~/.hermes/loops/hermes.yaml` BEFORE dispatching `per_loop_audit` children; on missing registration, the helper returns rc=2 with a clear `kind=needs_input` reason referencing the dry-run placeholder contract), the canonical dry-run placeholder-loop YAML fields (status: live-pending-test, tags: [kind:dry-run-placeholder], minimal worker_prompt, no trigger), and the paired stub intent doc contract (1-page stub: goal + scope + stop-condition + archive-after-master-close; NO Phase-V-HITL-Retro structure).

2. **Part 8 (Cross-references) — gains the amend-123 line** citing the source ticket (`t_cfe657f4` via doc-writer per_loop_audit child) + companion intent doc + companion concept note + companion helper + test patches ships via coder follow-up per op-guard-16 spec-first-flow + op-guard-21 chat-side orchestrator-only.

3. **Frontmatter** version bumped v0.9.1 → v0.9.2; `updated:` field bumped 2026-08-26T11:37Z → 2026-08-26T15:40Z; `amended_by_v0_9_2` added with source `t_cfe657f4`; `amended_by_v0_9_2_source` added with the doc-writer's amend-123 amendment record; `amended_by_v0_9_2_companion_intent_doc` added.

### Why this amendment

The pre-v0.9.2 flow had a missing edge in the per-loop audit pre-flight: the canonical helper `~/.hermes/scripts/file_graph_audit_children.py` (per v0.9.0 amend-102) dispatches the 8 children (per_loop_audit + V-1..V-of-V + Retro-A + Retro-H), and the per_loop_audit doc-writer child runs `python3 ~/.hermes/tools/loop_live_audit.py --loop-id <target_loop_id> --format json` — but the helper did NOT pre-flight-verify the target loop was registered in `~/.hermes/loops/hermes.yaml` before dispatching. The audit tool's per-loop filter (`--loop-id`) walks `audit_all()["loops"]`; if no entry matches, the tool exits 1 with `# error: unknown loop-id '<target_loop_id>'`. The flow appears to fail at the audit step, but the actual cause is upstream: the target loop was never registered.

The DRY-RUN master ticket `t_db5cf6da` (per the operator's 2026-08-26 chat verbatim: *"let's verify planner is working first, then dry run graph audit"*) caught this exact edge: the operator's intent was to validate the dispatcher + script + middleware end-to-end before running the real alice-publish v6; the flow's dispatcher→script chain works (per the op-guard-dispatcher comment on the master at 2026-08-26T10:30:12Z, the helper was invoked + emitted the canonical rc=2 pre-flight message); the helper's "loop must be registered" pre-check is the actual gate catching the dry-run.

The amend-123 codifies this pre-flight as a normative check (not just a tool-internal error) + the dry-run placeholder-loop canonical contract that an operator can register to enable the dry-run without re-scoping to a real loop. The contract is the durable fix: every future dry-run uses the same canonical pattern (placeholder loop entry + paired stub intent doc + archive-after-master-close termination), and the pre-flight check surfaces the missing-registration case with a clear `kind=needs_input` reason that the chat-side can surface to the operator-DM cron per op-guard-13 + the operator-action-dm cron.

### Companion artifacts (ships via coder follow-up per op-guard-16 + op-guard-21)

- **Companion intent doc:** `~/.hermes/loops/intents/graph-audit-promotion-intent.md` v0.9.2 (paired-wiki mirror per op-guard-5; v0.9.2 reflects the dry-run placeholder-loop contract).
- **Companion concept note:** `~/Documents/HermesVault/2-ATOMIC/concepts/graph-audit-promotion-dry-run-placeholder-contract-v0-9-2-2026-08-26.md` (NEW; the canonical concept note for amend-123; paired-wiki source for the intent doc + methodology doc v0.9.2 mirror).
- **Companion helper patch:** `~/.hermes/scripts/file_graph_audit_children.py` v0.9.2 (NEW pre-flight check in `_main_wrapper` — pre-flight-verify target_loop_id is registered; on failure return rc=2 with the amend-123 reason string). Ships via coder ticket per op-guard-21.
- **Companion test suite:** `~/.hermes/tests/test_file_graph_audit_children.py` v0.9.2 (NEW; 6-case regression suite: registered loop passes pre-flight; unregistered loop returns rc=2; rc=2 reason string matches amend-123; placeholder loop canonical-fields match contract; idempotency-key dedup; aggregate-fail on per-child failure). Ships via coder ticket.
- **Paired stub for the dry-run:** `~/.hermes/loops/intents/graph-audit-dry-run-test-loop-intent.md` (NEW; 1-page stub per the amend-123 paired-stub contract). Ships via coder follow-up.

### Audit-line per op-guard-11

```
## [2026-08-26T15:40Z] doc-writer-shipped — methodology/M-decide-graph-audit-promotion-flow.md v0.9.1 → v0.9.2 (amend-123, source t_cfe657f4 via doc-writer per_loop_audit child). Target-loop pre-flight check + dry-run placeholder-loop canonical contract codified: (1) canonical helper `~/.hermes/scripts/file_graph_audit_children.py` MUST pre-flight-verify target loop is registered in `~/.hermes/loops/hermes.yaml` BEFORE dispatching per_loop_audit children; on missing registration, returns rc=2 with clear kind=needs_input reason referencing the dry-run placeholder contract; (2) dry-run placeholder-loop canonical YAML fields codified (status: live-pending-test, tags: [kind:dry-run-placeholder], register_with_profile: doc-writer, run_with_profile: doc-writer, target_board: loop-builder, minimal worker_prompt, no trigger); (3) paired stub intent doc contract codified (1-page stub: goal + scope + stop-condition + archive-after-master-close; NO Phase-V-HITL-Retro structure). Part 7 gains the new sub-section; Part 8 gains the amend-123 line citing source ticket + companion intent doc + companion concept note + companion helper + test patches ships via coder follow-up per op-guard-16 spec-first-flow + op-guard-21 chat-side orchestrator-only; frontmatter version bumped v0.9.1 → v0.9.2; updated: field bumped 2026-08-26T11:37Z → 2026-08-26T15:40Z; amended_by_v0_9_2 added with source t_cfe657f4. Source: DRY-RUN master t_db5cf6da per_loop_audit child t_cfe657f4 (audit-tool exit=1 on unregistered loop-id per the operator's 2026-08-26 chat verbatim: "let's verify planner is working first, then dry run graph audit"). Coder follow-up tickets (per op-guard-16) ship the helper patch + test patches + paired stub for graph-audit-dry-run-test-loop-intent.md; compliance-verifier child is the done-gate per op-guard-17.
```

---

## v0.9.3 amendment record (2026-08-26, source: t_f66a7b5e, amend-124)

The v0.9.3 amendment codifies the **verifier-chain parent-edge gate** per the Retro-A re-anchor (`t_ce82ce87`) + V-of-V FAIL finding (`t_fac38a2d`) + op-guard-32 v3 gate-order enforcement layer.

### What changed

1. **Field 5b — Parent-edge gate for the verifier chain (NEW).** The dispatcher's parent-edge gate previously fired on `tasks.status = done` alone, but verifier-chain members (V-1..V-of-V) emit `result = "compliance: fail"` while still `status = done`. The new gate requires `parent.status == done AND parent.result starts_with "compliance: pass"`; on FAIL, the child blocks on `kind=dependency` with the canonical reason `gate-order violation: V-(N-1) emitted <result>; verifier chain requires compliance: pass`. Retro-A inherits the same gate (parent V-of-V must emit `compliance: pass`); Retro-H inherits a sibling gate (parent Retro-A must complete normally via `closed_per_decision`, NOT `closed_in_error`).

2. **Part 8 (Cross-references) — gains the amend-124 line** citing the source ticket + companion intent doc + companion concept note + companion dispatcher patch + companion test suite.

3. **Frontmatter** version bumped v0.9.2 → v0.9.3; `updated:` field bumped 2026-08-26T15:40Z → 2026-08-26T15:55Z; `amended_by_v0_9_3` added with source `t_f66a7b5e`.

### Why this amendment

The pre-v0.9.3 flow had a missing gate: the dispatcher's parent-edge resolver fired on `tasks.status = done` alone, which is correct for non-verifier children but incorrect for verifier-chain children (which emit `result = "compliance: fail"` while still `status = done`). The FAIL-loop pattern (V-of-V FAIL on identical evidence N times per the operator's Retro-A re-anchor) was caused by the missing gate — V-(N+1) was proceeding past a V-N FAIL because the gate accepted `status = done`. The amend-124 fixes this by extending the parent-edge gate to check `result starts_with "compliance: pass"` for verifier-chain children.

---

## v0.9.4 amendment record (2026-08-26, source: t_5e1a285c, amend-125)

The v0.9.4 amendment codifies the canonical `## Audit run` schema for the per-loop audit ticket body per the doc-writer lane amend-125 (parent Retro-A `t_ce82ce87`, master `t_db5cf6da`, V-of-V FAIL `t_fac38a2d`, fix ticket `t_5e1a285c`).

### What changed

1. **Field 3 (Outputs) — Audit run output line + per-loop audit ticket line updated.** The audit run output is committed to the per-loop audit ticket body (NOT just to a `kanban_comment` on the master) via the canonical `## Audit run` schema (per the new §"## Audit run schema (v0.9.4 amend-125)" sub-section below). An exit_code != 0 (unknown loop-id / missing intent doc / tool error) is itself a valid audit finding; the per-loop audit ticket body carries the JSON envelope so the verifier chain has a determinate output to grade. The per-loop audit ticket line gains the schema reference.

2. **Field 3 (NEW sub-section) — `#### ## Audit run schema (v0.9.4 amend-125)`.** The schema is the contract between the doc-writer lane (the producer) and the verifier chain (the consumer). The sub-section carries the canonical schema markdown, the 4 schema rules (commit regardless of exit_code; aborted is a valid result; verbatim stdout/stderr; score table populated from output), the `amend-125 stderr → per-item FAIL mapping table` (canonical mapping for unknown-loop-id / intent-doc-missing / yaml-unparseable / tool-internal-error / success cases), and a worked example for the unknown-loop-id abort case (the DRY-RUN fixture case from `t_db5cf6da` / `t_cfe657f4`).

3. **Field 4 (Success criteria) — first bullet updated.** "The audit run output is committed to the per-loop audit ticket body (NOT just to a `kanban_comment` on the master) via the canonical `## Audit run` schema (per v0.9.4 amend-125 §"## Audit run schema (v0.9.4 amend-125)"). The schema is committed regardless of exit_code; an `aborted` run still produces a populated per-item scoring table." This is the canonical invariant the doc-writer lane MUST satisfy; the verifier chain reads the per-loop audit ticket body to grade the audit.

4. **Part 4 step 2 (Audit node) — sentence expanded.** "Output is committed to the **per-loop audit ticket body** (NOT the master ticket, NOT a `kanban_comment`) as a `## Audit run` section per the canonical schema (per v0.9.4 amend-125 §"## Audit run schema (v0.9.4 amend-125)"). The schema is committed regardless of `loop_live_audit.py` exit_code; an `aborted` audit still produces a populated per-item scoring table. The doc-writer lane is responsible for the commit per amend-125 schema rules." This is the corrected Process step (the prior version committed to the master ticket, which was the doc-writer bug).

5. **Part 8 (Cross-references) — gains the amend-125 line** citing the source ticket + companion intent doc + companion concept note + companion env patch + companion tool patch.

6. **Frontmatter** version bumped v0.9.3 → v0.9.4; `updated:` field bumped 2026-08-26T15:55Z → 2026-08-26T16:05Z; `amended_by_v0_9_4` added with source `t_5e1a285c`; `amended_by_v0_9_4_source` added; `amended_by_v0_9_4_companion_intent_doc` added; `amended_by_v0_9_4_companion_concept_note` added; `amended_by_v0_9_4_companion_env_patch` added; `amended_by_v0_9_4_companion_tool_patch` added.

### Why this amendment

The doc-writer lane amend-125 fixes the FAIL-loop that surfaced in the DRY-RUN master ticket `t_db5cf6da` (per the per_loop_audit child `t_cfe657f4` which ran `python3 ~/.hermes/tools/loop_live_audit.py --loop-id graph-audit-dry-run-test-loop --format json` at 2026-08-26T10:34Z, got exit_code=1 with stderr `unknown loop-id 'graph-audit-dry-run-test-loop'`, posted the verbatim output as a `kanban_comment` on the master at 10:35, but did NOT commit a `## Audit run` section to the per-loop audit ticket body). Without the per-ticket `## Audit run` JSON, the verifier chain (V-1..V-of-V per Part 3) had no audit output to verify and emitted FAIL on identical evidence 5x (per the V-of-V `t_fac38a2d` FAIL + Retro-A `t_ce82ce87` re-anchor).

The amend-125 codifies the durable fix: the doc-writer lane MUST commit the `## Audit run` section to the per-loop audit ticket body regardless of exit_code; `aborted` is a valid result; the per-item scoring table is populated from the audit tool's stdout/stderr (not replaced by a "no score" line); the doc-writer lane maps stderr patterns to per-item FAIL via the amend-125 mapping table. The verifier chain now has a determinate output to grade, breaking the FAIL-loop.

### Companion artifacts (ships via coder follow-up per op-guard-16 + op-guard-17)

- **Companion intent doc:** `~/.hermes/loops/intents/graph-audit-promotion-intent.md` v0.9.4 (paired-wiki mirror per op-guard-5; v0.9.4 reflects the amend-125 audit-run schema).
- **Companion concept note:** `~/Documents/HermesVault/2-ATOMIC/concepts/graph-audit-promotion-doc-writer-audit-run-schema-v0-9-4-2026-08-26.md` (NEW; the canonical concept note for amend-125; paired-wiki source for the intent doc + methodology doc v0.9.4 mirror).
- **Companion env patch:** `~/.hermes/profiles/doc-writer/SOUL.md` + `~/.hermes/skills/autonomous-ai-agents/jarvis-coordinator/SKILL.md` doc-writer ticket body template patched to require the `## Audit run` section per the amend-125 schema. Ships via coder ticket per op-guard-16 spec-first-flow + op-guard-21 chat-side orchestrator-only.
- **Companion tool patch:** `~/.hermes/tools/loop_live_audit.py` v0.2.0 — emit JSON envelope `{exit_code, error, items, score}` on errors (currently exits 1 with `unknown loop-id` stderr without emitting a JSON envelope; the amend-125 doc-writer lane relies on the stderr → per-item FAIL mapping table to translate the abort output to a populated score table; the v0.2.0 tool patch makes the abort output structured so the mapping is deterministic rather than regex-derived). Ships via coder ticket.
- **Compliance-verifier child ticket:** the per-loop audit doc-writer lane reads the `## Audit run` section from the per-loop audit ticket body (NOT the master's `kanban_comment` thread); on missing `## Audit run` section, the verifier emits FAIL with the canonical reason `missing-audit-run-section` (per op-guard-17 + op-guard-30 evidence chain).

### Audit-line per op-guard-11

```
## [2026-08-26T16:05Z] doc-writer-shipped — methodology/M-decide-graph-audit-promotion-flow.md v0.9.3 → v0.9.4 (amend-125, source t_5e1a285c, parent Retro-A t_ce82ce87, master t_db5cf6da, V-of-V FAIL t_fac38a2d). Canonical `## Audit run` schema codified for the per-loop audit ticket body: doc-writer lane MUST commit the `## Audit run` section to the per-loop audit ticket body regardless of `loop_live_audit.py` exit_code; `aborted` is a valid result with a populated 9-item scoring table derived from the audit tool's stdout/stderr (per the amend-125 stderr → per-item FAIL mapping table); the schema carries verbatim stdout/stderr + Tool + Captured at + Target loop + Result + score table + Score. Field 3 (Outputs) + Field 3 (NEW sub-section "## Audit run schema (v0.9.4 amend-125)") + Field 4 (Success criteria first bullet) + Part 4 step 2 (Audit node sentence) updated to reflect the canonical contract; the prior "Output is committed to the master ticket as a `## Audit run` section" sentence (Part 4 step 2) was the doc-writer bug and is corrected to "Output is committed to the per-loop audit ticket body". Part 8 gains the amend-125 cross-reference; frontmatter version bumped v0.9.3 → v0.9.4; updated: field bumped 2026-08-26T15:55Z → 2026-08-26T16:05Z; amended_by_v0_9_4 + amended_by_v0_9_4_source + companion_intent_doc + companion_concept_note + companion_env_patch + companion_tool_patch added. Coder follow-up tickets (per op-guard-16) ship: (1) intent doc v0.9.4 paired-wiki mirror, (2) doc-writer SOUL.md + jarvis-coordinator SKILL.md env patch (require the `## Audit run` section per amend-125), (3) `~/.hermes/tools/loop_live_audit.py` v0.2.0 tool patch (emit JSON envelope on abort). Compliance-verifier child is the done-gate per op-guard-17. Source: t_5e1a285c doc-writer-lane amend per Retro-A re-anchor (doc-writer per_loop_audit child `t_cfe657f4` FAIL-loop).
```

## [2026-08-26T16:05Z] doc-writer-shipped — methodology/M-decide-graph-audit-promotion-flow.md v0.9.3 → v0.9.4 (amend-125, source t_5e1a285c, parent Retro-A t_ce82ce87, master t_db5cf6da, V-of-V FAIL t_fac38a2d). Canonical ## Audit run schema codified for the per-loop audit ticket body. See v0.9.4 amendment record section above for full detail.
