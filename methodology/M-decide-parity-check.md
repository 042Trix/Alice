---
id: alice-methodology-M-decide-parity-check
created: 2026-08-05T15:18:00Z
updated: 2026-08-11T23:30:00Z
title: "Methodology M — Decide parity check"
type: methodology
status: draft
source: alice-framework
tags: [kind:methodology, kind:parity, kind:meta, project:alice]
confidence: 0.0
links: ["[[methodology/00-decide-ticket-naming.md]]", "[[methodology/03-decide-agents.md]]", "[[methodology/M-decide-spec-first-flow.md]]", "[[templates/parity-check.md.template]]", "[[templates/spec-first-flow.md.template]]"]
promoted_from: _inbox/M-decide-parity-check.md
teaching-example: true
---

# Methodology M — Decide parity check

> Alice describes a working system, not an aspirational design. Before adding a new pattern to Alice, the doc-writer verifies the pattern against the **operator's current environment**. Patterns without operator evidence are deferred until the operator's environment actually does the thing.

## Part 1: Why parity matters

Without a parity check, Alice becomes clean-room methodology. A friend reading Alice applies the methodology, finds that the operator's working environment doesn't actually match Alice, and concludes Alice is "documentation that describes a dream, not a system." With parity, Alice is "documentation that describes a system that already works, and that other operators can apply."

The rule is generalizable: every methodology doc that names a real working pattern should be traceable to evidence in the operator's environment on the day the doc is filed. The rule for tickets that propose adding NEW behavior is harder — there is no operator evidence yet because the behavior doesn't exist. The parity check is what forces the doc-writer to either find the evidence or refuse to write the doc until the evidence exists.

## Part 2: The rule

**Every new ticket on the `alice-framework` board must include a "Parity check" section in the body.** The section answers one question:

> *What evidence in the operator's environment supports this pattern?*

Evidence comes from one of five places:

1. **Crons** — `hermes cron list` or `~/.hermes/cron/jobs.json`. A pattern like "the dispatcher polls every minute" is parity-supported when the operator's jobs.json has `dispatch-liveness-60s` at `*/1 * * * *`.
2. **Boards** — `hermes kanban boards list`. A pattern like "the operator has an agent-resources board" is parity-supported when the boards list shows `agent-resources` with `done > 0`.
3. **Skills** — `ls ~/.hermes/skills/`. A pattern like "skills have a 1-layer rule" is parity-supported when the operator's skills directory has the rule written down.
4. **Vault** — `~/Documents/HermesVault/2-ATOMIC/...` or `~/Documents/HermesVault/AGENTS.md`. A pattern like "the operator's vault uses a 4-tier Zettelkasten" is parity-supported when the vault's `AGENTS.md` (or any 2-ATOMIC note) describes that tiering.
5. **Cron output** — `~/.hermes/cron/output/...` or `0-INBOX/jarvis-pending-stuck-<date>.md`. A pattern like "the operator's stuck-sweep cron writes a NEEDS-ATTENTION file" is parity-supported when such a file is on disk.

The doc-writer's first action on every new ticket is to look for the evidence in those five places. The evidence either exists or it doesn't. **Aspirational patterns with no evidence must not be documented in Alice as if they were real.** They get a follow-up ticket to add the missing infrastructure first.

## Part 3: The three verdicts

Each parity check produces one of three verdicts:

1. **Parity-checked.** Evidence exists. The methodology doc describes the operator's existing pattern. The ticket proceeds normally.
2. **Partially supported.** Some aspects have evidence; others do not. The methodology doc describes the supported subset; the unsupported aspects get a follow-up ticket.
3. **Aspirational.** No evidence. The pattern is novel. The doc-writer posts a `kanban_comment` saying "no parity evidence found; infrastructure gap is X" and **does not write the methodology doc**. The ticket body is updated to "first add the missing infrastructure to the operator's environment, then re-file as an Alice methodology ticket." The 4-phase spec-first flow (`methodology/M-decide-spec-first-flow.md`) is the durable mechanism: phase 1 writes the doc, V-1 verifies it, phase 2 validates it, V-2 verifies the validation, phase 3 (assigned to a non-doc-writer agent) updates the environment, V-3 verifies the environment change, phase 4 closes the loop. Aspirational tickets carry explicit phase markers AND V markers per `templates/spec-first-flow.md.template`. The verifier steps are mandatory; the 7-step expansion of the 4-phase flow is the canonical application order.

