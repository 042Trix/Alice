---
id: alice-methodology-00-decide-ticket-naming
created: 2026-08-05T14:30:00Z
updated: 2026-08-11T23:58:00Z
title: "Methodology 00 — Decide ticket naming"
type: methodology
status: draft
source: alice-framework
tags: [kind:methodology, kind:naming, kind:ticket, kind:universal-discipline, project:alice]
confidence: 0.0
amended_by: ["[[ticket:t_94c0c7cf]]", "[[ticket:t_6bbbe1ed]]", "[[ticket:t_7be253bd]]"]
version: 0.4.4
links: ["[[methodology/04c-decide-master-ticket.md]]", "[[methodology/04b-decide-board-routing.md]]", "[[~/.hermes/methodology/M-decide-alice-versioning.md]]", "[[~/.hermes/methodology/M-decide-worker-stall-detection.md]]", "[[methodology/M-decide-instance-vs-framework.md]]", "[[templates/AGENTS.md.template]]", "[[references/board-routing.md]"]
teaching-example: true
---

# Methodology 00 — Decide ticket naming

> A ticket's title is the operator's first read. The body is the source of truth; the title is a navigation aid. This methodology prescribes the format that makes tickets scannable in list view and tells future authors how to use it.

Alice's tickets become a long list over the lifetime of a project. Reading the body of every ticket to know what it covers does not scale. The title has to carry enough information that the operator can identify the work in two or three seconds. This methodology defines the format and the rules that make it work.

---

## Part 1: The format

Every ticket filed on the `alice-framework` board uses the following two-line shape:

```
AREA
<topic-name>: <descriptive name>
```

Three structural rules:

1. **Line 1 is the AREA.** Capitalized. The methodology cluster the ticket belongs to. Examples in current use: `Harness`, `Meta`, `Graph`, `Loop`. AREA is the *axis of grouping*.
2. **Line 2 has two parts:** a short `topic-name` (2-4 words, lowercase, noun phrase) followed by a colon, followed by a one-line `descriptive name` that says **what the ticket covers** in plain language.
3. **No topic numbers.** Numbering is implicit in the order tickets are filed. `Harness / memory vs context` and `Graph / flow spec fields` are independent counters; their numbering never collides and never needs to align.

The colon is the visual cue that separates the topic from the descriptive name. Em-dashes are fine inside the descriptive name (for sub-clauses), but **no em-dash on the topic-name line** — that's reserved for the descriptive clause in v2's vocabulary, and v3 promotes the colon to the canonical separator.

The format applies to every ticket on every board (per Part 3 §Universal title discipline). The two-line shape is the canonical expression; other formats that carry a descriptive topic are also valid (e.g., `[MASTER] / <descriptive-name>` for masters), as long as the operator can identify the work in 2-3 seconds from the list view.

### Why this format

Three problems the format is designed to solve:

1. **List view collapses long titles.** Most kanban boards truncate title display around 60–80 characters. A long-form title like "Add the memory-vs-context distinction to the memory methodology doc" gets cut off in the middle of the clause. The two-line AREA + topic + descriptor shape keeps the scannable part visible on line 1.
2. **A single ticket covers one EXTEND or NEW.** Mixing create-vs-modify in one title makes it impossible to route the work. The descriptive name announces the action ("extend 01a with ...", "new methodology doc ...") so the reader knows what kind of work to expect.
3. **Topic numbers were a primitive, not a priority.** v2 used `TOPIC <N>` as a grouping index. But the index became its own bookkeeping tax: which number is next? does this ticket belong under H-3 or H-4? Operators reading a list do not benefit from the index because the list is already sorted by AREA + recency. v3 drops the number entirely; AREA + topic-name replaces AREA + TOPIC + parenthetical.

---

## Part 1.5: Navigation aid — the 3-layer stack (HARNESS / LOOP / GRAPH)

> **Navigation aid (2026-mid-year public framing). Alice's canonical schema is the AREA taxonomy.**

