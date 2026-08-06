---
id: alice-methodology-06a-decide-retro
created: 2026-08-05T16:00:00Z
updated: 2026-08-05T17:15:00Z
title: "Methodology 06a — Decide the retro (post-execution retro + human feedback window)"
type: methodology
status: draft
source: alice-framework
tags: [kind:methodology, kind:retro, kind:iteration-loop, kind:feedback, kind:self-improvement, project:alice]
confidence: 0.0
links: ["[[methodology/06-iteration-loop.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[methodology/04d-decide-flow-spec.md]]", "[[methodology/05-strike-rules.md]]", "[[worked-examples/01-solo-founder-skeleton/AGENTS.md]]"]
---

# Methodology 06a — Decide the retro (post-execution retro + human feedback window)

> The graph ran. The evidence shipped. The ticket is `done`. **Now what?** Every graph execution ends with a retro that closes the loop: a 1-day-open window for human feedback, then a forced application of the lessons-learned to the graph, flow, or skill so the next execution is better than this one. The retro is the system's mechanism for self-improvement; without it, the same mistakes ship every run.

This is the sibling doc to `methodology/06-iteration-loop.md`. The 06 doc describes the iteration loop primitive (detect → surface → act) and the engine that drives crons and skills. This doc describes the **retro** — the post-execution step that sits *after* the act leg and *before* the next detect. The retro converts one execution's evidence into the next execution's improved graph/flow/skill.

---

## Part 1: What the retro is (and isn't)

### What the retro is

A **retro** (short for *retrospective*) is a structured artifact produced at the end of every graph execution. The retro has four parts:

1. **A trigger** — the graph execution that just finished (a master ticket, a flow run, a council verdict, a cron sweep with side effects).
2. **Three fields of reflection** — what worked, what didn't, what to change (the doc-writer may extend).
3. **A 1-day-open human feedback window** — the operator (or a human delegate) has 24 hours to amend any field or veto the auto-action.
4. **A forced close + apply step** — at the 24-hour mark, the retro closes (with or without human input) and the "what to change" output becomes the payload of a follow-up ticket against the graph, flow, or skill that ran.

The retro is **post-execution**, not intra-execution. The graph ran to completion. The retro looks back at the run. The output of the retro is *changes for the next run*.

### What the retro isn't

- **NOT a post-mortem after a failure.** The retro fires on *every* execution, success or failure. A retro that only fires on failure becomes a debugging tool; a retro that fires on every run becomes a learning engine.
- **NOT a status meeting.** The retro is a written artifact with a fixed shape (3 fields), not a synchronous gathering.
- **NOT an audit-log entry.** An audit-line (`## [<ISO8601-UTC>] <kind> — <message>`) records what happened. The retro records *what was learned* and *what will change next*. The two are different shapes and live in different places.
- **NOT a feedback form for the operator to fill out.** The agent fills the three fields from the run's evidence; the operator may amend within 1 day, but the default writer is the agent that ran the graph, not the human.

### Why the retro exists (the gap it fills)

Per the operator's GRAPH notes (2026-08-05):

> "Every graph (and therefor execution) ends with a retro and option for human feedback (stays open for 1d then closes if no response), then improves the graph/flow/skill/etc so that future executions are better."

Without the retro:

- The same surface-level friction repeats every run ("step 3 was slow" — every run).
- Strike rules accumulate because nobody looks back at *why* the operator corrected.
- The system plateaus: the same flows ship the same way, the same humans see the same warnings, the same agents produce the same marginal output.

The retro is the **ratchet**. Each run is at-least-as-good as the last run. The 1-day-open window is the operator's veto; the auto-apply at 24h is the ratchet.

---

## Part 2: The retro's anatomy (the 3 + 1 fields)

### The 3 reflection fields

Every retro captures three reflections, in this order. The doc-writer may extend (e.g. add "what surprised us" or "what to keep doing"), but the three below are mandatory.

#### Field 1: What worked

**What it captures.** Concrete behaviors, artifacts, or decisions that produced good outcomes this run. *Specifics only.* No platitudes.

