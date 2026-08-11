---
id: alice-methodology-03b-decide-operator-agent-interaction
created: 2026-08-04T12:30:00Z
updated: 2026-08-11T23:30:00Z
title: "Methodology 03b — Decide how the operator interacts with agents (5 roles + ticket lifecycle + chat budget + PDF review format)"
type: methodology
status: draft
source: alice-framework
version: 0.1.2
amended_by: ["[[ticket:t_5dc19cae]]", "[[ticket:t_472c75f2]]", "[[ticket:t_34dc1c2b]]"]
tags: [kind:methodology, kind:operator-agent, kind:operator-review, kind:pdf-format, kind:executive-reporting-aligned, project:alice]
confidence: 0.0
links: ["[[methodology/03-decide-agents.md]]", "[[methodology/03a-decide-agent-vault-interaction.md]]", "[[methodology/05-op-guards.md]]", "[[methodology/06-iteration-loop.md]]", "[[methodology/07-council-methodology.md]]", "[[methodology/01a-decide-memory.md]]", "[[methodology/M-decide-human-digest.md]]", "[[templates/operator-interaction-patterns.md.template]]", "[[templates/agent-soul.md.template]]", "[[templates/AGENTS.md.template]]", "[[templates/human-digest.md.template]]"]
teaching-example: true
---

# Methodology 03b — Decide how the operator interacts with agents

> **The agent design doc tells you WHO the agent is. The agent-vault doc tells you HOW THE AGENT acts on the vault. This doc tells you HOW THE OPERATOR interacts with the agent.** Every session has an operator on one side and an agent on the other. Without a unified methodology, the operator's interaction is ad-hoc — too sparse (agent can't get help) or too flooded (operator context fills).

This methodology covers **the design of operator↔agent interaction** — the 5 operator roles, the operator-action ticket lifecycle, the "operator-as-launcher" pattern, the chat-output-budget rule, the approver / feedback-giver / fallback / curator patterns, the escalation chain, the DM/DM-back discipline, the "operator as final authority" rule, and the interaction anti-patterns. The methodology is tool-agnostic. The canonical implementation in the worked examples uses the AGENTS.md + kanban + DM pattern.

**Documentation convention:** Alice documents the **method**, not specific instances. This doc does not list any specific operator or agent. The friend applies the method to their own context.

---

## Part 1: Why operator-agent interaction design matters

### The decision

The operator (you) decides:
- **How do you launch a session?** (the launcher pattern)
- **How do you approve agent work?** (the approver pattern)
- **How do you govern the agent's behavior?** (the governor pattern)
- **How do you curate the agent's skills, SOULs, operational guards?** (the curator pattern)
- **What do you do when the agent fails?** (the fallback pattern)

Without an interaction design, the answers are implicit. The operator starts sessions ad-hoc, approves inconsistently, governs by operational-guard accidents, curates by drift, and falls back without recovery. The agent behaves inconsistently across sessions.

### The 5 failure modes

An operator-agent interaction system fails in 5 predictable ways:

1. **Operator flood.** The agent surfaces every decision as operator-action. The operator context fills with asks. The operator can't decide; they rubber-stamp everything.
2. **Operator starvation.** The agent never asks for help; it makes decisions that the operator should review. The operator misses irreversible actions.
3. **Operator drift.** The operator's preferences change but the agent doesn't know. The agent follows old operational guards. The operator corrects repeatedly.
4. **Operator override.** The operator manually does what the agent should do. The agent becomes a notification system.
5. **Operator abandonment.** The operator disappears; the agent keeps working. Without operator governance, the system drifts.

The interaction design is the design that prevents all 5.

---

## Part 2: The 5 operator roles

Alice recommends a 5-role interaction model. Each role is a **distinct way the operator engages with the agent**:

### Role 1: Launcher

**What:** the operator opens a session. The agent reads the AGENTS.md (cold-start memory) and asks the first question.

**When:**
- Every cold-start session
- Every new task

**Pattern:**
1. Operator types the initial prompt (or context)
2. Agent reads AGENTS.md + relevant skills
3. Agent asks one clarifying question (if needed) or proceeds
4. Operator responds

**Discipline:**
- The operator's first message includes context (what they want, what they've tried, what's expected)
- The agent's first response acknowledges context and asks one question (not a list)
- The exchange ends with the agent having a clear task

### Role 2: Approver

**What:** the operator approves agent proposals (Pattern 2: Propose in `methodology/03a-decide-agent-vault-interaction.md`) and scaffolds (Pattern 4: Scaffold).

**When:**
- The agent proposes a new note / edit / scaffold that needs operator review
- The agent archives a note based on operator instruction

**Pattern:**
1. Agent proposes (with rationale + alternatives)
2. Operator reviews
3. Operator approves / rejects / modifies
4. Agent acts