The 3-layer stack below is a reading-onramp for newcomers. It is NOT the canonical schema, which remains the AREA taxonomy from Part 1 (decision-ordered: HARNESS → LOOP → GRAPH, with the operational cross-cuts applied per ticket). The diagram tracks the 2026-mid-year public discourse on layered agent systems — see the **5-seat council verdict** for Alice (digest v0.2.0, Section 9 "Individual council / agent responses", Strategist seat) for the citation context (Addy Osmani, Lilian Weng, Peter Steinberger, sarthakai). No live URLs are embedded here; the canonical reference is the council log.

```
┌─────────────────┐    ┌──────────────────────┐    ┌───────────────────────┐
│    HARNESS      │    │        LOOP          │    │        GRAPH          │
│  (per-agent     │ →  │  (build → verify     │ →  │  (fan-out, fan-in,    │
│   profile)      │    │   → retry)           │    │   sub-agents)         │
└─────────────────┘    └──────────────────────┘    └───────────────────────┘
                         ▲       │
                         └───────┘ (verifier-FAIL re-enters LOOP)
```

How to read it alongside the AREA taxonomy:

- **HARNESS** is the per-agent configuration (model, tools, profile, system prompt). This is the row that the AREA taxonomy's "who runs this ticket" column references, not a separate axis.
- **LOOP** is single-agent iteration: build → verify → retry. The retry loop is local; the verifier is the same agent's verifier-gate or a paired verifier profile.
- **GRAPH** is multi-agent fan-out / fan-in: orchestrator + downstream agents + sub-agents. The dashed feedback arrow from GRAPH back to LOOP represents the case where a graph-level verifier fails and the work re-enters a single-agent LOOP for the next iteration. This is a separate diagram (the verifier-node feedback callout, captured in `methodology/04a-decide-work-graph.md` Part 3.5) — drawn here as a dotted return arrow for orientation only.

For builders, the AREA taxonomy in Part 1 is authoritative — every ticket names its AREA, and the routing / verification / op-guard rules apply per-AREA. The 3-layer diagram is a way in for readers who already know the public 2026-mid-year framing and want to map it onto Alice's canonical schema without reading the full methodology first.

---

## Part 2: The six topic-name rules

These rules cover line 2. Line 1 (the AREA) is mechanical; line 2 is where the work happens.