**Examples.**
- "The flow-spec doc (04d) prevented scope drift on step 2 — the worker refused to expand scope without the operator LGTM."
- "Auto-detect found the stale ticket at T+12h, before it hit the 24h stale threshold."
- "The agent's review comment cited evidence path /Users/homestead/.../foo.md, which the operator verified in one click."

**Anti-patterns for this field.**
- "Everything went great." (No specificity.)
- "The team was excellent." (No edge to a graph/flow/skill.)
- "Communication was clear." (Not actionable; no improvement signal.)

#### Field 2: What didn't

**What it captures.** Concrete failures, blockages, or near-misses. Be honest; the operator reads this. No blame, just facts.

**Examples.**
- "Step 3 (the verifier node) took 2x the expected time because the test data was not pre-seeded."
- "The dispatcher dropped the second child ticket due to a typo in the assignee profile name — caught only at the 24h stale sweep."
- "The agent produced a 400-line reply for a 20-line task; the operator had to scroll past context."

**Anti-patterns.**
- "Nothing." (If nothing didn't work, the field is "nothing" — but then the retro is short, see Part 5.)
- "The operator was slow to respond." (Don't blame humans for being human.)
- "The system is bad." (Not a graph/flow/skill edge; not actionable.)

#### Field 3: What to change

**What it captures.** The forward-looking recommendations that become the payload of the next improvement ticket. Each recommendation should be:

- **Concrete.** Names the artifact, the line, the threshold, the edge.
- **Bounded.** Scoped to one graph/flow/skill (or a small set with parent edges).
- **Reversible when possible.** Prefer "add a 2nd retry" over "delete the cron."

**Examples.**
- "Pre-seed the verifier test data in step 3's input contract (methodology/04d §2.x)."
- "Add a typo-check to the dispatcher's profile-resolution path before the spawn (hermes-cli/kanban_db.py::spawn)."
- "Reduce the agent's reply cap from unlimited to 400 lines by default (skill `02-decide-skills`)."

**Anti-patterns.**
- "We should improve communication." (Not an edge.)
- "Rewrite the whole flow." (Too big; split into 3+ smaller retros.)
- "Maybe think about scaling." (Speculation; no concrete change.)

### The 4th field (optional): What surprised us

The doc-writer may add a 4th field — *what surprised us* — for flows where unexpected outcome is a useful signal. This is optional; default is off. If you add it, document the addition in the flow spec (`methodology/04d-decide-flow-spec.md`) so consumers know.

**When to enable the 4th field.** When the flow is non-deterministic (Agents, council verdicts, LLM-driven content), and when "surprise" is a useful signal for the next run. NOT when the flow is deterministic (cron sweeps, format conversions) — there is no surprise in deterministic code.

---

## Part 3: The 1-day-open window

### The window is a hard timeout

The retro's human-feedback window is **24 hours exactly**, not "24-ish" or "until the operator notices." After 24 hours, the retro closes — with or without human input — and the "what to change" output becomes the payload of a follow-up ticket.

**Why 24 hours, not open-ended.** A retro that waits indefinitely for human feedback is a retro that the operator forgets about. The 24-hour window is the discipline: the operator has one day to amend or veto, and the system moves forward after that.

**Why 24 hours, not shorter.** Less than 24 hours and the operator (with a day job, two kids, an evening routine) cannot reliably respond. The 24h mark is the *fastest* window at which the operator's response rate stays above ~90% in practice.

### What the operator can do in the window

Three valid actions, ranked by how often they're used:

1. **Amend a field.** The operator edits one or two of the three fields before the retro closes. Most common action. Example: operator changes "step 3 was slow" to "step 3 was slow + step 5 misrouted to wrong board."
2. **Veto the auto-action.** The operator marks a "what to change" entry as NOT-actionable. The retro still closes at 24h, but that recommendation does NOT spawn a follow-up ticket. Example: operator says "we're not changing the dispatch path this week."
3. **No response.** The retro closes at 24h with the agent's three fields intact, and the auto-apply step runs against whatever the agent wrote. This is the common case — most retros close silently.

### What the operator cannot do in the window

- **Re-open a closed retro.** A retro that has auto-applied is closed. If the operator wants to revisit a recommendation, they file a new ticket.
- **Extend the window.** 24 hours is 24 hours. No "I'll respond Monday" exception. (If the operator needs more time, they can disable the retro cron for one cycle, but the default is hard-timeout.)
- **Modify the run after the fact.** The retro reflects the run that happened. If the run itself needs amending, that's a different ticket (a re-run, a patch, a rollback — not a retro amendment).

### The silent-on-no-action discipline (apply it to retros too)

The retro cron is silent when there is no retro to surface. A master ticket that completed 23 hours ago and the retro is still in the agent's "in-progress" state is NOT a surface event — the retro cron waits until the retro is published, then surfaces. See Part 4.

---

## Part 4: The retro lifecycle (states + transitions)

The retro itself is stateful. Alice recommends this 4-state lifecycle:

```
┌──────────┐  run finishes   ┌─────────────┐
│ (none)   │ ──────────────▶ │  drafting   │  (agent fills 3 fields)
└──────────┘                 └──────┬──────┘
                                     │  publish
                                     ▼
                              ┌─────────────┐
                              │  open       │  (operator has 24h to amend/veto)
                              └──────┬──────┘
                                     │  24h elapses / operator responds
                                     ▼
                              ┌─────────────┐
                              │  applied    │  (follow-up ticket filed)
                              └─────────────┘
```

- **`drafting`** — the agent is filling the 3 fields from the run's evidence. No external visibility.
- **`open`** — the retro is published; the 24h clock starts. Visible to the operator (DM, ticket comment, or surface file).
- **`applied`** — the 24h window has elapsed (or the operator responded); the "what to change" output has been filed as a ticket (or marked NOT-actionable per a veto).
- **Termination.** Once `applied`, the retro does not transition again. If the operator wants to revisit a recommendation, they file a fresh ticket.

### What state the run finishes in

A retro can only be drafted for a run that finished. "Finished" means:

- The flow's terminal node (the last script, the last agent, the last operator LGTM) has shipped.
- All evidence paths have been written to the audit-trail (kanban event log, vault notes, run summaries).
- The `done` state has been set on the master ticket (per `methodology/04a-decide-work-graph.md`).

A run that crashed, blocked, or aborted does NOT get a retro. The crash/block is itself the lesson; record it in the kanban event log and run the next iteration.

### When retro state is "open" and the operator is offline

The 24h clock runs on wall-clock time, not on operator-availability time. If the operator is offline for 2 days, retro recommendations from day 1 apply at the 24h mark; recommendations from day 1.5 apply at day 2.5; the operator returns to a queue of `applied` retros waiting for review. This is correct behavior — the system does not pause retro clocks for human schedules.

---

## Part 5: The auto-apply step (when the window closes)

When the 24h window closes, the auto-apply step runs. The auto-apply takes every unvetoed entry in "what to change" and files it as a follow-up ticket. The mapping is:

| Retro entry "what to change" | Follow-up ticket |
|---|---|
| Graph node change (e.g., "add a verifier step") | New ticket on the work-graph substrate board, parent = the master ticket that ran |
| Flow-spec change (e.g., "spec field needs clarifying") | New ticket on the work-graph substrate board, parent = the flow-spec doc |
| Skill change (e.g., "skill prompt needs tightening") | New ticket on the agent-profiles board, parent = the skill |
| Strike-rule change (e.g., "add a new rule") | New ticket on the platform-config board, parent = the relevant strike-rule file |
| Anything else | Default to the default-routing board with parent = the master ticket |

**Why these board assignments.** Per `methodology/04b-decide-board-routing.md`, board routing is the audit-trail of where work lives. Graph/flow changes go to the work-graph substrate board; agent/skill changes go to the agent-profiles board; platform-config changes go to the platform-config board; everything else goes to the default-routing board.

> **Note on naming.** The operator's Alice instance uses the board names `loop-builder`, `agent-resources`, `hermes`, and `default` for these four roles (work-graph substrate, agent-profiles, platform-config, default-routing respectively). Other Alice users may use different board names; the routing pattern (which role owns which kind of retro-spawned follow-up) is the same regardless of board name.

### The auto-apply is not the same as the retro closing

The retro closes when the follow-up tickets are filed (or when the operator vetoed everything). If the auto-apply step itself fails (e.g., the dispatcher is unreachable), the retro stays in `open` state until the failure is resolved. The 24h clock has elapsed but the retro has not auto-applied — the surface (next operator session) sees the stuck retro and acts.

### What if every "what to change" was vetoed

The retro still closes. The `applied` state still transitions. The audit-line reads: `retro-closed-with-no-action: all recommendations vetoed by operator within 1d window.` That's a valid retro outcome; the system honors the operator's veto.

### What if there are no "what to change" entries

The retro closes silently. Audit-line: `retro-closed-no-recommendations: 3 fields filled, no actionable changes proposed.` This is the correct outcome for a clean run — the agent ran well, the system is stable, no ratchet needed.

---

## Part 6: Worked example (the canonical shape)

### The run

Operator files master ticket `t_404: ship the v0.2 methodology release`. The flow has 6 children:
- 2 doc-producing agent nodes (extend 04a and 04b)
- 2 code-producing agent nodes (add a small CLI tool + smoke test)
- 1 verifier agent node (run all smoke tests)
- 1 human node (operator LGTM on the release)

All 6 children run over 4 days. Verifier passes; operator LGTMs; master ticket transitions to `done`.

### The retro drafts at the run's end

The doc-producing agent (the one closest to "what worked / what didn't" for a doc release) drafts the retro:

**Field 1: What worked**
- The 04a-part-4 (3-node-type taxonomy) update shipped clean and was approved by the operator on first review.
- The verifier's smoke-test suite caught 3 regressions before they hit release.
- The 04b board-routing doc resolved three pre-existing routing disagreements without further operator input.

**Field 2: What didn't**
- The doc-producing agent had to re-read 04a twice to check the cross-references; the "See also" sections were stale on first pass.
- The code-producing agent's CLI tool shipped with a missing `--help` flag; the verifier caught it late in the cycle.
- The operator LGTM window was 14 hours, not 2 hours as expected — likely due to operator-side availability, not the system.

**Field 3: What to change**
1. Add a "cross-reference audit" cron to the polish loop — see `doc-cross-reference-audit` skill, run before each release. (skill change)
2. Add `--help` and `--dry-run` flags to the code-producing agent's CLI tool as a default in the skill prompt. (skill change)
3. Change the operator-LGTM-expected-window rule from 2h to 24h (so the system's "stale-operator-LGTM" detector doesn't fire on normal operator-side delays). (strike-rule change, not this doc)

### The 1-day window

The retro publishes to the operator's audit-channel. Operator reads within 6 hours. Operator's actions:

- Amends Field 2 row 2: "The code-producing agent's CLI tool also shipped without a `--version` flag."
- Vetoes Field 3 row 3: "Don't change the LGTM window — the 14-hour delay was an outlier; we'd rather have one false-positive than lose the early signal."

### The auto-apply at 24h

The 24h mark hits. The retro cron reads the operator's amendments and the veto. Auto-apply files 2 follow-up tickets:

- **t_405** (agent-profiles board, parent: t_404) — "skill: add cross-reference audit to polish loop per retro t_404.1"
- **t_406** (agent-profiles board, parent: t_404) — "skill: code-producing agent CLI default flags should include --help, --version, --dry-run per retro t_404.2"

The vetoed row is dropped. The retro closes: `applied-with-2-tickets`.

### What the next execution gets

The next release (the v0.3 methodology work) auto-loads these two retro-spawned tickets as child tasks. The cross-reference audit runs before the doc-producing agent finalizes; the code-producing agent's CLI ships with the right flags. The retro's recommendations land *as work, not as aspirations*. The system ratchets forward.

---

## Part 7: Implementation notes (what this doc is NOT)

### Not a cron spec

The retro cron itself is a separate ticket (`retro-cron-design-2026-08`). This doc defines the *protocol* — the 3 fields, the 1-day window, the auto-apply — not the implementation. The doc-producing agent does not write the cron; the cron-implementing agent (the profile that owns scheduled-loop execution in this Alice instance — e.g. the operator's `dispatcher-cron` profile) writes the cron once the protocol is documented here.

### Not a process spec

The retro lifecycle (draft → open → applied) is a state diagram, not a procedure. There is no "step 1: do X, step 2: do Y" — there's a state machine and the agent + cron + operator move through it. If you find yourself wanting a procedural checklist, you're over-fitting the protocol.

### Not tied to a board

The retro cron may publish to any surface — DM, ticket comment, audit-line, vault file. The doc-producing agent recommends a dedicated ticket comment on the master ticket (so the retro travels with the run it reflects), but the surface is a tooling decision, not a methodology decision. (Default: ticket comment on the master; alternative: an inbox-folder file such as `0-INBOX/retro-<id>.md` for runs that span many master tickets — the operator's instance uses `0-INBOX/retro-<ticket-id>.md`.)

### Not a feedback channel for unrelated thoughts

The retro captures *what was learned from this run*. If the operator has a thought that's not about this run — "I want to redesign the agent-fallback chain" — that's a new master ticket, not a retro amendment. Retro amendments stay scoped to the run that produced them.

---

## See also

- `methodology/06-iteration-loop.md` — the iteration loop primitive that the retro wraps around; the retro is the *post-execution* step that closes the loop after one full iteration
- `methodology/04a-decide-work-graph.md` — work-graph states (`ready`, `running`, `done`); the retro fires on the `done` transition
- `methodology/04d-decide-flow-spec.md` — the 6-field flow spec; the retro's output ("what to change") becomes a follow-up ticket against the flow-spec doc
- `methodology/05-strike-rules.md` — strike rules often originate from retro's "what to change" recommendations; the auto-apply step files them as hermes-board tickets
- `methodology/03b-decide-operator-agent-interaction.md` — the operator-action ticket lifecycle (surface → respond → resume); the retro's 1-day-open window is one instance of this lifecycle

## What's next

- `methodology/06b-decide-retro-cron.md` (forthcoming) — the cron that publishes the retro at the run's end, manages the 24h window, and triggers the auto-apply step
- `methodology/07-council-methodology.md` — councils may also produce retros for council-level lessons; the retro pattern generalizes
- `references/tool-mapping-guide.md` — how to implement retro + 1-day-open in concrete tools (kanban-board comment + cron, vault file + filesystem watcher, etc.)

## Anti-patterns to watch for

1. **"The retro is a post-mortem that only fires on failure."** → No. The retro fires on *every* run. A retro that only fires on failure is a debugging tool, not a learning engine.
2. **"The retro waits indefinitely for the operator to respond."** → No. 24 hours is a hard timeout. The system moves forward at the 24h mark regardless of operator availability.
3. **"The retro is a meeting."** → No. The retro is a written artifact with a fixed shape (3 fields), not a synchronous gathering.
4. **"The retro's 'what to change' is just a wish list."** → No. Every entry must name a concrete artifact (graph node, flow-spec line, skill prompt, strike-rule file) and be bounded to one graph/flow/skill.
5. **"The retro applies silently to the next run."** → No. The retro spawns follow-up tickets; the next iteration's work is the union of those tickets, not a silent diff against the previous run's graphs.
6. **"The operator can extend the window."** → No. 24 hours is 24 hours. If the operator needs more time, they disable the retro cron for one cycle (the next cycle's retro files as normal).
7. **"We have 7 retro styles across 7 flows."** → No. One retro shape (3 fields + 1 optional) across all flows. Variation breaks the operator's mental model.
8. **"The retro is the auditor's job."** → No. The agent that ran the graph fills the 3 fields; the auditor (or verifier) may review, but the writer is the runner.

## Touch-points with other methodology docs

This doc touches:

- `methodology/06-iteration-loop.md` — append paragraph: "after the act leg, the run spawns a retro; the retro is the post-execution step that closes one full iteration"
- `methodology/04a-decide-work-graph.md` — note in Part 1: "the `done` state transition triggers a retro (per 06a) — retro is co-located with the done event, not separately scheduled"
- `methodology/04d-decide-flow-spec.md` — extend the 6-field spec with a 7th optional field: `retro_recipient` (the agent or board that consumes the retro's "what to change" output)
- `methodology/03b-decide-operator-agent-interaction.md` — add section: "the operator-action ticket lifecycle (surface → respond → resume) generalizes to the retro's 1-day-open window (open → respond/expire → applied)"
