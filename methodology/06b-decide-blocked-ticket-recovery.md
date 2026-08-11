---
id: alice-methodology-06b-decide-blocked-ticket-recovery
created: 2026-08-06T08:00:00Z
updated: 2026-08-11T15:02:30Z
title: "Methodology 06b — Decide the blocked-ticket recovery procedure (the standing rule for stuck-ticket handling)"
type: methodology
status: draft
version: 0.1.0
source: alice-framework
tags: [kind:methodology, kind:recovery, kind:stuck-ticket, kind:state-cache, kind:helper-tool, project:alice]
confidence: 0.0
links: ["[[methodology/06-iteration-loop.md]]", "[[methodology/05-op-guards.md]]", "[[methodology/04b-decide-board-routing.md]]", "[[templates/AGENTS.md.template]]"]
promoted_from: _inbox/06b-decide-blocked-ticket-recovery.md
---

# Methodology 06b — Decide the blocked-ticket recovery procedure (the standing rule for stuck-ticket handling)

> A blocked-ticket recovery procedure is the **operational rule** that fires when the agent infrastructure (dispatcher, kernel state-cache, worker process) leaves a ticket in `blocked` or stuck-`running` state and no `kanban_complete` event is firing. This document is the canonical reference for the procedure. The chat-side agent (jarvis) owns proactive monitoring; the doc-writer owns the canonical rule; the operator owns disposition. The rule is non-trivial because the doc-writer profile's toolset does NOT include process-kill or dispatcher-control — the recovery cross-cuts profile boundaries.

The 06 doc describes the iteration loop primitive (detect → surface → act, with feedback). The 06a doc describes the retro (a kanban task written by a different agent on master-ticket flows). This doc describes the **post-failure recovery procedure** — what happens when a flow is stuck and the iteration loop's detect-leg has caught it. The recovery procedure is the *act* leg for stuck-ticket state, distinct from the chronic-strategy retro that fires at the end of a flow.

The recovery procedure is reactive, not proactive. It fires on a specific trigger ("a ticket has been blocked > 1h or stuck in `running` > 4h with no progress") and produces a specific outcome (rescue: file a child ticket, re-scope, or close as filed-in-error). It is NOT a operational guard because operational guards are one-paragraph constraints derived from 3+ observed failures; this procedure is a multi-step investigation and triage. It is a methodology, not a operational guard. It is also NOT a skill because the operator wants the durable record on disk, not a workflow the agent invokes; the procedure is a standing rule the agent reads on cold-start.

---

## Part 1: The trigger conditions

The recovery procedure fires when ANY of these is true on the `alice-framework` or `agent-resources` board:

| Condition | Threshold | Cadence |
|---|---|---|
| Ticket in `blocked` state | > 1 hour | check every 30 min |
| Ticket in `running` state with no heartbeat | > 4 hours | check every 30 min |
| Ticket in `running` state with stale heartbeat | > 2 hours since last heartbeat | check every 30 min |
| Ticket in `running` state with recent heartbeat but no completion | > 1 hour since heartbeat | check every 30 min |

The 30-min cadence is the minimum, not the maximum. If the chat-side agent is mid-conversation and the operator is asking questions, the cadence applies to the background check. If the agent is otherwise idle, the check can run more frequently.

The two boards (`alice-framework` and `agent-resources`) are the canonical scope. Other boards (`hermes`, `msaa-pipeline`, `lawnsvc`, `default`) have their own watchers and their own recovery procedures; this methodology is scoped to the two boards where the doc-writer and verifier profiles are the primary executors.

---

## Part 2: The investigation procedure

When a trigger fires, the chat-side agent (jarvis) runs the following investigation. This is a **read-only** procedure — no file writes, no state mutations, no tool calls that change the system. The output is a *decision* about which rescue action to take (Part 3).

### Step 1: Check the file system

Is the work shipped on disk? For each candidate stuck ticket, scan the ticket's body for the expected artifact path (e.g. `methodology/06b-decide-*.md`, `templates/AGENTS.md.template`, `references/board-routing.md`) and verify the file exists.

- **If the file exists** with non-empty content: the doc-writer did the work. The completion event is stuck in the kernel state-cache. Move to Step 2.
- **If the file does not exist** or is empty: the work is NOT shipped. Move to Step 2 (the doc-writer may still be working).

### Step 2: Check the doc-writer's process

