---
id: alice-methodology-04b-decide-board-routing
created: 2026-08-04T12:30:00Z
updated: 2026-08-07T11:15:00Z
title: "Methodology 04b — Decide your work-type routing system (board-routing)"
type: methodology
status: draft
source: alice-framework
tags: [kind:methodology, kind:board-routing, project:alice]
confidence: 0.0
links: ["[[methodology/01-decide-vault-tier.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[methodology/05-op-guards.md]]", "[[references/board-catalog.md.template]]", "[[references/kanban-lite-disciplines.md]]"]
---

# Methodology 04b — Decide your work-type routing system (board-routing)

> **Work-type routing** is the decision the operator (or the agent) makes every time a ticket is filed: "this work belongs on this board." The routing system is the substrate that makes the work graph (methodology 04a) queryable by work type. Without it, the operator faces "every ticket on the default board" or "every ticket on the wrong board."

This methodology covers **the design of a work-type routing system** — boards, the keyword-routing table, the cross-board move pattern, and the default-board pattern. The methodology is tool-agnostic. The canonical implementation in the worked examples uses the kanban board. The tool-mapping guide (`references/tool-mapping-guide.md`) says how to implement this in your specific tool.

**Documentation convention:** Alice documents the **method**, not specific instances. This doc does not list any specific board names. The friend applies the method to their own context.

---

## Part 1: Why work-type routing matters

### The decision