1. **Topic name = 2-4 words.** A noun phrase the operator can scan in a list view. Anything longer dilutes the scan; anything shorter is cryptic.
2. **Descriptive name = 1 line.** Says **what the ticket covers**, in plain language. Avoid clauses that need the body to decode; the title should be self-explanatory in two or three seconds.
3. **Use "new" or "extend"** in the descriptive name when the work is create-vs-modify. (`new methodology doc 04c...` or `extend 02 with ...`.) Naming the action verb in the title removes a round-trip to the body.
4. **No em-dash in the topic-name line.** Use a colon (`:`) to separate the topic name from the descriptive name. Em-dashes are fine inside the descriptive name (for sub-clauses): *"extend 04a with required task metadata and the 3-exit-flows rule"* uses an em-dash and that is correct.
5. **Topic numbers are NOT used.** `Harness / memory vs context` and `Graph / flow spec fields` are independent. Numbering is implicit in the order tickets are filed (first HARNESS ticket is `Harness / memory vs context`; the next HARNESS ticket is whatever its topic is — the order doesn't carry meaning).
6. **Lowercase in the topic-name line** unless a proper noun. The AREA on line 1 is capitalized. Generic topic descriptors stay lowercase; proper nouns (`Alice`, `Kanban`, `Methodology doc 04c`) stay capitalized wherever they appear.

### Edge cases the rules do not cover

- **A ticket that touches more than one AREA.** File the ticket under the primary AREA and mention the secondary AREA in the descriptive name. The primary is the AREA whose methodology doc is the artifact; the secondary is the AREA whose method is referenced. Do not invent a new AREA to cover a one-off crossover.
- **A ticket that is a follow-up to a previous ticket.** File under the same AREA. Pick a topic name that scans as a sibling (`Harness / memory vs context` → next memory topic is `Harness / memory persistence`). Topic-name reuse is allowed when it scans as the same bucket; reuse is discouraged when it scans as a different topic.
- **A ticket that is a meta-ticket about the methodology itself.** The convention is `Meta` as the AREA. The `Meta` AREA is the umbrella for tickets that describe how the methodology is structured, not what the methodology says. The current ticket (`t_d727f13f`) is itself a meta-ticket about this convention.

---

## Part 3: Universal title discipline (every ticket, every board)

The two-line AREA + topic + descriptor format in Part 1 is the canonical shape for `alice-framework` tickets. The **title-discipline contract itself is universal** — it applies to every ticket filed on every board, by every profile.

### The rule

A ticket's title MUST be **descriptive of the topic** in plain language. The title is the operator's first read across a list of peers; with 50+ tickets on a single board, identical titles (or titles that are URL-only, file-path-only, or source-ticket-only) make the work unreadable.

**Three universal rules:**

1. **Title carries the topic.** The title includes the operator-facing topic name (e.g., "x-article-review of rari's 3-layer agent-stack piece", "rename end_session_handoff.py to v0.3.0 with validator") — never the URL, post ID, source ticket ID, or file path alone.
2. **Body carries the source.** The body has a `## Source` block with the URL, post ID, source ticket, file path, or other reference. The title can include a short slug if it aids scannability (e.g., "rari 3-layer-stack"); the URL does NOT appear in the title.
3. **List-view survival.** Most kanban UIs truncate titles around 60-80 characters. The descriptive topic must fit in that budget. The full URL + boilerplate does NOT.

### Scope of the rule

This rule applies to:

- All ticket types: master, child, META, follow-up, retro, compliance verifier, code review, ad-hoc.
- All boards: alice-framework, hermes, agent-resources, default, loop-builder, msaas-finder, requirements, patchwork, lawnsvc.
- All profiles: jarvis, doc-writer, coder, verifier, planner, council, business-analyst, marketing, msaa-scout, web-research, default.

### Anti-patterns

- `Update ticket X` — title is a source reference, not a topic.
- `https://y.com/...` — title is a URL, not a topic.
- `t_xxxx` — title is a ticket ID, not a topic.
- `~/.hermes/profiles/foo/SOUL.md` — title is a file path, not a topic.
- `META` — title is a structural prefix, not a topic. (META is the structural type; the descriptive topic goes on line 2.)
- Identical titles across 50+ tickets in a list view — operator cannot identify which ticket is which.

### Correct patterns

- `[MASTER] x-article-review / rari 3-layer-stack — review + council verdict + retro`
- `doc-writer: amend methodology/00-decide-ticket-naming.md with universal title discipline`
- `coder: harden end_session_handoff.py with validator + length budget + env override`
- `verifier: compliance gate for master-title discipline cluster (5 checks)`
- `META / session-handoff-protocol-overhaul: 3 infrastructure fixes + Alice M-decide-session-handoff.md`

### Master tickets

Master tickets follow the universal rule (above) with one addition: the `[MASTER]` prefix. See `methodology/04c-decide-master-ticket.md` Part 8 §Master Title Discipline for the master-specific application. Masters MUST include a descriptive request summary; the URL or source reference goes in the body under `## Source`.

---

## Part 4: Worked examples

### Current HARNESS series (already filed, kept under their old titles — see Part 5)

The six HARNESS tickets filed under v2 are out of scope for renaming; they are listed here so the reader sees what the v2 → v3 mapping looks like:

| v2 title | v3 title (for reference only — not applied) |
| --- | --- |
| `AREA (Harness) TOPIC 1 (memory vs context — extend 01a with the memory-vs-context distinction)` | `Harness\nmemory vs context: extend 01a with the memory-vs-context distinction` |
| `AREA (Harness) TOPIC 2 (master ticket — new methodology doc 04c-decide-master-ticket)` | `Harness\nmaster ticket: new methodology doc 04c-decide-master-ticket` |
| `AREA (Harness) TOPIC 3 (skill 1-layer rule + no implicit model knowledge — extend 02 with the no-nesting and no-implicit-model-knowledge rules)` | `Harness\nskill 1-layer rule: extend 02 with the no-nesting and no-implicit-model-knowledge rules` |
| `AREA (Harness) TOPIC 4 (work-graph metadata + 3-exit-flows — extend 04a with required task metadata and the 3-exit-flows rule)` | `Harness\nwork-graph metadata: extend 04a with required task metadata and the 3-exit-flows rule` |
| `AREA (Harness) TOPIC 5 (operator-can-but-shouldn't + polling exemption — extend 08 and 04 with the operator-bypass rule and the polling exemption)` | `Harness\noperator-can-but-shouldn't: extend 08 and 04 with the operator-bypass rule and the polling exemption` |
| `AREA (Harness) TOPIC 6 (spec hierarchy — new methodology doc covering request doc → architecture → impl plan → tests → tickets)` | `Harness\nspec hierarchy: new methodology doc covering request doc → architecture → impl plan → tests → tickets` |

### Future GRAPH and LOOP series (filed under v3 going forward)

```
Harness
memory vs context: extend 01a with the memory-vs-context distinction

Harness
master ticket: new methodology doc 04c-decide-master-ticket

Harness
skill 1-layer rule: extend 02 with the no-nesting and no-implicit-model-knowledge rules

Harness
work-graph metadata: extend 04a with required task metadata and the 3-exit-flows rule

Harness
operator-can-but-shouldn't: extend 08 and 04 with the operator-bypass rule and the polling exemption

Harness
spec hierarchy: new methodology doc covering request doc → architecture → impl plan → tests → tickets
```

```
Graph
flow spec fields: the 6-field flow spec — goal, inputs, outputs, success criteria, retry parameters, escalation

Graph
node types: the 3-node-type taxonomy — Human, Scripts/Code (deterministic), Agents (non-deterministic)

Graph
exit flows: 3 exit flows per node — success, fail, error; reconcile with 04a's 4-state model

Graph
retro + feedback: every graph ends with a 1-day-open retro that improves the next execution

Graph
flow derivation: when flows are discrete-defined vs dynamically-generated

Loop
feedback element: add to iteration-loop as the 6th element alongside trigger, goal, action, evidence, stop rule

Loop
self-improvement: how a graph retro improves the next execution; ties to Graph retro + feedback
```

Three observations:

- Graph and Loop are independent AREAs. Each starts with whatever its first ticket's topic is. Their numbering is implicit in filing order.
- The descriptive name is detailed enough that the reader does not need to open the ticket body to know what the work is.
- Em-dashes live *inside* descriptive names (for sub-clauses) but never on the topic-name line. The colon is the separator between topic and descriptor.

---

## Part 5: Out of scope

The convention does not apply to:

- **Existing tickets.** The six HARNESS tickets already filed under v2 (`t_c490ace1`, `t_b50f1546`, `t_d17af817`, plus the three HARNESS tickets filed 2026-08-05 afternoon — see `kanban list --board alice-framework` for the canonical set) keep their old titles. Renaming is per the doc-writer only; the doc-writer does not edit tickets directly during a single doc-writer pass. A rename pass is its own ticket.
- **The two-line AREA + topic + descriptor format** (Part 1) applies to the `alice-framework` board specifically. Other boards (e.g. `default`, `hermes`, `patchwork`, `msaa-pipeline`, `lawnsvc`) follow their own naming conventions. The **universal title-discipline contract** (Part 3) — title carries the topic, body carries the source, list-view survival — applies to every board. Cross-board tickets keep their originating board's two-line format (or its equivalent); only the title is preserved, the body is rewritten for the new board.
- **The ticket body.** The body is the source of truth. The title is a navigation aid. If the title and the body disagree, the body wins and the title is renamed in a follow-up ticket.

### What the convention does not yet cover

Two related questions that are out of scope for *this* methodology and reserved for a future ticket:

- **Renaming protocol.** When a ticket's title needs to be updated (e.g. the work shifts scope mid-flight under v3), what is the rename protocol? Is the original title kept in the comment thread? Does the new title get a `(formerly: ...)` marker? This is reserved for a future `Meta` AREA ticket.
- **Cross-board migration.** When a ticket moves from `alice-framework` to a different board, what title does it carry? The current convention says "preserved as-is." That may be wrong; it is a separate question.

---

## Part 6: Relationship to the operator-facing surfaces

The convention is enforced in three places:

1. **The ticket body.** Every newly filed ticket on `alice-framework` must use the two-line format. The body is the source of truth; the format is non-negotiable.
2. **The doc-writer's cold-start context.** The doc-writer's `AGENTS.md` (built from `templates/AGENTS.md.template` Section 7 — the kanban lifecycle section) makes the convention explicit. Future doc-writer runs do not re-derive the convention from the ticket body; they read it from the bootstrap.
3. **The references doc.** `references/board-routing.md` is the cross-reference target for the convention. When a ticket asks "which board does this go to?" the answer is `alice-framework`; the ticket-title format is the next question, and `references/board-routing.md` redirects to this methodology doc.

### Why the convention lives in three places and not one

Redundancy is the point. The ticket body is the source of truth for the *current* ticket. The doc-writer's cold-start context is the source of truth for the *next* ticket. The methodology doc is the source of truth for the *rule*. Each surface has a different consumer with a different failure mode; losing any one surface degrades the experience without breaking it.

---

## Revision history

- **v1 (initial, 2026-08-05 morning):** no format; titles like "Card H Fix 1" or "H-1: Extend 01a..." (operator-flagged as confusing — operators cannot tell what's a card, what's a fix, and what's a method-ticket).
- **v2 (2026-08-05 morning → afternoon, `t_d17af817`):** `AREA (<Section>) TOPIC <N> (<descriptive name — ...>)` introduced. Added the AREA-parens, TOPIC-number, and parenthetical-with-em-dash structure. Solves the "what's a ticket" scan problem but introduces bookkeeping tax (the operator has to track TOPIC numbers per AREA) and a verbose shape that fights list-view truncation. Superseded.
- **v3 (2026-08-05 afternoon, `t_d727f13f`):** two-line `AREA\n<topic-name>: <descriptive name>`. Dropped the parenthetical section label, dropped the TOPIC number, and dropped the em-dash from the topic-name line. The colon becomes the canonical separator between topic and descriptor; em-dash survives only inside the descriptive name. Topic numbering is implicit in filing order. Operator's direct example, two lines, no em-dash, no topic number.
- **v4 (2026-08-08, `t_94c0c7cf`, redirect of `t_2a513f60`):** added Part 3 (Universal title discipline) — the rule that every ticket title carries a descriptive topic, the body carries the source identifier, and the title survives 60–80 char list-view truncation. The rule is universal: applies to every ticket type, every board, every profile. Master tickets get a `[MASTER]` prefix on top of the universal rule (cross-ref `04c` Part 8 §Master Title Discipline). The two-line `AREA\n<topic-name>: <descriptive name>` format (Part 1) remains the canonical shape for `alice-framework` tickets. Triggered by operator direction 2026-08-08: "this 'descriptive master task title' should apply to all tickets, not just the x articles." Verified violator: `t_62a99460` (URL-only title).
- **v4.1 (2026-08-11, `t_6bbbe1ed`):** paired-wiki for `M-decide-alice-versioning.md` (added `[[methodology/M-decide-alice-versioning.md]]` link). No contract change to 00 itself; only the `links` frontmatter bumped per op-guard-5.
- **v4.2 (2026-08-11, `t_4d9bd6c5`):** corrected the `M-decide-alice-versioning.md` link to the instance-side path `[[~/.hermes/methodology/M-decide-alice-versioning.md]]` (per operator direction 2026-08-11: M-decide-* docs are Hermes-instance, not alice-framework). Added `[[methodology/M-decide-instance-vs-framework.md]]` link per op-guard-5 paired-wiki (the framework-vs-instance rule shipped in `t_7aa96032` is the decision-tree the versioning rule is a specialization of).
- **v4.3 (2026-08-11, `t_7be253bd`):** paired-wiki for `M-decide-worker-stall-detection.md` (added `[[~/.hermes/methodology/M-decide-worker-stall-detection.md]]` link). The new methodology codifies the forward-progress stall detection rule (heartbeat is liveness, not progress; 5-signal taxonomy in priority order; auto-reclaim on 1st stall, auto-block kind=stall_repeat + operator-DM on 2x stall on same task; per-profile config block; cron registration shape). No contract change to 00 itself; only the `links` frontmatter bumped per op-guard-5.
- **v4.4 (2026-08-11, `t_7be253bd`):** paired-wiki amendment for `M-decide-worker-stall-detection.md` v0.1.0 → v0.1.1 (operator refinement: monitoring + tuning feedback loop). Added Part 11 §8.1-8.5 to the methodology (structured jsonl log, daily aggregator, auto-tuning proposer, weekly operator review). No contract change to 00 itself; only the revision-history + `amended_by` field is preserved per op-guard-5 paired-wiki integrity discipline. The methodology is instance-side per `M-decide-instance-vs-framework.md`; this is a PATCH-level amendment to the instance doc, not an alice-framework framework version bump.

---

## Worked example

See `worked-examples/01-solo-founder-skeleton/methodology-notes/00-ticket-naming.md` (when v0.0 ships) for an anonymized example of two teams adopting the same convention across boards.

## See also

- `methodology/04c-decide-master-ticket.md` — the master ticket is the parent of every methodology ticket. The convention here applies to the master's children.
- `methodology/04b-decide-board-routing.md` — the board-routing doc answers "which board does this go to?" The ticket-naming convention here answers "what does the title look like once it lands on `alice-framework`?"
- `templates/AGENTS.md.template` Section 7 — the cold-start bootstrap for the doc-writer. Section 7 is where the ticket-naming convention is restated for the agent that files tickets.
- `references/board-routing.md` — the cross-reference target. When the operator (or the agent) asks "what is the format?" the answer chain is: this methodology doc → `references/board-routing.md` → the ticket body.

## What's next

- `methodology/04c-decide-master-ticket.md` — every methodology ticket is a child of a master. Read this to understand the parent-child structure that the naming convention subscribes to.
- `methodology/04b-decide-board-routing.md` — the board-routing rule determines which board the ticket lands on. The naming convention is the next layer down: once the board is chosen, the title is constrained.

## Anti-patterns to watch for

1. **"I'll write the title later."** → The title is filed when the ticket is filed. Writing the title last leads to a body that has matured beyond the title. The title is the contract; the body is the fulfillment.
2. **"The title should describe the work in detail."** → The title is a navigation aid, not the body. The AREA on line 1, the topic + descriptor on line 2 is enough. The body is where the detail lives.
3. **"Topic numbers are priority."** → There are no topic numbers in v3. AREA groups the work; the board's priority field carries priority. Do not reintroduce topic numbers "for ordering"; AREA + recency does that already.
4. **"The format is too rigid."** → The format is the format. The fixed parts (AREA, topic-name, descriptive name) carry the structure; the descriptive name carries the variation. If the work does not fit the format, the work is a meta-ticket (use `Meta` as the AREA) and the format holds.
5. **"Existing tickets should be renamed to match."** → Renaming is a separate ticket. The convention is forward-looking; existing tickets keep their titles until a rename pass is filed.
6. **"Use a hyphen or em-dash on the topic-name line."** → The colon (`:`) is the separator between topic name and descriptive name. Em-dashes live inside the descriptive name (for sub-clauses); colons live on the boundary.
