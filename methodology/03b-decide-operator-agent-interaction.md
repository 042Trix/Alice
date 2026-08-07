---
id: alice-methodology-03b-decide-operator-agent-interaction
created: 2026-08-04T12:30:00Z
title: "Methodology 03b — Decide how the operator interacts with agents (5 roles + ticket lifecycle + chat budget)"
type: methodology
status: draft
source: alice-framework
tags: [kind:methodology, kind:operator-agent, project:alice]
confidence: 0.0
links: ["[[methodology/03-decide-agents.md]]", "[[methodology/03a-decide-agent-vault-interaction.md]]", "[[methodology/05-op-guards.md]]", "[[methodology/06-iteration-loop.md]]", "[[methodology/07-council-methodology.md]]", "[[methodology/01a-decide-memory.md]]", "[[templates/operator-interaction-patterns.md.template]]", "[[templates/agent-soul.md.template]]", "[[templates/AGENTS.md.template]]"]
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

## Part 13: Interaction anti-patterns

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

## Part 14: When to revise the operator-agent interaction design

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