The boundaries matter. Partially supported is not a free pass to document the full pattern — the doc should be honest about which parts are real and which are aspirational.

## Part 4: How to apply the rule

When filing a new ticket on `alice-framework`:

1. **Draft the ticket body** with a "Parity check" section per the template at `templates/parity-check.md.template`.
2. **Run the evidence search.** Use `hermes cron list`, `hermes kanban boards list`, `ls ~/.hermes/skills/`, and `grep` against the vault's `2-ATOMIC/` tree. Document the evidence (or its absence) in the section.
3. **Pick a verdict.** Parity-checked, partially supported, or aspirational.
4. **For aspirational tickets:** post a `kanban_comment` saying "no parity evidence found; deferring per methodology M-decide-parity-check.md. Filing a phase-3 follow-up infrastructure ticket." The phase-3 ticket is filed on the appropriate board (usually the system / operator / kanban board) — NOT on `alice-framework` — and **the doc-writer is NOT on its assignee list** (per `methodology/M-decide-spec-first-flow.md` Part 3). Update this ticket's body to a single line: "deferred; infrastructure gap being filled by ticket <id>." The 4-phase flow's phase markers carry the explicit phase-3 reference.
5. **For partially supported tickets:** document the supported subset in the methodology doc; cite the missing pieces in the body with explicit follow-up ticket ids.
6. **For parity-checked tickets:** proceed normally. Cite the evidence in the methodology doc.

**Important:** aspirational patterns are not "bad ideas." They are "ideas whose time has not yet come." The rule defers them, not them. The operator's environment is the source of truth; once the environment catches up, the pattern can be re-filed.

## Part 5: Worked examples