Is the doc-writer process alive? Use `ps aux | grep <pid>` where `<pid>` is the `spawned.pid` from the most recent `event kind=spawned` row on the ticket.

- **If the process is alive**: the doc-writer may be mid-tool-call or about to log completion. Wait 5 minutes before re-checking. If after 5 minutes the completion event still has not fired, move to Step 3.
- **If the process is dead**: the doc-writer crashed without a terminal call. The work is shipped or not shipped depending on the Step 1 result. Move to Step 3.

### Step 3: Check the toolset limits

Does the doc-writer's toolset include the tools needed to recover? The doc-writer's toolsets are `file`, `kanban`, `memory`, `todo`, `session_search` — it has `kanban_complete` and `kanban_block` but NOT `process` (kill), NOT `cronjob`, NOT `messaging`. If the recovery requires one of the missing tools, the doc-writer cannot perform the recovery itself.

- **If the recovery requires a tool the doc-writer does not have**: file a follow-up ticket with `assignee` set to the profile that has the tool (e.g. `default` for kill+restart, `coder` for code change, `operator` for manual disposition). Link the candidate stuck ticket as a parent. The chat-side agent's job here is to file the follow-up, not to perform the recovery.
- **If the recovery is within the doc-writer's toolset**: proceed to Step 4.

### Step 4: Decide the rescue action

Based on Steps 1-3, pick ONE of the rescue actions in Part 3. Document the rationale in a `kanban_comment` on the candidate stuck ticket AND in the audit log (`~/Documents/HermesVault/log.md` per the audit-line contract).

---

## Part 3: The rescue actions

Four rescue actions, in priority order. Each one has a triggering condition and a concrete procedure.

### Action 1: Re-scope the ticket

**Trigger:** The work is shipped (Step 1 = yes) AND the original acceptance criteria cannot be met because the ticket's requirements were wrong (e.g. the ticket asks for process-kill, but the doc-writer's toolset doesn't have process-kill; the doc-writer cannot ship the original requirement).

**Procedure:**
1. Edit the ticket body to update the acceptance criteria (the new criteria are the rescue outcome, not the original ask).
2. Add a `kanban_comment` explaining the re-scope: cite the original criterion, the impossibility it ran into, and the new criterion.
3. Call `kanban_complete` with `--summary` describing the re-scoped outcome and `--metadata.changed_files` listing the artifacts that DID ship.
4. The ticket moves to `done`. The operator sees the re-scope in the audit trail.

**This is NOT a closure-without-evidence violation.** Re-scoping is a scope change, not a closure. The new acceptance criteria are real; the work that satisfies them is shipped.

### Action 2: Close as filed-in-error

**Trigger:** The work is shipped (Step 1 = yes) AND the ticket's only purpose was to track the work (e.g. a "supplement the changelog" ticket where the changelog was already updated in a separate transaction). The ticket duplicates a real outcome that is already in the audit trail.

**Procedure:**
1. Add `metadata.disposition=filed_in_error` to the ticket via `kanban_edit` (or via `kanban_complete` with a `--metadata` block).
2. Add a `kanban_comment` citing the duplicate-ticket reference (the ticket id whose work actually shipped) and the filed-in-error reason.
3. Call `kanban_complete` with `--summary` naming the duplicate ticket id and the filed-in-error marker.
4. The ticket moves to `done`. The audit trail records the duplicate and the reason.

**This is authorized by op-guard-10 v2 §1.3.** Op-guard-10 v2 explicitly permits closure-without-evidence when the ticket was filed in error (test artifact, duplicate, misroute). The filed-in-error documentation is the evidence.

### Action 3: File a kill-stuck-process follow-up

**Trigger:** The work is NOT shipped (Step 1 = no) AND the doc-writer process is dead (Step 2 = dead) AND the work cannot be re-scoped or closed (the original criteria are correct and the work needs to be done).

**Procedure:**
1. File a new ticket: `title="kill stuck process <pid> on <ticket-id>"`, `assignee=default` (the default profile has the process toolset), `parents=[<candidate-stuck-ticket-id>]`, `body="Doc-writer process crashed without a terminal call. Process pid: <pid>. Original ticket: <id>. Work NOT shipped. Recovery: kill the process and re-dispatch the original ticket."`.
2. Add a `kanban_comment` on the candidate stuck ticket linking the new follow-up as the parent.
3. The chat-side agent waits for the follow-up to complete; the dispatcher will re-dispatch the candidate stuck ticket once the parent is `done`.

