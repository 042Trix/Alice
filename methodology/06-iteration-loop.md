# Methodology 06 — The iteration loop (detect → surface → act, with feedback)

> The sixth decision: the engine. The detect → surface → act protocol is the heartbeat of the system. Without it, crons generate noise, skills generate no-ops, and the operator context window fills with nothing. **Without feedback, the engine runs but never improves — every loop is a fresh copy of the previous one. The feedback element is what makes the system a *learning* system rather than a steady-state one.**

## What the iteration loop is (in Alice's terms)

The **iteration loop** is the cycle that keeps the system alive. In its engine form (the implementer's lens), the loop has three legs:

```
Detect  →  Surface  →  Act  →  (Detect again)
```

- **Detect** — observe something in the system (a file, a state, a count, a condition)
- **Surface** — make the observation visible to a decision-maker (a DM, a notification, a log line, a kanban ticket)
- **Act** — take a known action on the observation (auto-recover, file a ticket, run a script, escalate)

In its **operator-facing form** (the loop as the operator describes it when reasoning about what the loop is *for*), the loop has six elements:

```
trigger → goal → action → evidence → feedback → stop rule
```

- **trigger** — what kicks off the loop (the *when*; a schedule, an event, a state change, a manual command)
- **goal** — what the loop is trying to achieve this run (the *why*; not the same as the loop's long-term purpose, which is the system-level heartbeat)
- **action** — what the loop actually did this run (the *what*; corresponds to `Act` in the engine form)
- **evidence** — the artifact or signal that the action ran and had (or did not have) the intended effect (the *did it work*; a log line, a state change, a file produced, a count moved)
- **feedback** — the loop's record of what to do differently next time, derived from the evidence (the *what to change*; this is the element that makes the system learn — see "The feedback element" below)
- **stop rule** — the condition that ends the loop (the *when to stop*; success, failure, age, count, operator override)

The two framings describe the same loop from different angles. The engine form (`Detect → Surface → Act`) is what you implement. The operator form (`trigger → goal → action → evidence → feedback → stop rule`) is what you reason about. The mapping is:

| Engine leg      | Operator elements                       | Notes                                                                            |
|-----------------|-----------------------------------------|----------------------------------------------------------------------------------|
| (implicit)      | `trigger`                               | Implemented as the cron's schedule or the agent's event-handler; not a "leg" because it is the loop's pre-condition, not its work |
| `Detect`        | (covered by `goal`)                     | The detect step is how the loop checks whether `goal` is still unmet             |
| `Surface`       | (covered by `evidence`)                 | The surface step is how `evidence` reaches the decision-maker                    |
| `Act`           | `action`                                | Direct correspondence                                                           |
| (missing!)      | `feedback`                              | **No engine leg. This is what this doc adds.** See "The feedback element"        |
| (implicit)      | `stop rule`                             | Implemented as the cron's exit condition or the agent's terminal-transition gate |

The point of the table is: the engine form is *missing a leg*. The engine says "detect → surface → act, then detect again," which produces a loop that never changes. The operator form says "and also remember what to do differently next time," which produces a loop that **improves**. Adding the feedback element to the engine is the durability piece — the engine's three legs become four: `Detect → Surface → Act → Feedback`.

This is the **engine** that drives crons, agents, and operational guards. Without it, the system is static — no detection, no notification, no action. Without the feedback element, the system is static in a second way: it never improves.

## Why this is the most important methodology doc

Every other methodology doc describes **what** (skills, agents, crons, operational guards). This doc describes **how** they interact. The iteration loop is the protocol that lets a detection lead to an action, and an action lead to a new detection.

If you only have time to read one methodology doc, read this one. The others are inputs; this is the engine.

## The 3 legs in detail

### Detect

**What it observes:**
- A file (created, modified, deleted)
- A state (board count, ticket status, run success/failure)
- A count (number of items in a queue, number of open tasks, number of failed runs)
- A condition (cron has not fired in N minutes, agent has not been dispatched in N hours, ticket is past N days stale)

**The discipline:**
- One detection = one observable. Don't try to detect "everything" in one cron.
- Detection is cheap. Run often. A detection that runs every minute and finds nothing is fine; a detection that runs daily and finds something late is expensive.
- The detector must be **specific**. "Is there a problem?" is not a detector. "Are there 3+ tickets older than 24h in status=blocked?" is.

### Surface

**Where it goes:**
- A DM (DM-shaped surface pattern)
- A kanban ticket (rescue-shaped monitor pattern)
- A log line (audit-line pattern)
- An email (high-stakes notifications only)
- A bridge file (file-system based, read by other agents or the operator on session-start)

**The discipline:**
- Surface is **for decision-makers**, not for the detector itself. If a detection loops back to the detector, you have a feedback loop. Stop.
- Surface is **specific**. Don't say "something is wrong." Say "ticket t_X has been blocked for 24h; see /path/to/ticket."
- Surface is **batched** when appropriate. A cron that fires 50 times an hour and surfaces 50 times is noise. Batch into "every 5 min, here are the 50 detected."

### Act

**What it does:**
- Auto-recover (close the ticket, run the script, fix the state)
- File a ticket (the rescue pattern)
- Run a worker (dispatch the agent)
- Escalate (notify the operator, the next person, the on-call)
- Defer (file for later, no action now)

**The discipline:**
- Act is **for known patterns**, not for novel situations. If the action is novel, it's a judgment call, not an act. Defer to operator.
- Act is **for the detection's scope**. If the detection is "is X blocked for 24h?" the act is "re-dispatch X." Not "fix the whole system."
- Act is **reversible** when possible. Auto-recover reversible; escalate irreversible.

### Feedback (the 4th leg)

> This section is new as of the operator's 2026-08-05 brain-dump ("trigger, goal, action, evidence, feedback, stop rule. Self improvement."). The original 3-leg engine had no feedback element. Adding it is what turns the loop from a steady-state cycle into a learning one.

**What it does:**
- Records what to do differently next time, derived from the evidence the loop just produced
- Updates the **graph**, **flow**, or **skill** so the next loop runs with the updated artifact
- Closes the loop between *this run* and *next run* — the next run reads the updated artifact instead of starting from scratch

**The pattern:**
```
evidence  →  review  →  action-on-update  →  next loop uses the updated artifact
```

The feedback element is the bridge between one loop's evidence and the next loop's input. Without it, every loop run is independent — the loop never accumulates learning. With it, the loop's audit-line (or its equivalent written artifact) becomes input to the next loop's `trigger → goal → action` chain.

**Three feedback shapes:**

1. **Skill update.** The loop's evidence shows the skill under- or over-shot (e.g. a rescue loop that ran 5 times in 10 minutes produced 3 false-positive tickets). The feedback is: "skill X needs the rescue-eligibility predicate tightened." The next loop uses the updated skill.

2. **Graph update.** The loop's evidence shows the graph routing was wrong (e.g. a detector surfaced a stuck ticket that was actually fine because it had been re-routed earlier in the day, and the detector missed the re-route). The feedback is: "graph node Y's `done` state needs to include the re-route event." The next loop uses the updated graph.

3. **Flow update.** The loop's evidence shows the flow spec was missing a step (e.g. an act pattern that closed a ticket but did not check whether the closure unblocked a parent — leaving an orphan). The feedback is: "flow Z needs a step between `close` and `exit`: `verify-parent-unblocked`." The next loop uses the updated flow.

In all three shapes, the feedback element produces a **change to the next loop's input**. The next loop's `trigger → goal → action` chain reads the updated artifact, not the previous one. That is what makes the loop self-improving rather than steady-state.

**The discipline:**
- Feedback is **derived from evidence**, not invented. The loop does not "have a feeling" that the next run should change; the loop reads what its evidence says and produces a change that follows from the evidence.
- Feedback is **specific to the artifact it updates**. "Skill X needs the rescue-eligibility predicate tightened" is feedback. "Things should work better" is not. The feedback element names the artifact, the change, and the reason.
- Feedback is **committed to a durable surface**. The feedback must be written to disk (a methodology doc, a skill file, a graph node, a comment on a ticket), not held in the loop's working memory. The next loop reads it; if it is not on disk, the next loop starts fresh.
- Feedback is **opt-in, not opt-out**. Not every loop run produces a feedback update. Most runs produce no change. The feedback element fires when the evidence demands a change, not on a timer. (This is the loop's "silent on no-action" rule applied to the feedback leg.)

**The 3rd class that has no feedback:**

Class 1 (Heartbeat) loops have no feedback element. Heartbeats are evidence-only — they exist to detect, not to improve. Forcing a feedback element onto a heartbeat produces noise.

Class 2-4 loops all have feedback elements, but the feedback fires only when the evidence shows a durable pattern (not on every run).

**Mapping to the operator's six elements:**

The feedback element corresponds directly to the operator's `feedback` in `trigger → goal → action → evidence → feedback → stop rule`. The "stop rule" comes after feedback — the loop does not stop until the feedback has been committed (or the loop has determined that no feedback is needed this run).

**Reconciliation with GRAPH / retro + feedback:**

The graph-level retro (per the `Graph retro + feedback` AREA in `methodology/00-decide-ticket-naming.md`) is **the feedback element at graph scope.** A graph that runs many loops has its own retro step: 1-day-open window, surface what worked, surface what didn't, update the graph's nodes/flows/skills based on the evidence. That retro *is* the feedback element — applied to the graph-as-a-whole rather than to a single loop run.

The reconciliation is:

| Scope              | What "feedback" means                                                                                       |
|--------------------|------------------------------------------------------------------------------------------------------------|
| Single loop run    | The loop's record of what to change before the next run. Committed to the loop's own artifact (skill, flow, graph node). |
| Graph (multi-loop) | The graph's retro — a 1-day-open window that aggregates per-loop feedback and updates the graph's structure. |
| Methodology doc    | A doc-writer ticket that aggregates many loop/graph retros and codifies the durable rule.                   |

All three are the same pattern at different scopes. The graph retro is not a separate mechanism; it is the feedback element applied one level up.

## The 4 classes of loop

### Class 1: Heartbeat (cheap, frequent, no surface)

- **Detect:** "Is the dispatcher alive?"
- **Surface:** Log line only. No notification.
- **Act:** None (the act is "log, then exit").

**Cadence:** Every 1-5 minutes. Cost: minimal. The heartbeat is the system's "I'm still alive" signal.

### Class 2: Monitor (cheap, frequent, surface on findings)

- **Detect:** "Are there 3+ tickets blocked for 24h?"
- **Surface:** Kanban ticket or DM if findings; silent if not.
- **Act:** File a ticket for the operator to act on (not auto-recover).

**Cadence:** Every 5-30 minutes. Cost: minimal per run.

### Class 3: Auto-recover (cheap, less frequent, surface on action)

- **Detect:** "Is the cron X registered and running?"
- **Surface:** Audit line + DM if it had to re-register.
- **Act:** Re-register the cron.

**Cadence:** Every 5-60 minutes. Cost: minimal but not free.

### Class 4: Trigger (medium, less frequent, surface always)

- **Detect:** "Is there a new X post to ingest?"
- **Surface:** Run the agent, write to vault.
- **Act:** Run the X-ingestion skill (writes to vault).

**Cadence:** Daily / weekly. Cost: medium (an LLM call per run).

## The "silent on no-action" rule

**Every loop is silent on no-action.** If the loop runs and finds nothing, it produces no output. The operator context window doesn't fill with "ran, found nothing, ran, found nothing."

This is the **discipline** that keeps the system quiet. The system speaks when it has something to say.

If your loop is producing output every run, the loop is misconfigured. Either the detection is too broad, or the surface is too eager, or the act is too aggressive.

## The "fail closed" rule

**Every loop fails closed.** If the loop can't detect, it doesn't surface, doesn't act, and logs a failure. The loop does NOT fail open (silently pass through the failure).

A loop that fails open is worse than no loop, because the operator thinks the system is healthy when it's not.

**Implementation:** every loop has a try/except that catches all exceptions, logs them, and exits cleanly. The loop does NOT swallow errors. The audit line shows the error.

## The 3 boundaries of a loop

Every loop has 3 boundaries:

1. **What it observes** (the detect scope)
2. **What it acts on** (the act scope)
3. **What it surfaces** (the surface scope)

**The 3 scopes should be the same.** A loop that detects "all 8 boards" but only acts on one specific board is misconfigured. The detect, surface, and act should all reference the same domain.

If you find yourself wanting different scopes, **the loop is doing two things.** Split it.

## Loop anti-patterns

1. **"The loop should detect and act in one cron."** → No. Detect is one cron. Surface is the loop's output. Act is a separate ticket or trigger. Mixing them is how feedback loops form.

2. **"The loop should run every minute."** → Almost always wrong. If you need minute-cadence, the loop is a hook, not a cron. Use a hook.

3. **"The loop should be smart."** → Loops are deterministic. If the loop is making decisions, it's a worker, not a loop.

4. **"The loop should self-heal."** → Sometimes. But self-heal loops are complex. Start with detect + surface. Add act when the pattern is well-understood.

5. **"The loop should email me."** → Almost never. Use DM or kanban ticket. Email is for high-stakes notifications only.

6. **"The loop should run forever."** → No. Loops have TTL. After N failures, escalate to operator. After N successes of doing nothing, consider whether the loop is still needed.

## The 5-loop-archetype pattern

A common failure mode is that the system has many loops, each one doing a small thing, and the loops themselves are not coordinated. The result is noise: many loops surfacing, no clear pattern.

The fix is the **5-loop-archetype pattern**:
1. The system has 1-2 heartbeat loops (cheap, no surface)
2. The system has 2-3 monitor loops (cheap, surface on findings)
3. The system has 0-1 auto-recover loops (rare, surface on action)
4. The system has 1-2 trigger loops (medium, surface always)
5. The system has 0 operational-guard-enforcement loops (operational guards are passive; the agent reads them)

If your system has more than 10 loops, you're over-looping. Consolidate.

## What's next

- `methodology/07-council-methodology.md` — when to convene a council to revise the loop
- `methodology/08-inbox-route.md` — how external inputs become loop-detected
- `methodology/09-inbox-from-external-sources.md` — X / articles / podcasts as a special case

## Anti-patterns to watch for

1. **"I have 11 crons all doing different things."** → Probably 5-6 are dead, 2-3 are chat lanes, 2-3 are real loops. Audit.
2. **"I have 0 crons and the operator is doing everything."** → Add 1 heartbeat. See what surfaces.
3. **"The loop should notify me every time it runs."** → No. Silent on no-action. Surface on findings only.
4. **"The loop should run on every event."** → That's a hook, not a cron. Use the right tool.
5. **"The loop should improve itself."** → Not on every run. Most runs produce no feedback. The loop's "self-improvement" is opt-in, derived from evidence, committed to a durable surface. A loop that updates itself every run is a loop that is rewriting its own source code on every tick. That's an agent, not a loop.
6. **"The feedback element is just logging."** → No. Logging is the evidence, not the feedback. The feedback is the *change to the next loop's input*. If the only thing the loop writes is a log line, the loop has evidence but no feedback.
7. **"I'll improve the loop next time I touch it."** → That is the failure mode this element prevents. The feedback element forces the change to happen *now*, with the evidence, in the same run. "Next time" is a place where improvements go to die.

## Worked example: a loop runs, gets feedback, updates the skill; the next loop uses the updated skill

This is the canonical example of the self-improvement pattern. It uses the **stuck-sweep rescue loop** from `methodology/04-decide-crons.md` as the concrete instance.

### The loop, run 1 (no skill update)

```
trigger:   cron tick, every 10 min
goal:      rescue tickets that have been blocked for >24h
action:    scan all boards, find blocked tickets older than 24h,
           re-dispatch each one to its assignee with a "you're stuck"
           comment
evidence:  7 tickets re-dispatched. 3 of them re-blocked within 1h
           because the underlying dependency was not actually resolved
feedback:  (see run 2)
stop rule: cron tick ends after the scan; loop sleeps until next tick
```

The evidence shows a problem: 3 of 7 tickets were re-dispatched to the same blocker that put them in `blocked` state in the first place. The loop's `action` did not check whether the dependency was actually resolved before re-dispatching. This is a **feedback-shaped finding**: the loop's skill (`re-dispatch-blocked-ticket`) is missing a pre-check.

### The feedback element fires (between run 1 and run 2)

```
evidence:  3/7 re-dispatched tickets re-blocked within 1h
review:    skill X is re-dispatching tickets without checking the
           dependency state
action-on-update: patch skill X to add a "verify-dependency-resolved"
           check before re-dispatch
artifact updated:  ~/.hermes/skills/rescue-stuck-tickets/SKILL.md
           (added step 3: "verify the parent block is unblocked")
```

The feedback element produced a **change to a durable surface** (the skill file). The next loop run will read the updated skill.

### The loop, run 2 (uses the updated skill)

```
trigger:   cron tick, every 10 min
goal:      rescue tickets that have been blocked for >24h
action:    scan all boards, find blocked tickets older than 24h,
           re-dispatch each one to its assignee with a "you're stuck"
           comment — BUT FIRST verify the dependency is resolved
           (per the updated skill)
evidence:  7 tickets scanned, 4 re-dispatched (3 skipped because
           the dependency was not actually resolved), 0 re-blocked
           in the following hour
feedback:  no change this run — the previous feedback has held
stop rule: cron tick ends after the scan; loop sleeps until next tick
```

The next loop used the updated skill, and the evidence shows the change worked: 0 re-blocks in the following hour (vs. 3/7 in run 1). This is the self-improvement pattern in operation: **run 1's evidence became run 2's input, via the feedback element's skill update.**

### What this example demonstrates

1. **Feedback is derived from evidence, not invented.** The feedback in run 1 was triggered by 3/7 re-blocks — a specific, countable signal. It was not "the loop should be smarter."
2. **Feedback is specific to the artifact it updates.** The feedback named the skill (`rescue-stuck-tickets`), the change ("add a verify-dependency-resolved check"), and the reason (3/7 re-blocks).
3. **Feedback is committed to a durable surface.** The skill file on disk. Not the loop's working memory.
4. **Feedback is opt-in, not opt-out.** Run 2 produced no further feedback — the previous feedback had held. Most runs produce no change. The feedback element fires when the evidence demands a change, not on a timer.
5. **The next loop uses the updated artifact.** Run 2 read the updated skill (with the new check) and produced better evidence (0 re-blocks). The loop is now measurably better than it was before.

### The pattern, abstracted

```
run 1:   trigger → goal → action → evidence (with problem)
         evidence → review → action-on-update (the feedback element)
         → skill/flow/graph updated on disk
run 2:   trigger → goal → action (using updated skill)
         → evidence (improved)
         → feedback (no change needed this run)
         → stop
```

The feedback element is the bridge between `evidence` (run 1) and `trigger`/`goal`/`action` (run 2). Without it, run 2 would use the same skill run 1 used, and the same problem would recur.

### How this maps to GRAPH / retro + feedback

This single-loop example is the smallest possible feedback element. The graph retro is the same pattern, but at graph scope:

- **Single loop retro:** evidence from one run → patch the skill for the next run
- **Graph retro:** evidence from many runs (over a 1-day-open window per the graph retro convention) → patch the graph's nodes/flows/skills for the next graph execution

Both are the feedback element. The graph retro is not a separate mechanism; it is the feedback element applied to a larger scope. A doc-writer ticket (such as this one) is the feedback element applied to methodology — the doc-writer's evidence comes from many graph retros over time, and the doc-writer updates the methodology doc so the next graph (and the next doc-writer pass) reads the updated methodology.

That is what makes the system *self-improving* end-to-end, not just per-loop.

**The full protocol for the graph retro — the 3 fields (what worked / what didn't / what to change), the 1-day-open human-feedback window, the auto-apply step, and the worked example — lives in `methodology/06a-decide-retro.md`.** This 06 doc covers the *principle* (feedback element, opt-in, durable surface); the 06a doc covers the *protocol* (the state machine, the operator amendment window, the follow-up-ticket routing).

## Maintenance

> The iteration loop is the engine. An engine needs maintenance — not on every run, but on a regular cadence. This section defines the loop-class audit: a periodic check that the engine is still doing what it claims to do, and the rules for retiring it when it isn't.

### 1. Audit cadence

**Every 30 days.** The loop is the engine; check it monthly. A loop that has been "running fine" for 6 months is suspect — the world changed, the loop didn't. The 30-day cadence is a class-level audit, not a per-loop audit: every active loop class (heartbeat, monitor, auto-recover, trigger) is checked as a group. The check is cheap: a single grep + a count of recent cron firings per leg. The check fires a doc-writer or operator ticket if drift is detected; silent otherwise.

### 2. Quality threshold

The loop is healthy when **all 4 legs are running** and the loop **closes** end-to-end:

1. **Detect** — has recent cron firings or trigger events (at least 1 in the last 7 days for monitor/auto-recover/trigger; at least 1 in the last 24h for heartbeat).
2. **Surface** — produced an output artifact in the last audit window (DM, kanban ticket, audit line, or bridge file).
3. **Act** — has at least one record of taking action (auto-recover, ticket filed, worker dispatched, escalation sent) when the detect found something.
4. **Feedback** — has produced at least one durable-surface update (skill patch, flow patch, graph node update, or methodology amendment) since the loop's last major change.

The loop closes when `detect → surface → act → feedback → next detect` is traceable in the audit log. A healthy loop has all four legs exercised in the last audit window, and at least one complete trace from a recent `detect` event to the next `detect` event (with surface/act/feedback in between).

### 3. Drift signals

The loop is drifting when:

- **One leg has no recent activity.** No `action` events in 7 days for a monitor loop, no `surface` artifacts for a heartbeat in 24h (acceptable — heartbeat's surface is silent — but verify the log line is still being written), no `feedback` events in 30 days for any class 2-4 loop. The leg is "running" but doing nothing.
- **Feedback is not actioned.** The loop produced a feedback update (skill patch, methodology amendment) but a follow-up inspection shows the next loop runs did not use the updated artifact. The feedback landed but the loop didn't consume it.
- **Detect-heavy, act-light.** The loop is firing frequently and surfacing frequently, but the act leg is consistently "file a ticket" without resolution. The loop is generating noise, not work. The act leg is misconfigured (too aggressive — surfacing without a real recovery path) or the detect is too broad (finding things that don't need acting on).
- **The loop is duplicated by another loop.** Two loops (e.g., a system-level loop and an agent-level loop) are detecting the same condition and acting on it independently. One of them is dead weight; consolidate.
- **The operator has a manual process that supersedes the loop.** The operator is doing the loop's work by hand because the loop's output is not useful. The loop is in the way; retire it.

### 4. Fix actions

When drift is detected:

1. **Missing leg.** Identify which leg is silent. Run the corresponding cron's health check (per `methodology/04-decide-crons.md` — `hermes cron list`, `hermes cron run <id>`, inspect the most recent firing). If the cron is registered but not firing, re-register or re-enable. If the cron is firing but the leg is silent in the output, the leg is broken at the script level — file a coder ticket.
2. **Feedback not actioned.** Inspect the most recent feedback update. Was the artifact on disk? Did the next loop read it? If the feedback landed on disk and the loop didn't consume it, the loop's `action` step is hard-coded to read the prior artifact, not the feedback-updated one. File a coder ticket to wire the loop to read the updated artifact.
3. **Detect-heavy, act-light.** Reduce the detect scope (narrow the predicate), or add an act-leg recovery path that doesn't require operator intervention. If neither is possible, retire the loop — see below.
4. **Loop duplication.** Identify the two loops. Pick the one with the cleaner scope. Archive the other. File a ticket documenting the consolidation so the audit trail preserves the rationale.
5. **Operator supersedes.** Confirm with the operator that the manual process is the new normal. File a ticket to archive the loop with `metadata.disposition=abandoned_per_decision` and a reference to the operator's confirmation comment.

### 5. Retirement conditions

A loop is retired when:

1. **The loop is duplicated by another loop** (e.g., a system-level loop and an agent-level loop doing the same thing). The redundant loop is archived; the surviving loop absorbs the scope. The audit trail records the consolidation.
2. **The operator has a manual process that doesn't need the loop.** The operator is doing the loop's work by hand and the loop's output is noise. The loop is archived with `metadata.disposition=abandoned_per_decision` referencing the operator's confirmation.
3. **The loop has been silent for 90+ days.** No detect firings, no surface artifacts, no act events, no feedback. The loop is dead but still registered. Archive it; if a need recurs, a new loop can be designed from scratch with the lessons learned (which is the feedback element at meta scope).
4. **The loop's evidence consistently shows it is not useful.** The loop runs, surfaces, acts — but the operator ignores the surface and the act has no follow-through. The loop is consuming cycles without producing value. Archive with `metadata.disposition=abandoned_per_decision` and a reference to the audit evidence.

Retirement is not failure. It is the loop's `stop rule` firing at class scope. A retired loop is the feedback element applied to the loop fleet: the audit line says "this loop class is no longer needed; here is the evidence." The next time someone designs a loop in this class, they read the retirement audit line and either re-instantiate with a different shape or pick a different class entirely.

### Maintenance parity check

This section defines a friend-portable method, not a claim that every platform supplies cron-firing telemetry, audit-log capture, or disposition review queues. Before adopting it, map each function — class-level audit, drift surfacing, fix-action routing, retirement archival — to mechanisms available in your own tool. The 30-day cycle is a methodology default; adjust it when measured loop activity, drift rate, or operator-stated risk provides better evidence, but record the exception so the loop set remains auditable.

## See also (updated)

- `methodology/00-decide-ticket-naming.md` — codifies the LOOP and GRAPH AREAs, including the `Graph retro + feedback` and `Loop feedback element` / `Loop self-improvement` topic names.
- `methodology/01a-decide-memory.md` — **the loop's audit-line is episodic memory (Tier 4).** Each loop run produces an audit line; the loop's state-of-the-world is reconstructed from the union of these audit lines. The feedback element's "commit to a durable surface" rule is satisfied by writing the audit line.
- `methodology/03b-decide-operator-agent-interaction.md` — loops surface to operator; the operator-action ticket lifecycle (surface → respond → resume) is documented in the operator-agent doc. The loop's "surface" leg is the first half of the operator-action ticket lifecycle.
- `methodology/04a-decide-work-graph.md` — the work graph is the substrate. **The loop's scope can be a single ticket, a parent ticket (cascading), or a sub-graph.** A rescue loop that rescues only the unit, not the children, leaves children orphaned. A monitor loop that scans only top-level tickets misses sub-tasks. The loop's detect step should specify the graph scope.
- `methodology/04b-decide-board-routing.md` — every ticket file is a routing decision. Loops that detect "stuck tickets" should scope the detect by board, not by all-boards-at-once. A loop that surfaces all-boards-at-once is over-firing. **Alice documents the method, not specific instances.**
- `methodology/04-decide-crons.md` — cron classification (heartbeat / monitor / auto-recover / trigger); the feedback element applies to classes 2-4, not class 1.
- `methodology/06a-decide-retro.md` — **the graph-retro protocol** (3 fields + 1-day-open window + auto-apply). This doc covers the feedback element *principle*; 06a covers the post-execution retro *protocol*.