(These are the 10 alice-framework tickets originally filed 2026-08-05 from the operator's brain-dump. They ship already; this section documents the parity verdict after the fact, so future doc-writers applying the rule have a reference for what each verdict looks like in practice.)

**Parity-checked:**

- `HARNESS / agent creation rule` — operator's `agent-resources` board has 17+ done and a stable CRUD pattern. The "operator-on-request" path is real; new agents can be created via ticket. Evidence: `hermes kanban boards list` shows `agent-resources`; several `agent-*` titles in done.

**Partially supported:**

- `HARNESS / dispatcher polling exemption` — operator's `jobs.json` has `dispatch-liveness-60s` and `dispatch-supervisor` both at `*/1 * * * *`. The polling-exemption pattern is real. The "operator-can-create-but-shouldn't" rule is partly supported (the operator can manually file tickets); it is documented in vault notes.
- `HARNESS / work-graph metadata + 3-exit-flows` — operator's AGENTS.md says "substantive --result on completion" (required-metadata pattern is real). 3-exit-flows (success/fail/error per node) is aspirational; the kanban DB has 4 states but not explicit success/fail/error per task.
- `GRAPH / flow spec fields` — operator's loop YAML has `worker_prompt` blocks with structured `Goal` / `Bound` / `verify` / `constraints` / `stop_when` fields. The 6-field template adds `inputs`, `outputs`, `success criteria`, `retry parameters`, `escalation`; the operator's YAML covers Goal + Bound + verify + stop_when but does not have explicit `inputs`, `outputs`, `retry parameters`, `escalation` fields. Partially supported.
- `GRAPH / flow derivation` — pre-defined flows (the operator's `loops/hermes.yaml` is a registry of pre-defined flows) is real. Dynamic generation (flows that emit themselves at runtime) is aspirational; the operator does not have dynamic generation.

**Aspirational:**

- `HARNESS / spec hierarchy` — operator's `specs/` directory and the operator's brain-dump → ticket pipeline exist, but there is no explicit spec-hierarchy doc on the operator's side. The pattern is novel.
- `GRAPH / retro + feedback` — operator has no retro cron in `jobs.json`. No `0-INBOX/retro-*.md` files exist. The pattern is novel.
- `LOOP / feedback element + self-improvement` — operator's `06-iteration-loop.md` describes 5 elements (trigger, goal, action, evidence, stop rule); feedback is missing. Self-improvement (how feedback leads to a graph/flow/skill update) is a new mechanism; the operator's vault has no documented self-improvement loop.

For the 3 aspirational tickets, the fix is **not** to revise the methodology doc. The fix is to add the missing infrastructure to the operator's environment first. The follow-up tickets (filed on the appropriate board per operator's conventions) are the durable artifact; once they ship, the operator's environment catches up, and the methodology ticket can be re-filed with evidence.

The re-audit on 2026-08-05 maps each of the 10 backlog tickets to the 4-phase flow (`methodology/M-decide-spec-first-flow.md`). All 10 tickets are **phase 1 complete** (the methodology doc shipped); the 3 aspirational tickets carry explicit **phase 3 follow-ups** assigned to non-doc-writer profiles. Phase 4 verification (verifier confirms the environment change matches the doc) closes the loop; on pass, the doc's parity-check section moves from "aspirational" to "confirmed."

## Part 6: Anti-patterns

- **"Documenting the dream."** Writing a methodology doc for a pattern that doesn't yet exist in the operator's environment. Fix: file an infrastructure ticket first; defer the methodology doc until the infrastructure ships.
- **"Trust me, it's in there."** Claiming parity without citing specific evidence. Fix: every parity claim must name the file path, board name, cron id, or skill name it points to.
- **"Partial means full."** Treating a partially-supported ticket as fully parity-checked and documenting the aspirational parts as if they were real. Fix: the methodology doc explicitly calls out which parts are supported and which are aspirational.
- **"Asking the operator."** Asking the operator to verify the parity claim. Fix: the doc-writer reads the cron list, kanban boards, skills, and vault independently. Operator confirmation is not required for an evidence-based check.
- **"Closing the ticket anyway."** Closing an aspirational ticket by writing the doc anyway. Fix: aspirational tickets stay open (or get a phase-3 follow-up ticket added per `methodology/M-decide-spec-first-flow.md`) until the infrastructure gap is filled.
- **"Doc-writer on phase-3."** Adding the doc-writer to the phase-3 infrastructure ticket's assignee list. Fix: the doc-writer is spec-only; phase-3 is a non-doc-writer agent's responsibility.

## Part 7: Cross-references

- `methodology/00-decide-ticket-naming.md` — the AREA / topic-name / descriptive-name format that the parity-check section fits inside.
- `methodology/M-decide-spec-first-flow.md` — the 4-phase flow (`doc → validate doc → update environment → validate update`) that aspirational tickets use to close the infrastructure gap.
- `templates/parity-check.md.template` — the fillable template for the parity-check section.
- `templates/spec-first-flow.md.template` — the fillable template for phase markers; aspirational tickets carry an explicit phase 3.
- `META / doc-collision-prevention` (ticket `t_845591bf`) — the related meta-rule that governs per-file serialization; the parity-check rule is orthogonal (it checks evidence, not serialization).
- Operator source-of-truth examples: `~/.hermes/cron/jobs.json`, `~/Documents/HermesVault/AGENTS.md`, `~/Documents/HermesVault/2-ATOMIC/...`.

## Approval

- [ ] `templates/parity-check.md.template` exists and references this doc.
- [ ] `templates/spec-first-flow.md.template` exists and references `methodology/M-decide-spec-first-flow.md`.
- [ ] Every alice-framework ticket filed after this rule ships contains a Parity check section AND phase markers (phase 1/2/3/4 as applicable).
- [ ] The 10 backlog tickets have parity-check verdicts recorded (kanban comments + this doc's worked-example section) AND phase-tag re-audit comments (per the 4-phase flow).
- [ ] Aspirational tickets have phase-3 follow-up infrastructure tickets filed on the appropriate board; the doc-writer is NOT on the phase-3 assignee list.
- [ ] Phase-4 verification has been performed for each phase-3 follow-up; on pass, the doc's parity-check section moves from "aspirational" to "confirmed."
- [ ] The 5 anti-patterns (4 original + "doc-writer on phase-3") are documented and reference this doc by id.