**Discipline:**
- The agent's proposal is specific (what, why, alternatives)
- The operator's response is one of: approve / reject / modify (not silence)
- The approval / rejection is logged

### Role 3: Governor

**What:** the operator sets operational guards, audits agent behavior, and revises the system when it drifts.

**When:**
- A failure pattern emerges (3+ of the same shape)
- An agent behaves outside its soul
- A operational guard needs revision

**Pattern:**
1. Operator observes the failure
2. Operator drafts a operational guard
3. Operator files the rule in the rules tier
4. Agent loads the rule on next session
5. Operator audits the agent's compliance

**Discipline:**
- The operator is the only one who writes operational guards (the agent proposes; the operator writes)
- Operational guards are reviewed quarterly (or sooner if patterns shift)
- Operational guards are archived when the pattern stops recurring

### Role 4: Curator

**What:** the operator curates the agent's skills, SOULs, and templates.

**When:**
- A new skill is needed (recurs weekly + bounded + verifiable)
- An existing SOUL needs revision (the agent's role changes)
- A template needs updating (new field, new structure)

**Pattern:**
1. Operator observes a recurring need
2. Operator writes the skill-brief / SOUL revision / template update
3. Operator reviews the agent's compliance with the new artifact
4. Agent loads the new artifact on next session

**Discipline:**
- The operator writes the SOUL (the agent doesn't)
- The operator approves the skill-brief (the agent implements)
- The curator role is distinct from the approver role (curator = writing artifacts; approver = approving proposals)

### Role 5: Fallback

**What:** the operator does manually what the agent can't do, and the operator captures the manual work for the agent to learn.

**When:**
- The agent fails on a task
- The agent asks for help on something the operator can do
- The agent is offline

**Pattern:**
1. Operator notices the failure / request
2. Operator does the work manually
3. Operator captures the work as an atomic note or skill-brief
4. Agent loads the work on next session

**Discipline:**
- The fallback is a learning opportunity, not a permanent handoff
- The operator's manual work is captured in the vault (atomic note or skill-brief)
- The agent's role is updated to handle the task next time

### The 5-role matrix

| Role | When | Pattern |
|---|---|---|
| Launcher | Every cold-start | prompt → AGENTS.md → first question |
| Approver | Pattern 2 / 4 in 03a | propose → review → approve/reject |
| Governor | Failure pattern emerges | observe → rule → review |
| Curator | New skill / SOUL / template needed | need → write → review |
| Fallback | Agent fails / offline | do → capture → agent loads |

---

## Part 2.5: The operator-bypass warning (operator can, but shouldn't)

**Rule:** The operator **can** create tasks directly on the kanban board (the kanban is a shared substrate, not a closed agent system). **But it is not recommended.** The orchestrator loses context when the operator opens a ticket directly: the operator's original request never enters the orchestrator's plan, so the orchestrator cannot decompose, route, or sequence the work correctly. The recommendation is for the operator to **describe the request to the orchestrator**, who opens a master ticket (see `methodology/04c-decide-master-ticket.md`) and owns the resulting work graph.

### Why operator-bypass breaks the system

1. **The orchestrator loses the request.** The orchestrator's plan is built from the operator's first message. If the operator skips the orchestrator and writes a ticket, the orchestrator never knows the request existed — it can only discover the ticket by polling the board, at which point the planning, decomposition, and routing have already been skipped.
2. **Request fidelity is lost.** A master ticket preserves the operator's original request verbatim in the body. A direct kanban ticket preserves only the operator's title and body — which are usually a paraphrase, not the original message.
3. **Routing goes wrong.** The orchestrator picks the correct board, the correct assignee, and the correct done-gate. A direct ticket by the operator picks whatever fields are visible in the form — wrong board, wrong assignee, ambiguous gate.
4. **Decomposition is skipped.** Multi-step requests need a master + children. A direct ticket is a leaf; the multi-step structure is invisible.
5. **The orchestrator can't course-correct.** If the operator opens the wrong ticket on the wrong board, the orchestrator (who would have caught it) has no signal to correct it. The wrong work fans out before anyone notices.

### Anti-pattern

**The operator opens a kanban ticket directly to ask the agent to do something.** The ticket lands on some board with some assignee; the orchestrator sees only the resulting ticket, never the original request. The work proceeds without the orchestrator's plan.

### What to do instead

- **Describe the request to the orchestrator in chat.** The orchestrator's job is to receive the request, plan it, decompose it, and open the master. The orchestrator's response to the operator's first message is the planning surface.
- **If a master is already open, add children to the master.** Don't create a sibling ticket. If the work is part of an existing request, it belongs under the master; if not, describe the new request to the orchestrator and let it open a new master.
- **Use operator-action tickets only for explicit decisions.** Operator-action tickets (`assignee=operator`, `block_kind=needs_input`) are how the orchestrator surfaces decisions back to the operator. They are not how the operator initiates work.

### Related

- `methodology/04c-decide-master-ticket.md` — the master ticket is the orchestrator's tool for capturing an operator-originated multi-step request. The operator-bypass anti-pattern is what happens when the operator skips the orchestrator and skips the master.
- `methodology/06-iteration-loop.md` — the iteration loop's "surface to a decision-maker" pattern assumes the operator surfaces *from* the orchestrator, not *as* a ticket author. Operator-authored tickets bypass the surface-to-act loop.

---

## Part 3: The operator-action ticket lifecycle

### The rule

When the agent surfaces an operator-action item, the ticket moves through 3 phases:

```
surface  →  respond  →  resume
```

### Phase 1: Surface

**What:** the agent files an operator-action ticket. The ticket has `assignee=operator` and `block_kind=needs_input`.

**When:**
- The agent needs a decision the operator must make
- The agent has completed work requiring operator review
- The agent has detected an issue requiring operator attention

**Discipline:**
- The ticket is specific (one question, one decision)
- The ticket has a clear "what I'm asking" (not "what should I do?")
- The ticket is filed with the relevant context

### Phase 2: Respond

**What:** the operator responds with a decision.

**When:** as soon as practical; the operator prioritizes operator-action items.

**Discipline:**
- The operator's response is one of: approve / reject / modify / defer
- The response is in the chat (or in a comment on the ticket)
- The response is recorded (audit line or ticket comment)

### Phase 3: Resume

**What:** the agent resumes the task based on the operator's response.

**When:** after the operator's response.

**Discipline:**
- The agent acknowledges the response ("Got it — I'll do X")
- The agent does the work
- The agent reports back when done

### The lifecycle anti-patterns

- **Surface without response.** The operator doesn't respond; the ticket ages; the agent is blocked.
- **Surface with vague ask.** "What should I do?" is not a specific ask.
- **Response without resume.** The operator responds but the agent doesn't act.

---

## Part 4: The chat-output-budget rule

### The rule

The operator's context window is finite. Every chat reply has a budget. Default budget: **≤200 words per chat reply**, **≤3,000 tokens per session**.

### Why a budget

Without a budget:
- The operator context fills with narrative
- The operator loses track of what the agent is doing
- The operator can't review the work

### The discipline

- **Chat is for confirmations, not narration.** One sentence per major action.
- **Long output goes to the vault, not chat.** Audit lines, code, configs → vault.
- **Receipts are short.** "Filed ticket t_X" is enough; the ticket has the details.

### The chat-output budget per message type

| Message type | Budget |
|---|---|
| Action confirmation | ≤30 words |
| Question to operator | ≤100 words |
| Multi-step summary | ≤200 words |
| Critical alert | ≤500 words |

### The session budget

Every session has a budget. The default is **≤3,000 tokens per session** for chat output (not counting the agent's tool calls).

When the budget is hit, the agent:
- Summarizes the rest of the session in the vault
- Asks the operator to start a new session if more work is needed

---

## Part 5: The "operator-as-approver" pattern

### The rule

When the agent proposes work (Pattern 2 in `methodology/03a-decide-agent-vault-interaction.md`), the operator approves, rejects, or modifies.

### The approval format

The operator's response is one of:

- **Approve:** "Approved. Proceed." (or just "Approved")
- **Reject:** "Rejected. [Reason]"
- **Modify:** "Modified: [what changed from the proposal]"

### The approval discipline

- The operator's response is in the ticket comment (not chat)
- The response cites the ticket id (e.g., "Approved t_X")
- The agent acts only after the operator's response

### The approval anti-patterns

- **Rubber-stamping.** The operator approves without reading the proposal.
- **Silent approval.** The operator doesn't respond; the agent proceeds anyway.
- **Modifying without explanation.** The operator changes the proposal but doesn't say why.

---

## Part 6: The "operator-as-feedback-giver" pattern

### The rule

When the operator corrects the agent, the correction is structured and updates memory.

### The feedback format

The operator's feedback is:

```
You're doing X. The right behavior is Y. Reason: Z.
```

**Why:** the agent can update its memory (Tier 6: operator preferences) with this structured feedback.

### The feedback discipline

- The operator cites the specific behavior (not "you're wrong")
- The operator states the right behavior (not just "stop")
- The operator gives the reason (so the agent can apply it to similar cases)

### The feedback anti-patterns

- **Vague correction.** "Be better." is not actionable.
- **Recurring correction.** The operator corrects the same thing 3+ times; that's a missing operational guard or SOUL revision.
- **No memory update.** The operator corrects; the agent forgets by next session.

---

## Part 7: The "operator-as-fallback" pattern

### The rule

When the agent fails or is offline, the operator does the work manually and captures it for the agent.

### The fallback pattern

1. **Operator notices the failure.** (The agent crashes, asks for help, or is offline.)
2. **Operator does the work manually.** (Operator writes the note, files the ticket, runs the cron.)
3. **Operator captures the work.** (Atomic note, ticket comment, or skill-brief.)
4. **Agent loads the work.** (On next session, the agent reads the captured work.)

### The fallback discipline

- The fallback is **temporary.** The operator doesn't become a permanent worker.
- The operator's manual work is **structured** (atomic note, not just chat).
- The agent's role is updated to handle the task next time.

### The fallback anti-patterns

- **Permanent handoff.** The operator does the work forever; the agent never learns.
- **Silent fallback.** The operator does the work; the agent doesn't know; the agent re-tries next session.
- **No capture.** The operator does the work; nothing is recorded; the agent can't learn.

---

## Part 8: The "operator-as-curator" pattern

### The rule

The operator curates skills, SOULs, and templates. The agent proposes; the operator writes.

### The curator pattern

1. **Operator observes a recurring need.** (5+ weekly tasks, bounded, verifiable.)
2. **Operator writes the skill-brief.** (The skill-brief template has 7 sections.)
3. **Operator approves the agent's implementation.** (Pattern 2: Propose in 03a.)
4. **Operator reviews the agent's compliance.** (Quarterly audit.)

### The curator discipline

- **The operator writes the SOUL.** The agent implements; the operator governs.
- **The operator approves the skill-brief.** The skill-brief is the spec; the operator owns the spec.
- **The operator audits the agent's use.** The skill is shipped; the operator verifies usage.

### The curator anti-patterns

- **Agent-written SOULs.** The SOUL is the operator's contract with the agent; the operator writes it.
- **Unaudited skills.** A skill ships; the operator never verifies it's used.
- **Drift.** A skill that was useful 6 months ago is now obsolete; the operator doesn't archive.

---

## Part 9: The escalation chain

### The rule

When the agent encounters a decision it can't make, it escalates to the operator. The escalation has 4 phases:

```
request  →  action  →  escalation  →  decision  →  resume
```

### Phase 1: Request

The agent encounters a decision. The decision is one the agent can't make alone.

### Phase 2: Action (until escalation)

The agent takes any safe actions it can. (Read notes, gather context, draft a proposal.)

### Phase 3: Escalation

The agent surfaces the decision as an operator-action ticket.

### Phase 4: Decision

The operator responds (approve / reject / modify / defer).

### Phase 5: Resume

The agent acts based on the operator's decision.

### The escalation discipline

- The escalation is **specific** (one decision).
- The escalation has **alternatives** (the agent proposes 2-3 options).
- The escalation has **recommendation** (the agent says which it prefers).
- The operator's response is **timely** (priority depends on stakes).

### The escalation anti-patterns

- **Escalation without proposal.** "What should I do?" is not a specific escalation.
- **Escalation without alternatives.** The agent proposes one option; the operator has nothing to choose between.
- **Operator silence.** The operator doesn't respond; the agent is blocked.

---

## Part 10: The "operator-as-question-asker" pattern

### The rule

The operator asks the agent state queries. The agent answers in a structured format.

### The question format

The operator's questions are:

- **State queries:** "What's the state of t_X?"
- **Why queries:** "Why did you do Y?"
- **What-if queries:** "What if we did Z?"
- **How queries:** "How do I do W?"

### The answer format

The agent's answers are:

- **Specific** (named the actual ticket / file / value)
- **Brief** (≤100 words for state queries, ≤500 for how queries)
- **Cited** (the agent cites the source of the answer)

### The question anti-patterns

- **Vague questions.** "What's going on?" is not a specific query.
- **Recurring questions.** The operator asks the same thing weekly; that's a missing MOC or skill.
- **Long answers.** The agent narrates; the operator context fills.

---

## Part 11: The DM/DM-back discipline

### The rule

The agent DMs the operator when needed. The operator DMs back when responding.

**Important:** in a multi-agent system, DMs happen ONLY between operator and orchestrator (the main agent). Sub-agents communicate via ticket comments, not DMs. See `methodology/03c-decide-agent-communication-channels.md` for the full channel design (DM, comment, kanban event).

### The DM discipline (agent → operator)

- The DM is **specific** (one ask / one notification / one alert).
- The DM is **brief** (≤200 words).
- The DM cites the relevant ticket / file / context.

### The DM-back discipline (operator → agent)

- The operator's response is **directive** (approve / reject / modify / defer).
- The operator's response is **in the same channel** (chat / DM / ticket).
- The operator's response is **timely** (priority depends on stakes).

### The DM anti-patterns

- **DM without context.** "Hey, can you check X?" without explaining why.
- **DM without closure.** "Did you do X?" without waiting for the answer.
- **DM in chat, response in ticket.** The conversation fragments.
- **Sub-agent DMs operator.** In a multi-agent system, the orchestrator is the only one with DM access. Sub-agents use ticket comments.

---

## Part 12: The "operator as final authority" rule

### The rule

When the agent and operator disagree, **the operator wins**. The agent signals disagreement; the operator decides; the agent acts.

### The disagreement pattern

1. **Agent proposes an action.**
2. **Operator rejects or modifies.**
3. **Agent signals disagreement** (if the agent believes the operator is wrong).
4. **Operator re-considers** (or stays the course).
5. **Agent acts** (per the operator's decision).

### The disagreement discipline

- The agent can **signal** disagreement (with rationale).
- The agent **does not override** the operator.
- The operator **does not punish** the agent for signaling disagreement.
- The disagreement is **logged** (audit line or ticket comment).

### The disagreement anti-patterns

- **Agent overrides operator.** The agent does what it thinks is right, ignoring the operator.
- **Operator punishes disagreement.** The agent signals; the operator rejects; the agent learns not to signal.
- **No logging.** The disagreement happens; nothing is recorded; the agent can't learn from it.

---

## Part 13: The operator-review artifact format (PDF rule)

### The rule

**Any artifact that requires the operator's review MUST be in human-readable PDF format.** The Markdown source is preserved on disk for editability and indexing. **Both formats ship.** The PDF is the operator-facing presentation layer; the Markdown is the canonical source of truth and the audit record.

This applies to every artifact that the operator is asked to review, including but not limited to:

- Retro digests (already covered by `methodology/M-decide-human-digest.md` + `templates/human-digest.md.template`)
- Master ticket summaries (when the operator is asked to review a master before closing)
- Audit-line digests (when a cron or loop fires and produces a digest for the operator)
- Council synthesis (when the operator is asked to accept or reject the council's verdict)
- Spec proposals (when the operator is asked to approve a spec change)
- Council review summaries (any review where the operator's accept / reject / defer decision is needed)
- Verification reports (when the verifier's output is presented to the operator)
- Approval requests (any ticket the operator is asked to approve or reject)

### Why PDF (not Markdown-only)

The PDF is the operator's reading surface. Markdown is fine inside a vault, but the operator's reading surfaces are macOS Preview, iOS Preview, the Discord inline viewer, and the browser. Across those surfaces:

- **PDF renders consistently** — tables, headers, code blocks, and callouts survive in the same shape everywhere.
- **PDF survives in Discord** — Discord renders Markdown inconsistently; PDF preserves the structure.
- **PDF is printable** — the operator can mark it up on paper.
- **PDF is self-contained** — no editor, no toolchain, no missing fonts.
- **PDF is audit-friendly** — durable artifact, easy to attach and re-attach.

Markdown's job in this pattern is to be the canonical source. The PDF is a *rendering* of the canonical source. The two never disagree because the PDF is regenerated from the Markdown before delivery; the Markdown remains the source of truth and the audit record.

### The 5-step delivery protocol

Every operator-review artifact follows the same delivery chain:

```
Markdown (canonical)  →  PDF (rendered)  →  Discord attachment  →  ticket comment  →  vault copy
```

1. **Markdown (canonical).** Write the artifact as Markdown at the canonical workspace path (for editability and indexing). The Markdown is the source of truth.
2. **PDF (rendered).** Render the Markdown to PDF using the agent's standard Markdown-to-PDF tool (e.g. `~/.hermes/tools/md_to_pdf.py` for Hermes-implementing operators; the tool path varies by implementation). The PDF lives at `<workspace>/human-review.pdf` or at the artifact's canonical PDF path.
3. **Discord attachment.** Send the PDF as a Discord attachment to the operator's chat channel when the chat is connected. The attachment is the operator-facing surface.
4. **Ticket comment.** Post a one-line summary to the originating ticket (or master ticket) with a path pointer to the artifact. The comment is the durable back-link.
5. **Vault copy (when persistent).** When the artifact is meant to outlive the workspace (e.g., a council verdict, a master review), store a copy at `~/Documents/<framework>/2-ATOMIC/decisions/<artifact-id>.pdf` for the durable audit trail.

### The shape contract

The PDF MUST follow the operator-facing shape from `templates/human-digest.md.template` (or its successor):

1. **What this is** — one or two sentences describing the artifact.
2. **The headline** — 3–5 bullets, plain language, no jargon.
3. **What the [agent / verifier / council] found** — short paragraphs (or numbered sections) with the substantive findings.
4. **Recommended next steps** — actionable recommendations, each stating why it matters and what decision is needed.
5. **Evidence anchors** — a collapsible section pointing to the technical record (file paths, ticket IDs, comment IDs).
6. **Operator's observations** — blank section for the operator to add observations.
7. **Operator decisions** — Accept / Reject / Defer decision table.
8. **Metadata** — timestamp, source ticket, generation method.
The shape is the operator's reading contract. The Markdown source follows the same shape; the PDF is a rendering, not a different artifact.

### The canonical implementation

The canonical 8-section shape contract above is the operator-review artifact contract. The Hermes-instance implementation that operationalizes this contract is the `executive-reporting` skill at `~/.hermes/skills/productivity/executive-reporting/` (Scribe-owned, doc-writer-authored, shipped 2026-08-08, ticket `t_4699bb4a`). The skill is the canonical implementation; this methodology is the contract.

The skill operationalizes:

- **5 digest types** (council verdict / retro / master summary / audit-line / approval request) — see `references/per-digest-type-shape.md`. The retro specialization (10 sections) is **Type 2**; the canonical 8 sections is the base for Types 1, 3, 4, 5.
- **The 6 translation rules** — see `references/translation-discipline.md`. The rules are the canonical discipline for all 5 types; the retro-specific application of these rules is in `methodology/M-decide-human-digest.md` Part 3.
- **The 5-step delivery protocol** (Markdown → PDF → Discord → ticket comment → vault copy) — operationalized as the canonical pattern, paired with the 5-step protocol in this Part 13.
- **Worked examples** for each shipped type — see `references/worked-examples.md`. The retro worked example is the canonical reference for retro §4 agreement-column vs §9 per-seat alignment (see audit gap 3).
- **The canonical 8-section fillable form** for non-retro artifacts — `~/.hermes/skills/productivity/executive-reporting/templates/executive-report.md.template`. The retro specialization's fillable form is `templates/human-digest.md.template` (the Alice-canonical retro template).

When a new operator-review artifact is needed, the doc-writer or Scribe loads the `executive-reporting` skill and follows the per-digest-type shape map; the methodology docs (this Part 13 + `M-decide-human-digest.md`) are the contract, the skill is the implementation. The two must not drift; if they do, the spec (this methodology) is canonical and the skill must be patched on the next skill release.

### The discipline

- **Both formats ship.** Markdown is canonical; PDF is the operator's reading surface. Never ship one without the other.
- **Render before delivery.** The PDF is generated from the Markdown immediately before the Discord attachment step. A stale PDF is a drift risk.
- **Plain language in the headline.** No jargon, ticket IDs, file paths, run IDs, agent / profile names, or implementation details in the PDF's headline section. Technical references belong in the collapsible **Evidence anchors** section.
- **Decision table is mandatory.** Every operator-review artifact ends with an Accept / Reject / Defer decision table. Silence is not consent — an operator who does not respond has not accepted.
- **Discord is the operator-facing surface when connected.** If no chat channel is connected, the artifact still lives at its canonical paths; the delivery step is recorded as not applicable. A failed delivery must not invalidate the artifact.
- **The technical record remains canonical.** When the PDF and the underlying technical record disagree, the technical record wins and the PDF is regenerated before delivery. The PDF is a translation layer, not a second source of truth.

### Why the rule exists (operator correction 2026-08-07)

The pattern hit the same shape three times:

1. **First correction (2026-08-07 morning):** operator: "Let's make sure that any items that are sent to the operator (me) for review are set like this, human readable format in PDF format." The standing rule was established.
2. **Second correction (2026-08-07 morning, follow-up):** operator: "Can we do it as a PDF format instead of MD?" PDF was selected over a polished Markdown alternative because PDF preserves structure across the operator's surfaces (Preview, iOS Preview, Discord, browser).
3. **Third correction (2026-08-07 evening):** retro digests shipped as Markdown-only artifacts; operator flagged that retro digests are *one case* of the operator-review-artifact pattern, and the rule needs to apply to *every* operator-review artifact (master summaries, council synthesis, spec proposals, verification reports, approval requests), not just retros.

The rule codifies the operator's preference for PDF as the universal operator-review format. The retroactive digest template (`templates/human-digest.md.template`) remains the canonical fillable form; the rule generalizes the format requirement from "retro digests" to "all operator-review artifacts."

### The anti-patterns

- **Markdown-only operator-review artifact.** Shipped as `.md` only; the operator opens a Markdown viewer, headers reflow, tables break, code blocks lose formatting. The operator cannot read it on iOS without an editor.
- **PDF with no Markdown source.** The PDF is generated but the Markdown is not saved. The audit record is gone; the artifact cannot be re-rendered; the verifier cannot inspect the source.
- **Stale PDF.** The PDF was rendered from a Markdown that has since been edited. The operator reads a PDF that disagrees with the technical record. The fix is regenerate-before-deliver.
- **PDF-only without a Discord attachment.** The PDF lives on disk but is never delivered. The operator never sees it. The fix is the 5-step delivery protocol above.
- **Accept-by-silence.** The PDF was delivered; the operator did not respond; the agent proceeded as if the recommendation were accepted. The decision table exists; the operator has not filled it in. Silence is not consent.
- **Jargon in the headline.** The PDF's headline section uses internal vocabulary — phase names, ticket IDs, run IDs, agent / profile names. The operator cannot read it without the system context. The fix is plain-language discipline.

### Cross-references

- `methodology/M-decide-human-digest.md` — the human-digest methodology, which is the canonical instance of this rule (retro digests). The two-layer record (technical + digest) is the special case of this rule for retros. v0.1.1 flags the same shape contract as the canonical shape for council-verdict PDFs (see below).
- `templates/human-digest.md.template` — the fillable form for retro digests; its 8 sections are the shape contract for all operator-review artifacts (including council verdicts).
- `templates/operator-interaction-patterns.md.template` §"Operator-facing digest format" + §"Operator-review artifact format (PDF rule)" — the operator-facing engagement-pattern shape.
- `methodology/07-council-methodology.md` — council synthesis is an operator-review artifact; this rule applies.
- `methodology/06a-decide-retro-v2.md` — the retro flow's human-digest step is the canonical instance.
- `methodology/M-decide-x-article-review-flow.md` Part 7.5 — Phase 4 council verdict is the parallel downstream apply of this rule (the x-article-review loop). The verdict PDF follows the 5-step delivery protocol; the 8-section shape contract is shared with the Retro-A digest. Implementation: `~/.hermes/tools/post_council_verdict_pdf.py`.
- `~/.hermes/skills/productivity/executive-reporting/` — the Hermes-instance skill that operationalizes this canonical contract (Scribe-owned, doc-writer-authored, shipped 2026-08-08, ticket `t_4699bb4a`). The skill is the canonical implementation; this methodology is the contract. The skill covers all 5 digest types (council verdict / retro / master summary / audit-line / approval request), the 6 translation rules, the canonical 8-section shape contract, the 5-step delivery protocol, and the canonical 8-section fillable form (`templates/executive-report.md.template`). The retro specialization (Type 2) is the canonical instance of this Part 13 rule; see `methodology/M-decide-human-digest.md` + `templates/human-digest.md.template` for the retro-specific 10-section shape.

> **Two downstream applies (as of 2026-08-07, t_e35032aa):** (a) Retro-A digests via `M-decide-human-digest.md` + `post_graph_retro_human_digest.py` (the original instance since 2026-08-07 morning); (b) Phase 4 council verdicts via `M-decide-x-article-review-flow.md` Part 7.5 + `post_council_verdict_pdf.py` (the parallel instance since 2026-08-07 afternoon). The general council flow's PDF rule is the canonical scope: any council that emits a verdict (BUILD / VALIDATE-FIRST) follows the 5-step protocol, not just the x-article-review Phase 4 case. The doc-writer authors the methodology; the council/skill-curator lane applies the spec to the council skill body (per op-guard-16).

---

## Part 14: Interaction anti-patterns

### Anti-pattern 1: "The agent surfaces every decision."

No. The operator-action gate is strict: only decisions the agent can't make. Routine decisions are agent-handled.

### Anti-pattern 2: "The operator does everything."

No. The agent handles bounded, verifiable tasks. The operator handles judgment, irreversible, and policy.

### Anti-pattern 3: "The operator rubber-stamps."

No. The operator reviews the proposal. Rubber-stamping is worse than no approval.

### Anti-pattern 4: "The operator never approves."

No. Approval is a tool; over-deferral slows the system. Approve when the proposal is sound.

### Anti-pattern 5: "The agent narrates tool calls."

No. The agent does the action; chat confirms. Per `methodology/05-op-guards.md` operational guard example.

### Anti-pattern 6: "The operator context fills."

No. Chat output is budgeted. Long output goes to the vault.

### Anti-pattern 7: "The agent escalates without alternatives."

No. Escalation has 2-3 options + a recommendation.

### Anti-pattern 8: "The operator approves in chat, not in the ticket."

No. Approval is in the ticket comment. The chat is for confirmations.

### Anti-pattern 9: "The agent forgets the operator's preferences."

No. Tier 6 (operator preferences) is cold-start memory. The agent reads it on launch.

### Anti-pattern 10: "The operator never runs an audit."

No. The 5-question interaction audit is quarterly. Without audits, drift accumulates.

### Anti-pattern 11: "The operator opens a kanban ticket directly."

No. The operator can, but shouldn't. See Part 2.5 above. The orchestrator loses context when the operator bypasses it; the right path is to describe the request to the orchestrator, who opens the master.

---

## Part 15: When to revise the operator-agent interaction design

The interaction design is **not static.** Revise when:

1. **A role emerges** (a new way the operator interacts)
2. **A budget is consistently hit** (chat output too long)
3. **A budget is consistently underused** (the operator's preferences are sparse)
4. **A failure pattern emerges** (e.g., the agent keeps asking the wrong questions)
5. **A new tool changes the interaction** (e.g., a new chat client)

The operator revises the design **when the design is the bottleneck, not before.**

---

## Worked example (skeleton)

The 2 worked examples (solo-founder, research-analyst) show the methodology applied to a specific instance. The examples are skeletons, not snapshots.

The friend reads the methodology, then designs their own operator-agent interaction patterns for their own context. The friend does NOT copy the worked example's roles or budgets; they apply the method to derive their own.

---

## What's next

- `methodology/03-decide-agents.md` — agent design (WHO) is a sibling doc
- `methodology/03a-decide-agent-vault-interaction.md` — agent-vault interaction (WHAT AGENT DOES TO VAULT) is a sibling doc
- `methodology/05-op-guards.md` — operational guards are operator-governance
- `methodology/06-iteration-loop.md` — loops surface to operator; the surface-to-respond cadence
- `methodology/07-council-methodology.md` — council verdicts are operator-reviewable
- `methodology/01a-decide-memory.md` — Tier 6 (operator preferences) is the operator-as-feedback-giver's surface
- `templates/operator-interaction-patterns.md.template` — the fillable form for the operator's role distribution
- `templates/agent-soul.md.template` — the SOUL has an "Operator priority" dimension
- `templates/AGENTS.md.template` — the AGENTS.md is the operator-as-launcher's seed

## Anti-patterns to watch for (summary)

1. **"The agent surfaces every decision."** → No. Operator-action gate is strict.
2. **"The operator does everything."** → No. The agent handles bounded tasks.
3. **"The operator rubber-stamps."** → No. Review every proposal.
4. **"The operator never approves."** → No. Approve when the proposal is sound.
5. **"The agent narrates tool calls."** → No. Action + one-sentence confirmation.
6. **"The operator context fills."** → No. Chat output budget.
7. **"The agent escalates without alternatives."** → No. 2-3 options + recommendation.
8. **"The operator approves in chat."** → No. Approval is in the ticket.
9. **"The agent forgets preferences."** → No. Tier 6 is cold-start.
10. **"The operator never runs an audit."** → No. Quarterly.
11. **"The operator opens a kanban ticket directly."** → No. Describe the request to the orchestrator. See Part 2.5.

## Touch-points with other methodology docs

This doc touches:

- `methodology/03-decide-agents.md` — sibling doc (WHO)
- `methodology/03a-decide-agent-vault-interaction.md` — sibling doc (WHAT AGENT DOES TO VAULT)
- `methodology/04c-decide-master-ticket.md` — the master ticket is the orchestrator's tool for capturing operator-originated work; the operator-bypass anti-pattern is what happens when the operator skips both (Part 2.5)
- `methodology/05-op-guards.md` — operational guards are operator-governance
- `methodology/06-iteration-loop.md` — loops surface to operator; surface-to-act chain assumes orchestrator-authored surfaces, not operator-authored tickets (Part 2.5)
- `methodology/07-council-methodology.md` — council verdicts are operator-reviewable
- `methodology/01a-decide-memory.md` — Tier 6 (operator preferences) is the operator-as-feedback-giver's surface
- `templates/operator-interaction-patterns.md.template` — fillable form for the operator's role distribution
- `templates/agent-soul.md.template` — SOUL has "Operator priority" dimension
- `templates/AGENTS.md.template` — AGENTS.md is the operator-as-launcher's seed

## Audit-line

`## [2026-08-07T23:50Z] operator-review-pdf-rule-cross-refs — extended Part 13 cross-references to cite `~/.hermes/skills/autonomous-ai-agents/hermes-council/SKILL.md` and `~/.hermes/loops/intents/x-article-review-intent.md` v0.2.2 as downstream consumers; flagged the two downstream applies (Retro-A digests + Phase 4 council verdicts) and the general council-flow scope. source=t_e35032aa`
`## [2026-08-08T16:35Z] operator-review-pdf-rule-exec-reporting-aligned — added "The canonical implementation" subsection to Part 13 (the executive-reporting skill at `~/.hermes/skills/productivity/executive-reporting/` is the canonical Hermes-instance implementation of this Part 13 contract; covers 5 digest types, 6 translation rules, 5-step delivery protocol, worked examples, fillable form). Added skill references to Part 13 Cross-references (skill root + per-digest-type-shape + translation-discipline + audit-existing-artifacts references). Updated frontmatter `updated:` to 2026-08-08T16:35Z; added tag `kind:executive-reporting-aligned`. Companion Alice methodology update: `methodology/M-decide-human-digest.md` v0.4.0 + `templates/human-digest.md.template` v0.4.0 (aligned the retro specialization with the skill's Type 2 taxonomy + 6 translation rules). The methodology is the contract; the skill is the implementation. source=t_5dc19cae`