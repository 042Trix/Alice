# Methodology 04 — Decide which crons to run

> The fourth decision: which scheduled jobs are worth a cron. Crons are **scheduled tasks** — separate from agents (persistent workers), separate from skills (procedures the agent invokes).

## What a cron is (in Alice's terms)

A **cron** is a scheduled task that runs on a fixed cadence. It can:
- Run a script (no agent involvement)
- Run an agent (one-shot agent session)
- Run a check + dispatch (verify state, then create a ticket)

Crons are **recurring**. If a task is one-off, use `schedule` or `remind me` instead. If a task is variable cadence, use a hook (event-driven) instead.

## The detect → surface → act rule

**Every cron must satisfy three legs:**

1. **Detect** — the cron observes something in your system (a file, a count, a state, a condition)
2. **Surface** — the cron makes the observation visible to a decision-maker (a DM, a notification, a log line, a kanban ticket)
3. **Act** — the cron takes a known action on the observation (auto-recover, file a ticket, run a script, escalate)

**If any leg is missing, the cron is broken.**

- Detect without surface: silent failure. The cron is "running" but no one knows.
- Detect + surface without act: notification spam. The cron reports but does nothing.
- Act without detect: the cron "does something" but doesn't know why. (This is the "agent-on-a-schedule" anti-pattern — see below.)

## The "is this a cron?" decision

A task should be a cron when:

1. **The cadence is fixed.** Every 5 min, daily 04:00, weekly Monday 09:00. Not "whenever X happens."
2. **The cost is bounded.** A cron that runs every minute but takes 5 minutes to execute is a disaster. Cron cost = (per-run cost) × (frequency).
3. **The output is predictable.** "Send a DM if X" is a cron. "Maybe do something if X" is a hook or a worker.
4. **The output is consistent.** If the cron produces different output every run, the cron is probably doing too much.

If any fails, **don't use a cron.** Use a hook, a worker, or a one-shot.

## Common cron anti-patterns

1. **"I'll add a cron for everything I want to remember."** → No. Use a TODO list, a calendar, or a reminder tool. Crons are for SYSTEM checks, not for personal reminders.

2. **"I'll add a cron that runs every minute."** → Almost always wrong. If you need minute-cadence, you have an event-driven problem. Use a hook.

3. **"The cron should do X, Y, and Z."** → Probably the cron is doing too much. Split into multiple crons or refactor to a hook + a worker.

4. **"The cron should run forever."** → No. Crons have TTL. After 24-72 hours of no-success, escalate to the operator or kill the cron.

5. **"The cron will self-heal."** → Sometimes. But self-heal crons are complex. Start with "detect + surface" and let the operator act.

6. **"The cron should be a worker that runs the agent."** → This is the "agent-on-a-schedule" pattern. Don't run a full agent on a fixed schedule. If you need the agent to do the work, dispatch the work to the agent as a ticket, not as a cron.

## Cron-spec template

Use `templates/cron-spec.md.template`. The spec has 7 sections:

1. **Name** — what the cron is called
2. **Schedule** — cron expression
3. **Detect** — what does the cron observe?
4. **Surface** — where does the observation go?
5. **Act** — what action does the cron take?
6. **Cost** — per-run cost, frequency, monthly total
7. **Audit line** — what the cron writes when it acts

If you can't fill in all 7, the cron isn't ready.

## The "is this a cron or a hook?" decision

A task is a **cron** if:
- It runs on a fixed schedule (e.g., every 5 min, daily, weekly)
- The trigger is the clock, not an event

A task is a **hook** if:
- It runs in response to an event (e.g., "when the user pastes an X link")
- The trigger is the event, not the clock

Don't use a cron to fake a hook. "Cron that runs every minute and checks if X happened" is **hook-impersonation** and it's expensive.

## The "is this a cron or a worker?" decision

A task is a **cron** if:
- The output is bounded and predictable
- The agent (if any) is one-shot and the work fits in 1-2 turns

A task is a **worker** if:
- The output is variable
- The agent needs persistent context
- The work spans multiple turns or sessions

Don't use a cron to fake a worker. If the agent needs to be alive for the duration of the work, dispatch a worker ticket.

## Cost discipline

Every cron has a per-run cost and a frequency. **The total monthly cost = per-run × frequency × 30.**