Every ticket file is a routing decision. The operator (or the agent) decides:
- **What kind of work is this?** (e.g., infrastructure, content, customer-facing, internal)
- **Which board accepts this work type?** (e.g., the operator's infrastructure board, content board, etc.)
- **What's the canonical pattern for moving work between boards?** (if the routing decision was wrong)

Without a routing system, the decision is implicit. The operator files on whatever board is active, and the system can't reason about "where should this work live?"

### The 3 failure modes

A work-type routing system fails in 3 predictable ways:

1. **Everything → default.** The operator files everything on the default board because it's easier than routing. The system can't tell different work types apart. The default board becomes a junk drawer.
2. **Wrong board, no recovery.** The operator files on the wrong board and doesn't notice. The work is in the wrong place. Recovery requires a cross-board move, which is a multi-step procedure. The operator avoids the recovery, so the work stays in the wrong place.
3. **Boards proliferate.** The operator creates a new board for every new work type. The board list grows unbounded. The system becomes un-navigable.

The routing system is the design that prevents all 3.

---

## Part 2: The 5-question test for board-routing

When filing a ticket, run the 5 questions:

```
1. Is this work a real, separate work-type, or a sub-task of an existing ticket?
   (sub-task → use parent/child, not a new board)
2. Is this work recurring (5+ times/week) with a stable domain?
   (no → default board)
3. Is there an existing board that owns this domain?
   (yes → use that board)
4. If no existing board, is the work-type recurring AND well-bounded?
   (no → default board; yes → consider a new board)
5. Is the work-type high-stakes (irreversible, public-facing, customer-facing)?
   (yes → requires an operator gate, see methodology 04a)
```

**Decision tree:**

- **Sub-task:** file as a child of the existing ticket (use `parent_id`)
- **Recurring with existing board:** file on the existing board
- **Recurring without existing board:** consider creating a new board
- **One-off:** file on the default board

### The default-board pattern

**Always have a default board.** The default board is the catch-all for one-off tickets. Without a default board, you'll be tempted to add a board for every work type, and the board list will grow unbounded.

The default board should be:
- The board for one-off, cross-cutting, or unknown-domain tickets
- Allowed to be a junk drawer (with periodic cleanup)
- Cheap to file on (no domain checks)

If your tool doesn't have a "default" concept, create one. It's the safety net for "I don't know where this goes."

---

## Part 3: The keyword-routing table

The keyword-routing table is the **mechanical** part of the routing system. It maps title/body keywords to boards.

### The format

```
| Title/body keywords | Board |
|---|---|
| [keyword1, keyword2, ...] | [board_name] |
```

### How to design the table

1. **Start with 5-7 keywords per board.** More than 10 keywords per board means the board is doing too much; split it.
2. **Use specific keywords, not broad terms.** "release checklist" is more specific than "build". "Customer interview" is more specific than "research". Specific keywords reduce false positives.
3. **Use multi-keyword patterns when needed.** If a single keyword produces too many false positives, use 2-3 keywords in sequence: "loop AND requirements" routes to a different board than "loop AND build".
4. **Order the table by board, not by keyword.** The operator reads the table to find a board, not to find a keyword.
5. **Test the table against 10 real tickets.** If 8/10 route correctly, the table is good. If 5/10, the keywords are too generic.

### The 4 questions for each row

For each row in the table, ask:

1. **Are the keywords specific enough?** "build" is too generic. "build-loop" is specific.
2. **Do the keywords cover the common cases?** A board with 2 keywords misses 80% of tickets.
3. **Do the keywords conflict with other rows?** "loop" appearing in 2 rows is a conflict.
4. **Is the operator's intent clear?** If the operator says "this is a loop ticket" and the table routes it to a non-loop board, the table is broken.

### When the table fails

The keyword-routing table fails when:
- A ticket doesn't match any keyword (fallback: default board)
- A ticket matches multiple keywords (priority: most specific match wins, then default)
- A keyword is too generic and produces false positives (refine the keyword)

The table is **operator-context.** Different operators have different keywords. The friend running Alice's methodology designs their own table for their own context.

---

## Part 4: The cross-board move pattern

A ticket may be filed on the wrong board. Recovery requires a cross-board move. The canonical pattern:

### The 4-step procedure

1. **Close the source ticket** with `--result` citing the superseding ticket id and the reason. The closure is auditable.
2. **Switch active board** to the target board (if your tool has an "active board" concept).
3. **Re-file on the target board** with the full body preserved. The new ticket has a new id; the old ticket is closed.
4. **Restore previous active board** (if step 2 changed it).

### Audit-line on every cross-board move

Every cross-board move must produce an audit line:

```
## [ISO8601-UTC] cross-board-move — <source_tid> -> <target_board>: <reason>
```

The audit line is the operator's evidence trail. Without it, the operator can't reconstruct the move.

### Why not "edit the ticket's board"?

Some tools support a `--target-board` flag that edits the ticket's board field. **Don't use it.** The edit is silent — the operator can't tell the ticket moved unless they look at the ticket's history. The close-and-re-file pattern is explicit and auditable.

If your tool only supports the edit-flag pattern, **add the audit line manually** every time you use it. The audit line is the operator's evidence trail; the edit-flag pattern is silent.

### Cross-board contract

Some boards have **cross-board contracts** — formal agreements about how tickets move between boards. Example contracts:
- "Board A can re-file to Board B without operator approval"
- "Board B can re-file to Board A only with operator approval"
- "Tickets moved from Board A to Board B retain their parent_id"

The contracts are operator-context. The friend running Alice's methodology designs their own contracts for their own context.

---

## Part 5: When to add a new board

Add a new board when **at least 2** of the following are true:

1. **The work-type has its own worker profile.** (e.g., a "graphic designer" agent)
2. **The work-type has its own cron cadence.** (e.g., a daily 06:00 digest)
3. **The work-type has an operator gate.** (e.g., a public-deploy ticket requires operator approval)
4. **The work-type has a separate dashboard view.** (e.g., the operator wants a board-specific dashboard)
5. **The work-type is expected to have >50 tickets over its lifetime.** (e.g., a multi-year project)

If only 1 is true, **don't add a new board.** Use the default board.

### The board-creation checklist

Before creating a new board, run through this checklist:

- [ ] The 2-of-5 test passes (at least 2 conditions are met)
- [ ] The board's domain is documented (see `references/board-catalog.md.template`)
- [ ] The board's "use for" / "do not use for" are clear
- [ ] The board's cross-board contracts (if any) are documented
- [ ] The default-board is still the catch-all for one-offs
- [ ] The new board's keyword-routing table row is added

### The board-proliferation anti-pattern

If you find yourself wanting to add a new board every 2 weeks, **stop.** The default board is doing too much. The system is over-routing.

The fix is to **remove boards**, not add them. A solo operator with 8 boards has 3 too many. Audit, demote, archive.

---

## Part 6: Documenting the board-routing system

The board-routing system is documented in two places:

1. **The board-catalog** (per-board domain, use, don't-use, cross-board contract) — see `references/board-catalog.md.template`
2. **The keyword-routing table** (per-board keyword list) — inline in the AGENTS.md or as a separate reference

### The board-catalog format

Each board gets a section in the board-catalog:

```
## <board-name> — <display-name>

**Domain:** [what work types the board accepts]

**Use for:** [list of specific work types]

**Do not use for:** [list of work types that look related but belong elsewhere]

**Routing invariant:** [any cross-board contract that holds]

**Workers (default profile stack):** [which agents work on this board]
```

The friend running Alice's methodology fills in this template for each of their boards.

### The keyword-routing table format

The keyword-routing table is a single table:

```
| Title/body keywords | Board |
|---|---|
| [keyword1, keyword2, ...] | [board_name] |
| ... | ... |
```

The friend designs their own keyword-routing table for their own context.

---

## Part 7: Common board-routing anti-patterns

### Anti-pattern 1: "I'll add a board for every work type."

No. The default board is the catch-all. New boards are for recurring, well-bounded work types with their own profile/cron/operator-gate. Most one-off work types should stay on the default board.

### Anti-pattern 2: "I'll route by agent."

No. Boards are for work-product domain, not for which agent does the work. The same agent may work on multiple boards. A ticket's board says "what kind of work this is," not "who's going to do it."

### Anti-pattern 3: "I'll move tickets freely between boards."

No. Cross-board moves are explicit, auditable, and have a canonical 4-step pattern. They are not a substitute for filing on the right board in the first place.

### Anti-pattern 4: "I'll write a board-domain definition for every new board."

No. The board-domain definition is the work the operator does BEFORE creating the board, not after. The 6-question board-creation checklist is the test, not the documentation. If the operator can't fill in the board-domain, the board isn't ready.

### Anti-pattern 5: "I'll have a board for every keyword."

No. The keyword-routing table maps keywords to boards. If a keyword appears in 3 rows, the routing is over-specified. Refine the keywords.

### Anti-pattern 6: "The keyword-routing table is exhaustive."

No. The keyword-routing table is a **mechanical heuristic**, not an exhaustive classifier. Tickets that don't match any keyword go to the default board. The default board is the safety net.

### Anti-pattern 7: "I'll never move a ticket between boards."

No. Cross-board moves are sometimes necessary. The 4-step pattern is explicit and auditable. Refusing to move is refusing to fix a routing mistake.

---

## Part 8: The board-routing system and the work graph

The board-routing system is the **substrate** of the work graph (methodology 04a). The graph's nodes are tickets; the graph's edges are parent/child + blocks/blocked_by; the graph's substrate is the boards.

### What boards give the graph

- **Domain-routing:** the graph is partitioned by work type. Different boards are different sub-graphs.
- **Cross-board contracts:** the graph has explicit rules for how sub-graphs interact.
- **Default-board sub-graph:** the default board is a special sub-graph for one-off tickets. It's not part of the long-term work graph.

### What the graph gives the boards

- **Parent/child:** tickets on the same board can be parent/child. The graph's parent/child edge is intra-board.
- **Cross-board relationships:** tickets on different boards can `blocks` or `relates_to` each other. The graph's cross-board edge is inter-board.
- **Cascading effects:** a parent ticket's transition cascades to children. If the parent is on Board A and children on Board B, the cascade crosses boards.

### The board-routing + work-graph integration

- Every ticket on a board has the work-graph state machine (ready/running/done/archived)
- Every cross-board relationship is recorded as a graph edge
- Every board has a default-board fallback for one-offs

---

## Part 9: When to revise the board-routing system

The board-routing system is **not static.** It changes when:

1. **A new work type emerges** (5+ tickets in a week that don't fit any existing board) → add a new board
2. **A board is underused** (<5 tickets in a month) → demote to default board, archive
3. **A keyword is producing false positives** (wrong board for known work) → refine the keyword
4. **A cross-board move becomes routine** (>5 moves/week) → consider whether the boards should be merged or split

The operator revises the system **when the system is the bottleneck, not before.** A system that works for 6 months doesn't need a revision.

---

## Part 10: The default-board pattern in detail

The default board is the **safety net.** It's the board for:
- One-off tickets (single tasks that don't fit any domain)
- Cross-cutting tickets (work that touches multiple boards)
- Unknown-domain tickets (work that the operator can't route)
- Test tickets (work that's intentionally not real)

### The default board is not a junk drawer

The default board can become a junk drawer if the operator files everything there. The fix:
- **The keyword-routing table is reviewed weekly.** If a keyword produces many false-positives, the keyword is refined.
- **The default board is reviewed monthly.** Items older than 30 days that should be elsewhere are re-filed.
- **The default board is a transition, not a destination.** Items in the default board should be re-filed within 30 days, not left indefinitely.

### The default board vs the inbox

The default board is a **board**, not the inbox. The inbox is the routing layer; the default board is the catch-all. A ticket in the inbox is "needs routing"; a ticket on the default board is "routed, but domain is unknown."

---

## Part 11: Maintenance

Board-routing is a low-frequency, high-blast-radius decision. The operator designs the boards once, then files against them for months — which is exactly why drift here is easy to miss. A routing table designed for four boards keeps returning plausible-looking answers after the operator has grown to seven; the mis-routes are individually cheap to fix and collectively invisible. Unlike vault tiers (which drift visibly, as notes pile into the wrong directory) or crons (which drift loudly, as a schedule stops firing), routing drift surfaces only as a slow rise in re-filed tickets.

This section is the audit surface for the routing system. It mirrors the canonical 5-subsection shape used across `methodology/` so a friend reading any maintenance section sees the same rhythm: when to audit, what counts as healthy, what drift looks like, what to do when drift is found, and when a board should be retired.

### 11.1 Audit cadence

Audit the board-routing system every **90 days**.

The cadence is deliberately slow. Board-routing decisions are infrequent — the operator adds a board every few months, not every week — so a 30-day cadence produces mostly no-op audits and trains the operator to skip them. Ninety days is long enough that something has usually changed, and short enough that drift is still one quarter deep rather than two.

The audit is a **read pass**. It does not re-file tickets, merge boards, or edit the routing table; it produces a list of drift items (per 11.3) and an action queue (per 11.4). Acting on the queue is a separate, deliberate step.

Record each audit with: date, auditor, the board list at audit time, verdict per check in 11.2, corrective actions queued, and the next-audit date (default +90 days). Store the record wherever the operator's routing decisions already live — the board-catalog, a maintenance ticket, or the methodology changelog — so the trail survives across sessions.

**Audit sooner than 90 days when:**

- A new board is created (the routing table gained a row — confirm it does not conflict with an existing one).
- A board is retired or merged (the routing table lost a row — confirm nothing now falls through to the default board that shouldn't).
- The operator's work mix changes materially (a new project, a new domain, a new recurring work type).

An off-cycle audit is **added**, not substituted — the 90-day clock keeps running.

### 11.2 Quality threshold

The board-routing system passes the audit when **all three** hold:

- **The 5-question test is recent.** The test in Part 2 has been applied — not merely read — within the last **6 months**. A routing test that has not been run against a real filing decision in half a year is documentation, not a working test. If the operator cannot name a ticket the test was applied to, the check fails.
- **The keyword-routing table is current with the operator's actual boards.** Every board in the operator's tool has a row in the table, and every row in the table names a board that still exists. A row pointing at a retired board, or a live board with no row, is a stale table.
- **Every board has a clear domain.** Each board's "use for" / "do not use for" is written down and would let a second reader route a ticket the same way the operator would. A board whose domain can only be explained in conversation does not have a documented domain.

Each check is `pass` or `revise`. There is no partial credit: a table that covers six of seven boards is stale, not "mostly current." Partial credit is how routing drift hides.

### 11.3 Drift signals

Drift is observable. Any **one** of the following is enough to queue a fix (per 11.4):

- **≥ 3 tickets in 30 days were mis-routed and had to be re-filed.** This is the primary signal. Individual mis-routes are noise — the operator was moving fast, the keyword was ambiguous. Three in a month is a pattern: the table no longer matches the work. Count re-files, not regrets; a ticket that stayed where it was filed did not drift.
- **≥ 2 boards have overlapping domains.** Two boards that both plausibly accept the same ticket force a judgment call on every filing. The overlap, not the judgment, is the defect — it will be resolved inconsistently, and the resulting split history is unrecoverable without a cross-board move.
- **The operator is routing tickets manually.** The operator reads each ticket and decides its board from memory rather than from the table. This is the quietest signal and the most serious: the routing system has been replaced by operator attention, which does not survive a busy week, a delegated filing, or an agent doing the filing instead.

A drift signal does not always mean the design is wrong. Sometimes the boards are right and the keywords are stale; sometimes the operator's work genuinely moved and the board list should follow. The audit names the signal and proposes a disposition; the operator confirms.

### 11.4 Fix actions

When drift is detected, the corrective action depends on the signal:

| Drift signal | Fix action |
|---|---|
| ≥ 3 mis-routed tickets in 30 days | Re-run the 5-question test (Part 2) against the mis-routed tickets |
| ≥ 2 boards with overlapping domains | Merge the boards, or rename them so the domain boundary is unambiguous |
| Operator routing manually | Re-run the 5-question test, then refresh the keyword-routing table from the operator's real filings |

**When drift is detected, run the 5-question test again.** The test is the diagnostic, not just the design tool. Walk each mis-routed ticket through the five questions and note which question the operator answered differently at filing time than the table implies. That question names the defect: a divergence on question 3 means the board list is wrong; a divergence on question 2 means the keyword is too generic.

**When overlap is detected, merge or rename the boards.** Two boards with overlapping domains is a design error, not a filing error, and no amount of keyword tuning fixes it. Merge when one board's domain is a subset of the other's; rename when the domains are genuinely distinct but the names do not communicate the boundary. Prefer merging — per Part 5, the fix for proliferation is to remove boards, not add them.

Do not fix drift by editing the routing table until the audit reads green. A mis-route is a routing failure or a design failure; rewriting the keyword to match observed behavior hides the cause and guarantees the same audit fails again next quarter.

Record the disposition: signal, cause, action taken, next review date. A drift signal with no recorded disposition is unresolved.

### 11.5 Retirement conditions

Retire a board when **either** of the following holds:

- **The board has had 0 tickets for 90 consecutive days.** One full audit cycle with no filings means the routing never fires. The board is either over-deployed (created for work that never materialized) or its work has silently migrated elsewhere. Both are retirement conditions.
- **The board is fully superseded by a shared board.** Another board — often the default board, or a broader board created later — now accepts everything this board was created for. Two boards for one domain is the overlap signal from 11.3, resolved by retirement rather than by merge.

Retirement is not deletion. The sequence:

1. Name the surviving board (the default board, or the board that absorbed the domain).
2. Move any open tickets to the surviving board using the cross-board move pattern (Part 4), audit line included. Closed tickets stay where they are — history is not rewritten.
3. Remove the retired board's row from the keyword-routing table and fold its keywords into the surviving board's row, dropping any that would now conflict.
4. Update the board-catalog entry, the cross-board contracts, and any agent or cron that filed to the retired board.
5. Keep the board present but empty for one full audit cycle (90 days) so late-arriving references surface. Archive it only after that cycle completes with no inbound filings.
6. Record the retirement: date, surviving board, tickets moved, references updated.

**Do not retire the default board.** The default board is the safety net for one-off and unknown-domain tickets (Part 10); it is expected to be quiet in some quarters and is exempt from the zero-tickets condition. A routing system with no default board has nowhere to put "I don't know where this goes."

### Maintenance parity check

This section defines a friend-portable method, not a claim that every platform supplies board-level ticket counts, re-file tracking, or archived-board placeholders. Before adopting it, map each function — audit query, mis-route counting, cross-board move, retirement archival — to mechanisms available in your own tool. The 90-day cadence, the 6-month test-recency bar, and the ≥ 3 / ≥ 2 thresholds are methodology defaults; adjust them when measured re-file rate, board count, or ticket volume provides better evidence, but record the exception so the audit trail stays intact.

---

## Worked example (skeleton)

The 2 worked examples (solo-founder, research-analyst) show the methodology applied to a specific instance. The examples are skeletons, not snapshots.

The friend reads the methodology, then designs their own board-routing system for their own context. The friend does NOT copy the worked example's boards; they apply the method to derive their own.

---

## See also

- `methodology/01a-decide-memory.md` — **the board-catalog is long-form memory (Tier 5).** The catalog is documented in a vault atomic note; the keyword-routing table is in cold-start memory (AGENTS.md); the routing decisions are in episodic memory (audit lines + kanban events).
- `methodology/04a-decide-work-graph.md` — boards are the work-graph substrate; cross-board moves are graph actions.

## What's next

- `methodology/05-op-guards.md` — the operational guard "agents must check routing before filing" depends on the board-routing methodology
- `methodology/06-iteration-loop.md` — the loop's detect step can scan the routing system for stuck tickets
- `references/board-catalog.md.template` — the fillable form for documenting each board

## Anti-patterns to watch for (summary)

1. **"I'll add a board for every work type."** → No. The default board is the catch-all.
2. **"I'll route by agent."** → No. Boards are for work-product domain.
3. **"I'll move tickets freely."** → No. Cross-board moves are explicit and auditable.
4. **"I'll write board-domain definitions after the fact."** → No. The definition is pre-work, not post-work.
5. **"I'll have a board for every keyword."** → No. Refine keywords, not boards.
6. **"The keyword-routing table is exhaustive."** → No. The default board is the safety net.
7. **"I'll never move a ticket between boards."** → No. Cross-board moves are sometimes necessary.

## Touch-points with other methodology docs

This doc touches:

- `methodology/01-decide-vault-tier.md` — board-routing is parallel to vault-tier routing but for the ticket domain
- `methodology/04a-decide-work-graph.md` — boards are the work-graph substrate; cross-board moves are graph actions
- `methodology/05-op-guards.md` — operational guards for board-routing (e.g., "agents must check routing before filing")
- `methodology/06-iteration-loop.md` — the loop's detect step can scan the routing system for stuck tickets
- `references/kanban-lite-disciplines.md` — already mentions boards, should reference this doc
- `templates/inbox-route.md.template` — ticket-routing is a special case of inbox routing