### Action 4: Document the recovery in the audit log

**Trigger:** ALWAYS — regardless of which of Actions 1-3 fires.

**Procedure:**
1. Append an audit-line to `~/Documents/HermesVault/log.md` via `vault_log.append_audit_line`:
   ```
   ## [<isots>] op-guard-17-recovery — <candidate-ticket-id> — <action-1|action-2|action-3> — <one-line reason> — source=<candidate-ticket-id>
   ```
2. The audit-line is the durable record. The `kanban_comment` on the ticket is the local record; the audit-line is the operator-visible record.

---

## Part 4: Cadence and ownership

The chat-side agent owns the proactive monitoring. The cadence is 30 minutes for the two boards. The doc-writer owns the canonical rule (this document). The operator owns disposition (Actions 1-2 are routine; Action 3 surfaces to the operator via the op-guard-9 24h-stale force-surface rule).

The watcher for this is the `jarvis-stuck-board-monitor` cron (per op-guard-9), already registered. The new behavior is the rule itself: when the watcher catches a stuck doc-writer or verifier ticket, the chat-side agent runs the investigation procedure (Part 2) and applies one of the rescue actions (Part 3). The watcher does NOT auto-rescue; it surfaces. The agent decides.

---

## Part 5: Why this is a methodology, not a operational guard

A operational guard is a one-paragraph constraint derived from 3+ observed failures. The blocked-ticket pattern is a known failure, but the procedure is multi-step and the conditions are conditional (when the work is shipped vs not shipped, when the process is alive vs dead, when the doc-writer has the toolset vs not). A operational guard cannot capture the conditional procedure; a methodology can.

The 3-failure rule (per `methodology/05-op-guards.md`) does not apply here because the rule is reactive (it fires on a specific trigger) rather than preventive (it fires on every action). The methodology is the right shape.

This methodology is the first of its kind in the corpus. It is the template for future "operational recovery procedures" — rules that fire on a specific system state and produce a specific outcome.

---

## Part 6: Cross-references

- `methodology/06-iteration-loop.md` — the iteration loop primitive (detect → surface → act). The recovery procedure is the *act* leg for stuck-ticket state.
- `methodology/06a-decide-retro-v2.md` — the retro (post-execution self-improvement). The recovery procedure is distinct from the retro: it fires on a stuck-detection, not on a flow completion.
- `methodology/05-op-guards.md` — operational guards. The recovery procedure is NOT a operational guard (see Part 5).
- `2-ATOMIC/rules/strike10-no-close-on-age-2026-07-27.md` — closure-with-evidence rule. Action 1 (re-scope) and Action 2 (close as filed-in-error) are both authorized by op-guard-10 v2.
- `2-ATOMIC/rules/strike9-cron-loop-completeness-2026-07-21.md` — the cron-output-must-reach-decision-maker rule. The `jarvis-stuck-board-monitor` cron is the surface mechanism.
- `templates/AGENTS.md.template` Section 7 (kanban lifecycle) — references this rule via the doc-writer recovery procedure block.
- `ticket:t_78ffd7e5` — the parent ticket that produced this methodology.
- `ticket:t_0e6c33f8` — the 16h-blocked ticket that triggered this rule.
- `ticket:t_03dc7ef9` — adjacent ticket: out-of-spec correction for the 4 done infra tickets.
- `ticket:t_03193ab9` — adjacent ticket: spec-first flow.
- `methodology/04b-decide-board-routing.md` — board-routing rules; the two boards in scope are `alice-framework` and `agent-resources`.

---

## Part 7: How this maps to a future operational guard (if the pattern recurs)

If the chat-side agent runs Part 2's investigation procedure 3 times and the same root cause produces the stuck ticket each time, this methodology MAY be promoted to a operational guard (one-paragraph constraint). The pattern would be: "When a doc-writer ticket is stuck in `running` state, the chat-side agent MUST run the investigation procedure before filing a `kill stuck process` follow-up." Until 3 observed failures of the same shape, this rule stays as a methodology.

---

## Operator gate

None. Verifier-gated. The verifier-on-ticket reads the methodology + the AGENTS.md reference and confirms the procedure is documented.

## Revision history

- v1 (this doc, 2026-08-06): the initial recovery procedure. Documented per the operator's 2026-08-05 direction. Source ticket: `t_78ffd7e5`.