If the total monthly cost is more than the work is worth, the cron is too expensive. Either:
- Reduce the frequency (less often)
- Reduce the per-run cost (less work per run)
- Kill the cron (the work isn't worth it)

## Cron-cadence heuristics

- **Every 1 minute:** Use only when the cron is doing something the hook mechanism cannot.
- **Every 5 minutes:** Heartbeat / monitoring cron. OK if per-run cost is tiny.
- **Every 30 minutes:** Notification cron. OK if DM-able.
- **Hourly:** Almost never right. Either use a hook or a daily cron.
- **Daily at fixed time:** Morning report, daily cleanup, etc. Common.
- **Weekly at fixed time:** Monday digest, weekly review, etc. Common.
- **Monthly:** Reconciliation, billing, archival. Less common.

## The polling-engine exemption (the explicit 1-min heartbeat exception)

**Rule:** The "every 1 minute: almost always wrong, use a hook" heuristic does **not** apply to your system's polling engine (the work-graph polling loop that watches for newly-ready tickets). The polling engine is the system's heartbeat: it polls every 1 minute for tickets assigned to each agent and dispatches new work as tickets reach `ready`. This is a system loop, not a user-configured cron, and it is the explicit exception to the cadence heuristic.

> *Example: the operator's instance of this pattern is named "the dispatcher" — a kanban-aware polling loop in Hermes. Your system may use a different name (`work-loop`, `poller`, `scheduler`); the method is what matters.*

### Why the polling engine is exempt

1. **It is not user-configured.** The polling engine is the system's substrate-level polling loop. It exists underneath every kanban implementation — every work-graph-based system needs *something* that watches for `ready` transitions. User-configured crons sit on top of it; the polling engine sits underneath them.
2. **It cannot be a hook.** A hook fires on an event. The polling engine's job is to poll for *newly-ready* tickets, which is by definition not an event the source system emits — the source system only knows that a ticket was *written*, not that it just became *ready*. The `ready` transition is a derived state computed from `parents done AND block cleared`.
3. **Its per-run cost is tiny.** A polling loop that reads the board and dispatches new tickets has near-zero per-run cost. The cost formula is `(per-run cost) × (frequency)`, and the polling engine's per-run cost is small enough that 1-minute cadence is cheap.
4. **The latency must be short.** When a ticket transitions to `ready`, an agent should pick it up within a small bound (default: ≤1 minute). A 5-minute cadence would introduce avoidable delay between "this ticket is ready" and "the assigned agent is running it." That latency makes the operator feel the system is slow.

### Anti-pattern

**Writing a user-configured cron that polls your ticket system every 1 minute.** This is a hook-impersonation failure (see the cron-vs-hook decision above): the cron is faking the work-graph polling loop. The right answer is to file a ticket and let the polling engine dispatch it. If the per-ticket latency is too long, fix the polling engine, not the cron.

### What to do instead

- **Use the system's polling engine for ticket-dispatch polling.** The polling engine already polls every 1 minute; new tickets become `ready` automatically; assigned agents claim them.
- **Use hooks for event-driven work.** A hook is the right tool when the trigger is an event (a file appearing, an external API webhook, an X post) and the cadence is not the clock.
- **Use 5-min or longer crons for everything else.** The cadence heuristic above applies to every cron the user configures.

### Related

- `methodology/06-iteration-loop.md` — the iteration loop describes Class 1 heartbeat crons, which are also exempt from "every minute is wrong" because they are system-style monitoring. The polling engine is the strongest example of this exemption.

## What the operator sees

Every cron output should be visible to the operator in some form. The "silent cron" anti-pattern is "the cron runs but the operator never sees the output." That's not a cron, that's a black box.

If the output is a heartbeat ("yes, the cron is alive"), the operator sees the heartbeat. If the output is a notification, the operator sees the notification. If the output is empty (cron ran, no findings), the operator sees... nothing. That's also OK. **But the operator must be able to ask "did the cron run?" and get an answer.**

## Worked example (skeleton)

See `worked-examples/01-solo-founder-skeleton/methodology-notes/04-crons.md` when v0.0 ships for an anonymized example.

## See also

- `methodology/01a-decide-memory.md` — **cron's seen-state is system memory (Tier 7).** Crons write seen-state on every run; the seen-state is rotated periodically. The cron itself is procedural memory (it's a scheduled skill).
- `methodology/04a-decide-work-graph.md` — the work graph is the substrate; its canonical node order inserts an independent verifier gate between phases. **Cascading-crons** that dispatch child work or query sub-graphs require: (1) a sub-graph query (recursive traversal of `parent_id` + `blocks`), (2) a transactional dispatch (file all children, then transition parent), (3) an audit line per cascade, and (4) verifier evidence when the cascade produces a reviewable work product. Without these, the cascade is non-atomic and the operator can't reconstruct the work graph. **The polling-engine loop is the heartbeat that watches this work graph for `ready` transitions** (see the polling-engine exemption above).
- `methodology/04b-decide-board-routing.md` — every ticket file is a routing decision. Crons that file tickets (for example, a stuck-ticket rescue cron or a work-item auto-decompose cron) must respect the keyword-routing table. A cron that files on the wrong board is a routing violation. **Alice documents the method, not specific instances.**

## What's next

- `methodology/05-strike-rules.md` — the constraints that prevent crons from going wrong
- `methodology/06-iteration-loop.md` — the engine that drives cron + agent coordination
- `methodology/08-inbox-route.md` — how external inputs become crons / skills / agents

## Anti-patterns to watch for

1. **"I have 11 crons and they all do something different."** → Probably 5-6 are dead, 2-3 are chat lanes pretending to be crons, and 2-3 are real crons. Audit and consolidate.
2. **"I have 0 crons and the operator is doing everything."** → Add 1-2 heartbeats. The cron's job is to be the system's "I'm still alive" signal.
3. **"I'll add a cron for personal reminders."** → No. Personal reminders are not the agent's job.
4. **"The cron should be smart."** → Crons are deterministic. If the cron is making decisions, it's a worker, not a cron.
