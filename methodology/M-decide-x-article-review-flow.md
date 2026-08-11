---
id: alice-methodology-M-decide-x-article-review-flow
created: 2026-08-07T12:30:00Z
updated: 2026-08-10T20:00:00Z
version: 0.5.0
alice-ticket: t_c11e4845
amendment-ticket: t_cd29b136
instance-ticket: t_673bcf04
amended_by: ["[[ticket:t_c11e4845]]", "[[ticket:t_84782c7c]]", "[[ticket:t_1c61577c]]", "[[ticket:t_cd29b136]]"]
amendment: 4
title: "Methodology M — Decide the x.com article-review flow (read → research → compare → synthesize; one master per link)"
type: methodology
status: active
source: alice-framework
tags: [kind:methodology, kind:meta, kind:article-review, kind:master-ticket, kind:external-source, kind:operator-review, kind:pdf-format, kind:source-attribution, kind:title-discipline, kind:ship, kind:alice-only, kind:hitl-approve, kind:topic-only, kind:conditional, project:alice]
confidence: 0.9
promoted_from: _inbox/M-decide-x-article-review-flow.md
promoted_by: t_ba4b9a90
intent: ~/.hermes/loops/intents/x-article-review-intent.md
links: ["[[methodology/04c-decide-master-ticket.md]]", "[[methodology/04d-decide-flow-spec.md]]", "[[methodology/04a-decide-work-graph.md]]", "[[methodology/06a-decide-retro-v2.md]]", "[[methodology/09-inbox-from-external-sources.md]]", "[[methodology/07-council-methodology.md]]", "[[methodology/03b-decide-operator-agent-interaction.md]]", "[[methodology/M-decide-alice-publish-flow.md]]", "[[references/x-ingestion-pipeline.md]]", "[[_inbox/M-decide-spec-first-flow.md]]", "[[_inbox/M-decide-parity-check.md]]"]
---

# Methodology M — Decide the x.com article-review flow (v0.5.0)

> The operator posts an x.com article link and asks one question: **does this contain anything that would make Alice a more complete framework?** This doc defines the flow that answers it. Each link becomes one master ticket with four verifier-gated phases — read, research, compare, synthesize — and the operator dispositions each finding ACCEPT / REJECT / DEFER.

This is a META doc. It describes how the operator's agents review external articles **to improve the Alice repo itself**. It is operator-internal process. **v0.5.0 (2026-08-10, t_cd29b136, amendment 4):** Added the conditional Alice-only GitHub ship lane. New `## Part 7.5 — META done-gate (extended)` codifies that when an ACCEPTED finding produced an Alice-doc amendment in the apply set, the META done-gate gains a `push-done` precondition (in addition to Retro-A + Retro-H + compliance-PASS); when no Alice amendment is in scope, META closes at compliance-PASS as before. New `## Part 7.5.1 — Conditional Alice-only GitHub ship` sub-section codifies the 6-step pre-ship sequence (collect changed files → topic extractor with forbid-list → commit-message drafter with forbid-list + max-5-topic rule → PR diff packager → operator HITL approve gate → `git push` + optional `v<X.Y.Z>` tag) + the audit-trail contract (`push-done: <commit-sha> at <ISO> — <release-url> — <topic-list>` audit-line per op-guard-11; vault copy at `2-ATOMIC/decisions/article-<id>-release-notes.{md,pdf}`; master ticket records commit-sha + release-url + topic-list). Part 3 §Release-message discipline extended so the topic-only contract covers the git commit message AND the GitHub release notes (with forbid-list enforced by a pre-push linter, not human judgment). Part 5 dispatch table extended to record that the ship lane is run by `jarvis` via the `alice-publish` loop (`~/.hermes/loops/hermes.yaml`) with `run_with_profile: coder` for technical prep + `assignee: jarvis` for the HITL approve gate. Companion instance changes ship as Alice-second children per op-guard-17: a coder child ships the pre-push topic-linter tool + audit-line appender + push wrapper + push-done evidence helper; a loop-updater child amends `~/.hermes/loops/hermes.yaml` alice-publish entry; a compliance-verifier child extends the META done-gate to require push-done. Per op-guard-5, no other methodology doc requires paired-wiki amendment for v0.5.0 (M-decide-human-digest.md is unchanged — release-notes PDF flows through its existing canonical 8-section shape, not the retro specialization). Reference: `/Users/homestead/alive_x_apply_graph_v2.{html,png}` (the v2 visual with the indigo conditional ship lane). **v0.4.1 (2026-08-09, t_1c61577c):** Added the `kind:title-discipline` metadata marker while preserving the existing v0.2.3 rename-application contract in Part 3. The title contract remains: a `## Rename` comment is a request that MUST be applied with `hermes kanban edit --title` on the next orchestration tick; the orchestrator records `## Rename applied` or `## Rename failed` evidence. **v0.4.0 (2026-08-09, t_a92c1f88):** New `## Release-message discipline` sub-section under Part 3 codifies that the operator-facing Discord message that announces an alice-framework release MUST describe the **topic(s) changed**, NOT the article author. Canonical format: `Alice v<X.Y.Z>: <topic-1>, <topic-2>, ...`. The article stays in the master ticket body `## Source` block for the audit trail; the vault audit-line + methodology amendment rationale + CHANGELOG `## Source` sub-section + GitHub release notes may still cite the article as one source among several, but the operator-facing release message is topic-only. Companion instance changes (per op-guard-16 + op-guard-17 alice-first/instance-second): `~/.hermes/loops/intents/alice-publish-intent.md` v0.3.0, `~/.hermes/loops/hermes.yaml` `alice-publish` v0.3.0 (loop yaml amendment), `~/.hermes/tools/post_alice_release_discord.py` (NEW; Discord delivery per canonical format), `~/.hermes/tests/test_post_alice_release_discord.py` (NEW; 4-case regression suite). The methodology is the alice-first; the intent + script + loop-yaml + test ship as instance children; a verifier child confirms the instance matches the methodology rule on first publish under v0.4.0 (op-guard-17 compliance-gate). Source: operator 2026-08-09 Retro-H body observation #3 (verbatim: *"The message on the push should mention what topic was changed but not the article author, the article was just the inspiration for the change."*). **v0.3.0 (2026-08-09, alice-ticket `t_c11e4845` / instance-ticket `t_673bcf04`):** Phase 4 council verdict MUST surface the Phase 2 web_search findings per finding, not just the article URL. New mandatory sub-section §"Source-attribution discipline" under Part 7 codifies per-finding evidence anchors pointing to the Phase 2 sources the council actually considered. The `## Evidence anchors` section of the executive-reporting digest MUST list the Phase 2 web_search URLs by finding so the operator can audit the council's source set in 10 seconds. Paired with `~/.hermes/loops/intents/x-article-review-intent.md` v0.6.0 (intent worker_prompt Phase 4 + verify gates) + `~/.hermes/tools/post_council_verdict_pdf.py` (script that populates the Evidence anchors section). Per op-guard-5 paired-wiki integrity + op-guard-17 alice-first/instance-second/compliance-gate, the doc-writer ships the methodology amendment; the coder applies the spec to the script + intent in instance children. **v0.2.3 (2026-08-09, t_84782c7c):** Part 3 rename discipline strengthened — a `## Rename` comment is now a request that MUST be applied via `hermes kanban edit --title` on the next orchestration tick, not a soft suggestion. Added a new `## Title-rendering discipline` sub-section under Part 3 codifying the orchestrator's rename-application contract + the failure surface when the rename cannot be applied. The 80-char title budget + the title-recovery ladder (xurl → web_search → browser → post-id fallback) are flow-agnostic in `methodology/04c-decide-master-ticket.md` Part 8 §Master Title Discipline (v0.1.3 amendment in the same change set, paired per op-guard-5). The companion instance change is `~/.hermes/tools/file_x_article.py` (8th-method title-recovery ladder + post-creation 80-char assertion + `## Title source` block); a coder child ticket ships the script + 6-case regression suite. **v0.2.2 (2026-08-07, t_e35032aa):** added a mandatory §"The 5-step operator-review PDF delivery" sub-section under Part 7 (Phase 4 council synthesis) so the council-verdict PDF rule is owned at the methodology level (not just the loop intent doc). Paired with `methodology/03b-decide-operator-agent-interaction.md` Part 13; the intent doc v0.2.2 (`t_f99d8fb7`) implements the protocol. Per op-guard-16 (spec-first flow), the doc-writer ships the methodology; the council/skill-curator lane applies the spec to the skill body. **v0.2.1 (2026-08-07, t_ea62fcff):** Phase 4 now selects council seats with the three-signal algorithm. **v0.2.0 (2026-08-07, t_ba4b9a90):** promoted from `_inbox/` to canonical `methodology/` after article-1 retro F-1..F-8 ACCEPTED. Paired intent: `~/.hermes/loops/intents/x-article-review-intent.md` v0.2.0.

It is a specialization of `methodology/09-inbox-from-external-sources.md`. The 09 doc routes an external item into a vault tier. This flow does something narrower and more demanding: it asks whether the item changes a **specific existing framework**, and it requires the answer to survive a verifier who checks that every claim about Alice's contents is true.

---

## Part 1: Why this flow needs a master ticket

Apply the 4-question test from `methodology/04c-decide-master-ticket.md` Part 2:

| # | Question | Answer for one article link |
|---|---|---|
| 1 | Multi-step? | **Yes** — read, research, compare, synthesize. |
| 2 | Multi-board? | Usually no. Accepted findings may route to the doc board later. |
| 3 | Multi-child? | **Yes** — four phase children plus verifier children. |
| 4 | Operator-originated? | **Yes** — the operator posted the link and holds the decision. |

Question 4 is yes and questions 1 and 3 are yes. **One master ticket per link.**

One master per link, not one master per batch. Batching three articles into one master destroys the thing the master exists to provide: a durable answer to "what did we conclude about *that* article?" Articles are independent evidence; their findings are independently dispositioned; their masters are independent roots.

### Why the review is not just "read it and tell me"

An article is a claim about how to build something. Alice is also a claim about how to build something. The interesting output is not a summary — it is the **relationship** between the two claims, and there are only three relationships worth reporting:

- The article describes something Alice already covers (**covered**).
- The article describes something Alice does not cover (**gap**).
- The article describes something Alice covers *differently, in a way both cannot be right* (**contradiction**).

A summary tells the operator what the article said. A classification tells the operator what to do. The flow exists to produce the second.

---

## Part 2: The 6-field flow spec

Per `methodology/04d-decide-flow-spec.md`, the flow ships with all six fields.

```
FLOW: Review one x.com article for additions to Alice

GOAL
Produce a classified findings set for one x.com article — each finding marked
covered / gap / contradiction, each citing the article and the Alice doc it
bears on — sufficient for the operator to disposition ACCEPT / REJECT / DEFER.

INPUTS
  required:
    - the x.com article URL (from the operator)
    - the Alice repo working tree (methodology/, references/, templates/)
    - methodology/09-inbox-from-external-sources.md (the routing parent)
  optional:
    - the operator's stated reason for posting the link (sharpens relevance)

OUTPUTS
    - the master ticket body, updated with the accumulated findings (this IS
      the findings doc; there is no separate file)
    - one child ticket per phase, each completed with evidence
    - for each ACCEPTED finding, one implementation child ticket

SUCCESS CRITERIA
  a. The article was actually retrieved — a verbatim quote from the body text
     appears in the phase-1 child. (Not the title. Not the preview card.)
  b. Every finding names the specific Alice file it bears on, by path.
  c. Every claim about Alice's contents is true — verified by an agent that
     did not write the comparison.
  d. Every finding carries exactly one classification.
  e. Findings are indexed F-1..F-N and each has a disposition line.
  f. No operator-instance content leaks into anything repo-bound.

RETRY PARAMETERS
  - Retrieval (phase 1): up to 4 attempts, escalating method (see Part 4).
    A login wall or 404 is NOT retried with the same method — escalate method
    or block. Never retry a paywall by re-requesting the same URL.
  - Agent nodes (phases 2-4): up to 2 attempts, backoff 1 min then 10 min.
  - Verifier nodes: no retry. A verifier rejection returns work to the phase
    it gated; it does not re-run the verifier against the same artifact.
  - Human nodes (operator disposition): no retry; escalate per below.

ESCALATION PROCESS
  - Retrieval exhausted after 4 attempts → kanban_block(kind=capability) on the
    phase-1 child, reason naming each method tried and its failure mode. The
    master stays running. The operator can paste the article text directly,
    which converts the blocker into an input.
  - Verifier rejects the comparison twice on the same finding → drop the
    finding to the master body under "unresolved" with both readings recorded.
    Do not let two agents argue; give the operator the disagreement.
  - Operator disposition window: 24h from phase-4 completion, per the retro v2
    default. On timeout, undispositioned findings become DEFERRED with the
    timeout date recorded. Nothing is auto-accepted. Silence is never consent.
```

The escalation rule that matters most is the last one. An article review that auto-accepts on operator silence would let a stranger's blog post edit Alice. The gate is the whole point.

---

## Part 3: The master ticket body

Each link opens a master on the `alice-framework` board, assigned to the
**orchestrator lane** (`jarvis` or `planner`), **never** to a phase worker
(`web-research` / `doc-writer` / `council`). Orchestrator ≠ phase worker
(F-2, F-8). Phase work runs only on children. Body skeleton:

**Ship-lane dispatch (v0.5.0, t_cd29b136).** When the apply set includes an Alice-doc amendment (per Part 7.5.1 trigger), the master ticket body MUST carry `path: alice-publish` + `version: v<X.Y.Z>` in its routing fields, and the ship lane runs as a separate child ticket dispatched via the registered `alice-publish` loop (`~/.hermes/loops/hermes.yaml`) — NOT as a side-effect of the master orchestrator lane. Dispatch chain:

1. The orchestrator (`jarvis` lane) detects an ACCEPTED finding whose action-item cites an `methodology/*.md` / `templates/*.md.template` / `references/*.md` path; the orchestrator records this in the master ticket body under `## Alice amendment path` with the path + the disposition.
2. The orchestrator files a ship-lane child ticket with `assignee: jarvis` + `parents: [master_ticket]` + `path: alice-publish` + `version: v<X.Y.Z>` in the body, then `kanban_block`s the child until Part 7.5.1's pre-ship sequence (collect changed files → topic extractor → commit-message drafter → PR diff packager → operator HITL approve gate → push) is ready to run.
3. The `alice-publish` loop fires with `run_with_profile: coder` for the technical prep (Steps 1-5: pre-verify, work-complete check, verifier-ran check, CHANGELOG consolidation, per-file version reconciliation per `M-decide-alice-publish-flow.md` STEP 1 + STEPs 2-6) + an HITL gate where `assignee: jarvis` runs the operator-approve ticket (per op-guard-3 `[HUMAN ACTION]` prefix).
4. After the operator approves the commit-message + release-notes, the coder runs `git push` + (when applicable) `git tag v<X.Y.Z> && git push --tags` + the `gh release create v<X.Y.Z>` from `M-decide-alice-publish-flow.md` STEP 7-10.

The ship lane is never run by the doc-writer or verifier profile directly; it is always an alice-publish-loop child with the assignee = jarvis for HITL + run_with_profile = coder for technical prep. The loop yaml amendment (the `alice-publish` entry in `~/.hermes/loops/hermes.yaml`) is paired with v0.5.0 as a loop-updater instance child per op-guard-17. Body skeleton:

```markdown
## Source
- URL: <x.com link>
- Article: <author, title, date>
- Posted by operator: <YYYY-MM-DD>
- path: x-article-review
- Operator's stated reason: <verbatim, or "none given">

## X body access preflight
- [ ] xurl auth ready  OR
- [ ] operator pastes body  OR
- [ ] computer_use Chrome path approved

## Original request
<the operator's message, verbatim>

## Planned flow
1. Article retrieved and quoted (phase 1) → V-1 hard gate
2. Related prior art surveyed (phase 2) → V-2
3. Comparison to Alice, classified (phase 3) → V-3
4. Council synthesis + operator disposition (phase 4)
5. Flow retro (spawned at Phase 0, blocked needs_input); after Retro-A publishes, generate the human-readable digest and open Retro-H with its pointer

## Cross-board routing
- Review children → alice-framework board
- Accepted implementation children → the board owning the touched artifact

## Release-message discipline (v0.4.0, t_a92c1f88)

The operator-facing Discord message that announces an alice-framework release MUST describe the **topic(s) changed**, NOT the article author. The article is the inspiration; the methodology amendment is the artifact. Attributing the release to the article author misattributes the operator's + council's + Alice team's work to a single data point in the methodology's research.

**Canonical release-message format:**

```
Alice v<X.Y.Z>: <topic-1>, <topic-2>, ...
```

- One to N comma-separated topic slugs (the high-level change themes).
- No article author, no article URL, no source handle.
- No `[v<X.Y.Z>]` brackets in the message body (those live in CHANGELOG.md and the GitHub release notes, which are separate surfaces with their own attribution rules).

**Where the article reference lives:**

- **Master ticket body** under `## Source` block — full URL + author byline + post ID. This is the audit trail.
- **Vault audit-line** under `~/Documents/HermesVault/log.md` — the audit-line may cite the article URL as the *input* that motivated the methodology change, but the change itself is described by topic.
- **Methodology amendment rationale** — the article may be cited as one source among several under `## Sources` or in the per-finding evidence-anchor section.
- **CHANGELOG.md `## Source` sub-section** — the article may be cited as one source among several for the release. The release entry itself is described by topic, not article author.
- **GitHub release notes** — the article may be cited under `## Source` of the GitHub release notes (which mirrors the CHANGELOG entry); the GitHub release *title* is `Alice v<X.Y.Z>` and the *notes* are topic-driven, not article-driven.

**Where the article reference does NOT live:**

- The operator-facing Discord message (the release announcement) MUST NOT name the article author or URL.
- The audit-line format MUST describe the change by topic, not by source attribution.

**Why this rule exists:**

1. **Attribution hygiene.** The article is one data point in the methodology's research. The change is the operator's + council's + Alice team's work product. Crediting the article author in the release message misattributes the work and creates a misleading record of where the change originated.
2. **Methodology-article separation.** The change is to Alice's methodology, not a reproduction of the article's claims. Alice has its own rationale, its own council verdict, and its own acceptance criteria. The release message reflects that the change is Alice's, not the article's.
3. **Operator privacy.** The operator may not want the article author's handle surfaced in their Discord feed when Alice releases ship. The article is a research input; the operator's chat is the operator's surface.
4. **Source incident.** Retro-H body observation (2026-08-09, operator verbatim): *"The message on the push should mention what topic was changed but not the article author, the article was just the inspiration for the change."*

**Cross-reference.** The alice-publish flow (`methodology/M-decide-alice-publish-flow.md` v0.2.0+ Part 2 Field 3 Outputs + `~/.hermes/loops/intents/alice-publish-intent.md` v0.3.0+) carries the canonical instance side of this rule. The post-push Discord delivery is owned by the alice-publish loop's STEP 10 (release + Discord notification) + the `~/.hermes/tools/post_alice_release_discord.py` script (sibling amendment). The release-message-format rule is owned at the methodology level so: (a) any future publish flow inherits the same discipline, (b) the master-ticket methodology can reference one canonical source for release-message format, (c) the operator-facing `methodology/03b-decide-operator-agent-interaction.md` Part 13 has a clear downstream apply.

### Git commit + GitHub release-notes discipline (v0.5.0, t_cd29b136)

v0.4.0 codified the operator-facing Discord release message. v0.5.0 extends the topic-only contract to the **git commit message** and the **GitHub release-notes headline + body** — the two surfaces that previously could leak source/author/URL tokens even though the Discord message was topic-only. The extension is necessary because the conditional ship lane (Part 7.5.1) puts the alice-framework repo on the GitHub remote; the commit message + release-notes are the surfaces operators + auditors see first when scanning git history + the GitHub releases tab.

**Rule.** The git commit message AND the GitHub release-notes title + headline MUST follow the same topic-only contract as the operator-facing Discord message:

- **`git commit -m` headline line:** `Alice v<X.Y.Z>: <topic-1>, <topic-2>, ...` — same shape, no article author, no article URL, no source handle, no `[v<X.Y.Z>]` brackets. The headline line is operator-readable in `git log --oneline`.
- **Commit body (when multi-line):** stats + filing-ticket list (e.g., `Files changed: N. Tickets: t_xxx, t_yyy. Ship via alice-publish loop.`). The article reference MAY appear in the body as one source among several in a `## Source` sub-section — never in the headline.
- **GitHub release notes title:** literal `Alice v<X.Y.Z>` — no article author, no article URL, no bracket suffix. The release-title rule is unchanged from `M-decide-alice-publish-flow.md` v0.3.0 Output 6.
- **GitHub release notes body:** the canonical 5 sections per `M-decide-alice-publish-flow.md` STEP 10 (## What's new + ## Stats + ## Source + ## License + ## Author) — the article reference MAY appear in `## Source` and `## What's new` (as one source among several when the article is a research input) but NEVER in the release-title or in any headline-tier sentence.

**Forbid-list (mandatory at pre-push).** The pre-push linter refuses any commit-message headline OR GitHub release-notes headline that contains any of:

| Token class | Examples | Why forbidden |
|---|---|---|
| Bare URL | `x.com`, `twitter.com`, `https://`, `http://`, `www.` | URLs leak source platform into the audit surface |
| `@handle` | `@<anything>` (any `@<chars>` ≥ 2 chars) | Handles attribute the change to a person, not to Alice |
| Article-author byline | `<author display name>` from the source article's `## Source` block | The article author is one data point; the change is Alice's |
| Source-article title | the article's verbatim title from `## Source` | The article title names the source, not the topic |
| Bracketed version forms | `[v<X.Y.Z>]`, `[Alice v<X.Y.Z>]` | Brackets belong in CHANGELOG + GitHub release body, not in commit headlines or release titles |
| Trailing punctuation | any of `.`, `;`, `!`, `?` at end of headline | Disrupts the `git log --oneline` visual rhythm; the topic list ends with the last slug, no terminal punctuation |

**Per-line topic count.** Maximum 5 topics per headline line; topics separated by `, ` (comma + space); no parenthetical asides that could leak source/author into a sub-clause. A 6-topic release splits into two lines via the standard `Alice v<X.Y.Z>: <topic-1>, ..., <topic-5>,\n  <topic-6>, ..., <topic-N>` continuation.

**Linter is a tool, not human judgment (v0.5.0 sharpening).** The forbid-list is enforced by `~/.hermes/tools/topic_only_lint.py` (paired instance child per op-guard-17) at the pre-push step — same point the existing `verify_artifact_state.py` runs (per op-guard-19 + `M-decide-alice-publish-flow.md` STEP 1 + STEP 4-6). The linter exits 0 on a clean headline, exits 1 + writes the matching token to stderr on a violation. A commit that fails the linter does NOT push; the publish worker blocks with reason `topic-only lint failure: <token> at <line>:<col>`. Human judgment does not override the linter; if the operator wants to override (rare exception), the override is recorded as a `kanban_comment` on the master ticket citing the operator's chat quote.

**Where the article reference lives (v0.5.0 sharpening).** The v0.4.0 surface-attribution table is preserved and the git commit + GitHub release-notes rows are added:

|| Surface | Article reference lives here? |
|---|---|---|
| `git commit -m` headline | **NO** — topic-only. |
| `git commit -m` body (multi-line commits) | Optional — may cite article in `## Source` sub-section as one source among several. |
| GitHub release notes title | **NO** — `Alice v<X.Y.Z>` (unchanged from v0.4.0). |
| GitHub release notes body `## What's new` + `## Source` | YES — may cite article as one source among several. |
| Operator-facing Discord release message (v0.4.0) | **NO** — topic-only (unchanged). |
| Master ticket body `## Source` block | YES — full URL + author byline + post ID (audit trail, unchanged). |
| `~/Documents/HermesVault/log.md` audit line | YES — the `push-done: <sha> at <ISO> — <url> — <topic-list>` audit-line may cite the article URL in its `## Source` sub-section (new in v0.5.0). |
| Methodology amendment rationale | YES — may cite article as one source among several (unchanged). |
| `CHANGELOG.md` `## Source` sub-section | YES — may cite article as one source among several (unchanged). |

**Why the rule extends to git commits + GitHub release notes.** v0.4.0 was a Discord-message discipline — the operator-facing surface. v0.5.0 is a repo-side discipline: the alice-framework repo is the project's permanent ledger (`git log` is the operator's + future contributors' first read on what changed in a release), and the GitHub releases tab is the operator's + external readers' first read on the public face of the release. A topic-only Discord message paired with a named-author commit headline creates a seam: the Discord message says "we shipped `Alice v0.5.0: topic-X, topic-Y`" while `git log --oneline` says "<article author>'s piece on topic-X, plus topic-Y." The seam is the failure mode — the audit surfaces disagree. v0.5.0 closes the seam.

## Done gate
done-gate: operator-LGTM-done
Every finding carries a disposition and every ACCEPTED finding has a child
ticket. After phase 4 the master is blocked(kind=needs_input) — it does NOT
auto-complete. The operator's decision closes the master.

## Findings
### Phase 1: Article read summary
(pending)
### Phase 2: Further research findings
(pending)
### Phase 3: Alice comparison
(pending — F-1..F-N classification table lives here)
### Phase 4: Council synthesis + recommendation
(pending)

## Operator decisions
(Operator records ACCEPT / REJECT / DEFER per finding here.)

## Stop condition
All findings dispositioned; ACCEPTED findings have implementation children;
flow retro dispositioned.
```

**The done-gate is operator-LGTM, not auto-done.** Per `methodology/04c` Part 6, auto-done is the default — but this flow meets two of the explicit exceptions: the outcome is subjective (is this idea worth adopting?) and the operator asked to retain final acceptance. Write the gate in the body; never leave it implicit. After P4 the orchestrator calls `kanban_block(kind=needs_input)` on the master.

**The master body is the findings doc.** Do not write a separate report file. A findings file and a master ticket drift the moment the operator dispositions one finding in one place. One surface, one truth. Phase workers update the master body sections (not only comments).

**Preflight (F-4):** the three-option X body-access checklist is mandatory in the master body before Phase 1 starts. If none of the three is true, Phase 1 files a `[HUMAN ACTION]` child or capability-blocks — it does not proceed on title+preview alone.

**Title discipline (v0.2.3, t_84782c7c):** The master title is the operator's first read across a list of 50+ peers. The title MUST identify the topic in plain language; the URL, post ID, and other source identifiers live in the body under `## Source`, NOT in the title. Source: Retro-H body observation (2026-08-09) — *"when we rename the master ticket, the name should reflect the topic + author."* The rename rule below makes the discipline executable; the rendering rule below makes the discipline verifiable at filing time.

**Operator escape hatch (rename discipline, v0.2.3, t_84782c7c).** A ticket comment with a `## Rename` block is a **rename request that MUST be applied**, not a soft suggestion. The orchestrator (jarvis or planner lane) MUST execute `hermes kanban edit --title <new-title>` on the next orchestration tick and append a `## Rename applied` confirmation comment with the **old title**, the **new title**, and the **operator-quoted rationale**. The rename comment is a request, not an application.

### Title-rendering discipline (v0.2.3, t_84782c7c)

The orchestrator MUST apply the title discipline at three points in the master's life:

1. **At filing time (Phase 0).** The master title is built from three descriptive fields — `<author-slug>`, `<topic-slug>`, `<one-line description>` — and the assembly MUST fit an 80-codepoint budget. The title-recovery ladder runs BEFORE the first `kanban create` invocation: method 1 (xurl) → method 2 (web_search) → method 3 (browser_snapshot) → method 4 (post-id fallback, `tweet-<last-8>`). The chosen method is recorded in the body under `## Title source`. If the assembled title exceeds 80 codepoints, the orchestrator retries with a further-truncated `<topic-slug>` (1–2 words); if the retry still exceeds 80 codepoints, the filing REFUSES with a clear error (no silent URL-shaped fallback). The exact codepoint arithmetic is flow-agnostic in `methodology/04c-decide-master-ticket.md` Part 8 §Master Title Discipline; the per-flow filename convention lives there.

2. **During the flow (post-Phase 1 title recovery).** When the article body becomes available partway through Phase 1 (e.g. after the Phase 1 worker resolves the xurl auth path) and the auto-filed title was built from the post-id fallback, the orchestrator MUST apply a `## Rename` comment + `hermes kanban edit --title` invocation on the next orchestration tick, using the recovered article title as the new topic-slug. The 80-char budget is re-checked; the rename is truncated if needed. A `## Rename applied` confirmation comment is appended.

3. **On operator rename (any time).** When the operator posts a `## Rename` comment with a new title, the orchestrator MUST apply the rename via `hermes kanban edit --title` on the next orchestration tick. The application is NOT a follow-up; the rename comment is the trigger, the edit is the contract. The 80-char budget applies; an over-budget operator rename is truncated with the truncation rule recorded in the confirmation comment.

**Failure surface.** If `hermes kanban edit --title` fails (CLI rejected, dispatcher timeout, no-op returned), the orchestrator MUST:

- Append a `## Rename failed` comment with the CLI error, the requested title, and the previous title.
- Surface the failure to the operator via the operator-DM cron (per the `REAL_ASK_BUCKETS` gate in op-guard-13) only when the rename is a real operator ask (operator-authored `## Rename` comment); auto-recovered renames (Phase 1 post-availability rename) are logged to the master body's `## Title source` block instead.
- Never post a second `## Rename` comment as a substitute for the failed edit. Comments are not applications.

**Cross-reference.** The flow-agnostic title-rendering discipline (budget rule, the three-method ladder, the 80-char enforcement) lives in `methodology/04c-decide-master-ticket.md` Part 8 §Master Title Discipline (v0.1.3 amendment in the same change set). This sub-section narrows the rule to the x-article-review flow: the per-instance filename convention (`[MASTER] x-article-review / <author-slug> <topic-slug> — <description>`), the Phase 1 post-availability rename trigger, and the failure-surface contract.

---

## Part 4: Phase 1 — read the article

**Assignee:** `web-research`. **Deliverable:** the article's substance, quoted, plus a 1-2 sentence topic statement.

### x.com retrieval is the flow's most likely failure point

This is not a theoretical concern. On 2026-07-16 an attempt to read an x.com article failed through every ordinary path before succeeding — `curl` returned the JavaScript shell, nitter hit Cloudflare, and `xurl` lacked auth. What worked was driving a real logged-in Chrome via the `computer_use` toolset.

Attempt retrieval in this order, stopping at the first success:

1. **`web_extract` on the URL.** Cheapest. Often returns the JS shell for x.com; if the output has no article prose, treat it as a failure, not as the article.
2. **`web_search` for the article's title or a distinctive phrase.** Syndicated copies and quote-threads frequently carry the full text.
3. **Browser tools** (`browser_navigate` + `browser_snapshot`).
4. **`computer_use` against a logged-in Chrome.** The known-good path for gated x.com content. `cua-driver` is installed at `~/.local/bin/cua-driver`.

If all four fail, block with `kind=capability` and name each method and its failure. Do not summarize from the preview card.

### The hard rule: a title is not an article

The single most dangerous failure in this flow is a plausible review of an article nobody read. x.com previews give you an author, a title, and one sentence — enough to hallucinate three paragraphs of confident analysis that never touches what the article argues.

**V-1 exists to make that impossible.** The phase-1 child must contain a verbatim quote of at least one full paragraph from the article body. The verifier checks that the quote exists and reads like body prose, not like a headline. No quote, no passage to phase 2. V-1 FAIL blocks promotion of P2–P4 (F-1).

**Attribution rule (F-7):** Do not attribute named essays without a resolved primary URL + author byline from that URL. V-1/V-2 flag unverifiable attributions. (Article-1 failure mode: Phase 1 attributed "Agent Harness Engineering" to Lilian Weng; Phase 2 corrected — Addy Osmani Apr 19 2026.)

**Output lands in the master body** under `### Phase 1: Article read summary` (comments may mirror; body is canonical — F-5).

---

## Part 5: Phase 2 — research the topic

**Assignee:** `web-research`. **Deliverable:** prior art and surrounding context for the article's central claim.

The question this phase answers is **not** "what else is on the internet about this?" It is: **is this claim established practice, one person's opinion, or actively disputed?** That distinction changes what the operator should do with it.

| What the survey finds | What it means for Alice |
|---|---|
| Multiple independent sources, converging | Established pattern. A real gap if Alice lacks it. |
| One source, no corroboration | One person's practice. Interesting, not yet evidence. |
| Sources actively disagreeing | Contested. Alice should not adopt a side by accident. |
| Sources contradicting the article | The article may be wrong. Say so plainly. |

Record 3-6 sources with URLs and a one-line note on what each contributes. Six well-chosen sources beat twenty; this is a survey, not a literature review.

**The article is allowed to be wrong.** Nothing in this flow requires the article to be a valid source. "The article's central claim is contradicted by three sources" is a first-class, useful outcome — report it and let the operator decide.

V-2 is a light gate: it confirms the sources exist, resolve, and actually bear on the article's claim.

---

## Part 6: Phase 3 — compare to Alice

**Assignee:** `doc-writer`. **Deliverable:** the classified findings. This is the phase the flow exists for.

### Read before claiming

Every finding must name the Alice file it bears on, by path, and the writer must have read that file. `search_files` across `methodology/`, `references/`, and `templates/` before asserting that anything is missing.

"Alice doesn't cover this" is the easiest sentence in this flow to write and the easiest to get wrong. Alice has 24 methodology docs, 9 references, and 13 templates; the thing you think is absent is often present under different vocabulary. Search the concept, not the article's phrasing.

### The three classifications

Exactly one per finding.

**covered** — Alice already addresses this. Cite the file and section. A covered finding is still worth recording: it tells the operator their framework is holding up against outside practice. If Alice covers it *weakly*, that is not covered — it is a gap in depth. Say which.

**gap** — Alice does not address this, and the article's evidence suggests it should. Cite the file where it *would* belong, or state that it needs a new doc. A gap must name a destination; a gap with no destination is an observation, not a finding.

**contradiction** — Alice takes a position the article opposes, where both cannot be right. This is the rarest and most valuable class. Quote both sides. **Do not resolve it** — flag it and let phase 4 and the operator do that. A doc-writer who resolves a contradiction unilaterally has made a framework decision the operator did not delegate.

### Finding shape

Per the retro v2 structured-findings pattern, indexed `F-N`:

```markdown
### F-1: <one-line summary>
- **Classification:** covered | gap | contradiction
- **Article says:** <quote or tight paraphrase>
- **Alice says:** <quote + file path, or "no coverage found; searched: <terms>">
- **Evidence:** <the phase-2 sources bearing on this>
- **Proposed action:** <the specific change, or "none — informational">
- **Disposition:** PROPOSED
- **Notes:** <operator fills on disposition>
```

### V-3 is the flow's mandatory gate

**Assignee:** `verifier`. This is the one verifier step that cannot be elided.

The verifier independently opens every Alice file cited and confirms:

1. Each cited file exists at the stated path.
2. Each quote from Alice is accurate and not truncated into a different meaning.
3. Each "no coverage found" claim survives an independent search using different terms than the writer used.
4. Each finding carries exactly one classification.

Point 3 is the expensive one and the reason the gate exists. A false "gap" is the costliest error this flow can make: it invents work, and the work lands in the framework as a duplicate of something already there under another name.

The verifier does **not** judge whether a finding is a *good idea* — that is the council's and the operator's job. The verifier judges whether it is *true*.

---

## Part 7: Phase 4 — council synthesis and operator disposition

**Assignee:** `council`. File it as a board task with `assignee=council`. **Never use `delegate_task` for council work** — per the canonical council pattern, that produces a duplicate of the board task.

### Select seats before deliberation

Use `methodology/M-decide-council-sizing.md` and the Hermes reference `references/seat-selection.md`. The Phase 4 ticket body MUST contain `## Council Seats Selected` before the council runs. Assess the three signals:

- **Decision reversibility:** this review can change a framework contract and public methodology, so the cost of being wrong is high.
- **Stakeholder breadth:** Matthew/operator and Alice readers are both affected; accepted findings may also create maintenance work.
- **Question dimensions:** strategy, engineering feasibility, user-facing methodology, operations, and failure modes are all material.

Start with Strategist + Engineer + Skeptic; add Product for the user-facing framework, Operator for Matthew's daily maintenance burden, and Customer only if a commercial/buyer question is actually in scope. Use **3–5 seats**, with **Operator required** for this framework decision and **Product required** because the output affects user-facing methodology. Each selected seat gets a one-sentence justification in the ticket body. Do not use a hardcoded five-seat list when the question's signals do not require it, and do not silently reduce the council to the minimum.

The council output must contain a structured position for every selected seat. The Skeptic seat argues against adoption. The council does not re-verify facts; V-3 settled those. It answers the judgment question: **given that these findings are true, which ones should change Alice?**

The Skeptic seat carries a standing brief here: **argue against adoption.** The failure mode this flow is most exposed to is adopting a stranger's framework wholesale because it was articulate. Alice's coherence is worth more than any single article's ideas. A framework that absorbs every good idea it encounters stops being a framework.

The council's output is a recommendation per finding — adopt, decline, or needs-operator-judgment — appended to the master body. The verdict Markdown and PDF preserve the selected-seat analysis block and its composition; they do not assume a fixed five-seat output. Recommendations, not decisions.

**Per-finding verdict structure (v0.3.0).** Every finding the council addresses in Phase 4 MUST carry, in the verdict Markdown and the rendered PDF:

- **(a) the article's claim** — verbatim quote + the article URL.
- **(b) the Phase 2 web_search sources considered** — URLs + a one-line note on what each source contributes to the finding (does it support, contradict, or qualify the article's claim). The Phase 2 sources are the council's actual evidence base, not the article itself.
- **(c) the selected seats' positions on the finding** — the verdict block already records this; the per-finding structure ensures the seats' positions are traceable to (b).
- **(d) the final recommendation** — adopt / decline / needs-operator-judgment, with a confidence (low / medium / high) and a one-line rationale.

The `## Evidence anchors` section of the executive-reporting digest (canonical §5 per `~/.hermes/skills/productivity/executive-reporting/SKILL.md` v0.2.0) MUST list the Phase 2 web_search URLs by finding, so the operator can audit the council's source set in 10 seconds without re-reading the technical council log. The Phase 2 URLs go in the **bottom tier** (collapsible `<details>` block per the executive-reporting skill §Evidence anchors discipline) — NOT in the headline tier, the action-items tier, or the operator-decision tier. Technical detail belongs at the bottom, collapsed, expandable; the headline and action items stay plain-language.

### Source-attribution discipline (v0.3.0, mandatory)

The operator's review of an article-review verdict must be **auditable to the sources the council actually considered**. A verdict that doesn't surface its sources is unfalsifiable — the operator cannot tell whether the council reviewed the article alone (a thin review) or the article plus the surrounding ecosystem (a deep review). The source-attribution discipline makes the council's review auditable.

**Rule.** Every finding's evidence anchor MUST point to the Phase 2 web_search URLs the council actually weighed, not only to the article URL. The Phase 1 article quote establishes what the article says; the Phase 2 sources establish whether the article's claim is established practice, one author's opinion, or actively disputed. The council's verdict on each finding rests on (a) + (b), not on (a) alone.

**What the verdict Markdown MUST contain, per finding:**

```markdown
### Finding F-N — <one-line summary>
- **Article claim:** <verbatim quote> (<article URL>)
- **Phase 2 sources considered:**
  - <URL 1> — <one-line: contributes X, supports/contradicts/qualifies>
  - <URL 2> — <one-line: contributes Y, supports/contradicts/qualifies>
  - <URL 3> — <one-line: contributes Z, supports/contradicts/qualifies>
- **Seats' positions:**
  - Strategist: <position + 1-line reason>
  - Engineer: <position + 1-line reason>
  - Skeptic: <position + 1-line reason>
  - <additional seats as applicable>
- **Final recommendation:** adopt | decline | needs-operator-judgment (confidence: low | medium | high)
- **Rationale:** <one-line why this recommendation over the alternatives>
```

**What the rendered PDF MUST surface in the bottom tier (Evidence anchors section, collapsible):**

```markdown
<details>
<summary>Phase 2 sources by finding</summary>

- F-1 — <URL 1>, <URL 2>, <URL 3>
- F-2 — <URL 4>, <URL 5>
- F-3 — <URL 6>
- ...

</details>
```

**Anti-patterns (rejected):**

- **Article-URL only.** Citing only the article URL leaves the council's review unfalsifiable. The Phase 2 survey is the council's evidence base; the operator must see it.
- **Headline-tier Phase 2 URLs.** Putting URLs in the headline or action-items tier violates the executive-reporting headline-hygiene rule (no jargon / URLs / file paths in the headline). The Phase 2 URLs live in the Evidence anchors section (bottom tier, collapsible), not in the headline or action items.
- **Generic source list.** A blanket "see the Phase 2 survey" pointer does not surface the per-finding mapping. The operator needs the per-finding URL list, not a generic pointer.
- **Phase 2 = article sources.** Phase 2 is `web_search` against prior art and contested-claim sources — NOT the article itself. The article is Phase 1; the council's evidence base is Phase 2.

**Verifier check (V-4, v0.3.0).** The Phase 4 verifier confirms:

1. Every finding has a populated `(b) Phase 2 sources considered` list with URLs.
2. The URLs in (b) resolve and were actually produced by Phase 2 (not invented).
3. The bottom-tier `Phase 2 sources by finding` block is present and complete.
4. The verdict does NOT carry the Phase 2 URLs in the headline, action items, or operator-decision tiers (headline-hygiene check).

A verdict missing the per-finding source attribution is REFUSED (per the same pattern as Retro-A's `EligibilityError` gate). The Phase 4 council ticket gets a `Verdict generation REFUSED — missing per-finding source attribution` pointer comment naming the missing pieces; no PDF is rendered; no master comment is posted; no Discord attachment is attempted; the orchestrator retries the Phase 4 council child with the per-finding discipline instruction appended.

**Implementation (paired with the intent doc + script).** The source-attribution discipline lives in three places:

- **This methodology doc** (the contract; what the verdict must contain).
- **`~/.hermes/loops/intents/x-article-review-intent.md` v0.6.0+** (the intent worker_prompt Phase 4 + verify gates; what the council child is told to produce).
- **`~/.hermes/tools/post_council_verdict_pdf.py`** (the script that renders the verdict Markdown to PDF and populates the bottom-tier Evidence anchors section; refuses to ship a PDF that lacks the per-finding source attribution).

Per op-guard-5 paired-wiki integrity, all three ship in the same change set. Per op-guard-17 alice-first/instance-second/compliance-gate, this methodology doc is the alice-first; the intent + script amendments are the instance children; the compliance-verifier is the META done-gate child.

**Why this matters.** The operator's 2026-08-09 Retro-H observation #2: *"When presenting the council output, we need to include what information from the web-search results were considered during the council review. We need to make sure the council is looking at the article AND the web_search findings."* The wandermist article-3 council verdict at `~/Documents/HermesVault/2-ATOMIC/decisions/article-2082757212770898333-council-verdict.pdf` references its Phase 2 sources (Anthropic Building Effective Agents, LangGraph Workflows and Agents, Google ADK Parallel Workflow, AutoGen Reflection, etc.) in passing — in the Executive Summary's "Prior-art alignment" callout and in the Evidence anchors section. The operator's concern is that the source set be surfaced *explicitly, per finding*, not buried in a generic section. v0.3.0 codifies the explicit-per-finding discipline.

### The 5-step operator-review PDF delivery (mandatory for Phase 4 council verdicts)

The Phase 4 council verdict is an **operator-review artifact** per `methodology/03b-decide-operator-agent-interaction.md` Part 13. The Markdown verdict is the canonical source of truth; the PDF is the operator-facing reading surface. The verdict MUST follow the 5-step delivery protocol:

```
Markdown (canonical)  →  PDF (rendered)  →  Discord attachment  →  ticket comment  →  vault copy
```

1. **Markdown (canonical).** Write the verdict at `<workspaces>/<phase-4-id>/council-verdict.md` (or `_inbox/<article-slug>-council-verdict.md` for framework retros). The Markdown is the source of truth and the audit record. The master ticket body's Phase 4 section holds the same content as the selected-seat analysis block.
2. **PDF (rendered).** Render the Markdown to PDF at `<workspaces>/<phase-4-id>/council-verdict.pdf` using `~/.hermes/tools/md_to_pdf.py`. The PDF is the operator-facing reading surface (macOS Preview, iOS Preview, Discord inline viewer, browser).
3. **Discord attachment.** Send the PDF as a Discord attachment to the operator's connected chat via `~/.hermes/tools/send_dm_attachments`, subject `Council verdict: <article-slug>`. Delivery is **best-effort**: a failed send does NOT invalidate the verdict or the PDF. The failure is recorded in the audit-line and retryable on the next run.
4. **Ticket comment.** Post a one-line summary to the master ticket beginning exactly with the literal `Council verdict ready` prefix (parallel to the v0.2.1 `Digest ready` pattern). The comment names the PDF path, the verdict title, and the master ticket id. The technical master-ticket body is unchanged.
5. **Vault copy.** Store a copy at `~/Documents/HermesVault/2-ATOMIC/decisions/<article-slug>-council-verdict.pdf` for the durable audit trail. Generated alongside the PDF in step 2 (single render, two targets).

**Headline hygiene.** The 8-section PDF shape (per `templates/human-digest.md.template`) is mandatory: what this is, headline (3–5 plain-language bullets, no jargon), selected-seat findings, recommendations, evidence anchors, operator observations, decision table, metadata. The Council verdict is the canonical operator-review artifact; the rule applies to Phase 4 just as it applies to Retro-A digests.

**Implementation.** The pipeline is implemented by `~/.hermes/tools/post_council_verdict_pdf.py` (paired with `post_graph_retro_human_digest.py` for Retro-A). The script discovers Phase 4 council-synthesis children with `status='done'` on the alice-framework board and runs the 5-step protocol. Idempotent on (`article-slug`, `master-id`): a verdict that has already produced a PDF at the canonical path is skipped on re-run. The script is invoked automatically after the council child transitions to `done` (paired with the existing Retro-A digest pipeline).

**Why the methodology must own this rule, not just the loop intent doc.** The x-article-review loop's intent doc (`~/.hermes/loops/intents/x-article-review-intent.md` v0.2.2) carries the loop-engineering wiring. The methodology owns the protocol so: (a) any future loop that uses a Phase 4 council synthesis inherits the same rule, (b) the master-ticket methodology (`methodology/04c-decide-master-ticket.md`) can reference one canonical source for council-verdict PDF delivery, (c) the operator-facing `methodology/03b-decide-operator-agent-interaction.md` Part 13 has a clear downstream apply. The intent doc is the implementation; the methodology is the contract.

**Discipline (parallel to v0.2.1 Retro-A digest).**

- Both formats ship: Markdown is canonical; PDF is the operator's reading surface.
- Render before delivery: the PDF is generated from the Markdown immediately before the Discord attachment step. A stale PDF is a drift risk.
- Decision table is mandatory: every council-verdict PDF ends with the Accept / Reject / Defer decision table. Silence is not consent.
- Discord is best-effort: if no chat channel is connected, the verdict still lives at its canonical paths; the delivery step is recorded as not applicable. A failed delivery must not invalidate the artifact.
- The technical record remains canonical: when the PDF and the master ticket body disagree, the master ticket body wins and the PDF is regenerated before delivery. The PDF is a translation layer, not a second source of truth.

### The operator gate

The operator dispositions each finding within 24h, per `methodology/06a-decide-retro-v2.md`:

| Disposition | Operator writes | Downstream |
|---|---|---|
| **ACCEPT** | `ACCEPTED` + a `Notes:` line naming the action | Child ticket via `kanban_create(parents=[master_id], assignee=<profile>)`, body citing `Finding F-N` |
| **REJECT** | `REJECTED` + a `Notes:` line with the reason | No child. Reason recorded in the master body. |
| **DEFER** | `DEFERRED — <date or condition>` | No child now. Logged with the deferral target. |

On window timeout, undispositioned findings become `DEFERRED — disposition window timeout <ISO>`. Nothing auto-accepts.

**Accepted findings are specs, not implementations.** The implementation child is a normal spec-first ticket and re-enters the standard flow — doc first, verifier-gated, parity-checked. Acceptance means "this should change Alice," not "this change is now correct." Nothing from this flow lands in a public Alice doc without passing the same gates as any other change.

---

## Part 7.5: META done-gate (extended in v0.5.0)

The META ticket (master) done-gate is the precondition set the dispatcher checks before transitioning the master from `running` to `done`. v0.2.0 established the baseline gate; v0.5.0 extends it when an Alice-doc amendment is in scope.

### Baseline gate (v0.2.0, unchanged)

When **no Alice amendment is in scope** (every ACCEPTED finding produced an external-only output — research note, comparison-only doc, audit artefact — and no `methodology/*.md` / `templates/*.md.template` / `references/*.md` path was committed):

```
META done-gate = Retro-A done + Retro-H done + compliance-PASS
```

- **Retro-A done**: the per-flow Retro-A child ticket (filed at Phase 0, blocked needs_input) transitions to `done`. Retro-A carries the structured-findings + the verifier's verification receipt.
- **Retro-H done**: the human-digest Retro-H child transitions to `done` with the canonical 8-section PDF (per `methodology/M-decide-human-digest.md` Part 3) shipped via the 5-step delivery protocol (per `methodology/03b-decide-operator-agent-interaction.md` Part 13).
- **compliance-PASS**: the compliance-verifier child (per op-guard-17) confirms the applied instance conforms to the Alice methodology doc. For x-article-review, the compliance-verifier confirms (a) the per-finding disposition lines are present, (b) the per-finding source-attribution discipline (v0.3.0) is observed, (c) the master title-discipline (v0.4.1) is observed, (d) the topic-only release-message discipline (v0.4.0) is observed if a Discord message was posted, and (e) no operator-instance content leaked into any repo-bound surface.

When all three preconditions fire, the master auto-promotes to `done` via the dispatcher; no operator approval is required for the auto-done path (per `methodology/04c-decide-master-ticket.md` Part 6 default).

### Extended gate (v0.5.0, t_cd29b136)

When **at least one Alice amendment is in scope** (at least one ACCEPTED finding produced a `methodology/*.md` / `templates/*.md.template` / `references/*.md` change that has shipped to disk), the gate gains a fourth precondition:

```
META done-gate (extended) = Retro-A done + Retro-H done + compliance-PASS + push-done
```

- **push-done**: the conditional Alice-only GitHub ship lane (Part 7.5.1) has completed its 6-step pre-ship sequence + the operator HITL approve gate + the `git push` of the alice-framework repo. The push-done evidence is captured in three places: (1) a `kanban_comment` on the master ticket (`## push-done — <commit-sha> at <ISO> — <release-url> — <topic-list>`); (2) an audit-line appended to `~/Documents/HermesVault/log.md` per op-guard-11 + op-guard-19; (3) a vault-copy pair at `~/Documents/HermesVault/2-ATOMIC/decisions/article-<id>-release-notes.{md,pdf}` for durable operator-facing audit.

**No auto-done path under the extended gate.** When an Alice amendment is in scope, the master requires operator approval of the push before it can transition to `done`. The auto-done path is opt-in only when push-done is recorded; the compliance-verifier cannot fire push-done (push is a coder + operator action); the master orchestrator surfaces a `[HUMAN ACTION]` child when push-done is missing AND the compliance-PASS event has landed, with options `(1) approve push as drafted (continue)`, `(2) edit commit-message / release-notes before push`, `(3) reject — keep META open, file follow-up child with edit notes`. This is the same HITL pattern as the operator-approve gate inside Part 7.5.1 step 5; the difference is the surface — Part 7.5.1 step 5 is the pre-push operator-approve; this section is the post-compliance pre-META-close operator-approve.

**Skip path (preserved from v0.5.0 design).** When no Alice amendment is in scope, Part 7.5.1 does not fire at all and the baseline gate (Retro-A + Retro-H + compliance-PASS) is the entire gate. This is the common case — most x-article-review masters produce findings whose action is research/comparison-only (no Alice-doc amendment). The conditional ship lane is the *exception* path, not the rule.

**Why the gate extension is a methodology-level rule, not a coder-side enforcement.** The compliance-verifier reads this section + the per-finding disposition state from the master ticket body; it cannot infer the gate from the live `~/.hermes/cron/jobs.json` or from a script's internal state. The methodology is the contract; the verifier enforces the contract.

**Anti-patterns (rejected):**

- **Auto-done on push-done absence.** A master with an Alice amendment that auto-closes before push-done is a violation of v0.5.0; the closed-master state leaks the audit surface (`git log` does not show the release; `gh release list` is silent; log.md is silent). The verifier MUST refuse the close.
- **Manual close without push-done.** A `kanban_complete` call on the master with disposition `manual_close_per_operator` that lacks a push-done `kanban_comment` is a v0.5.0 violation; the closer must surface the missing push-done to the operator-DM cron with assignee=jarvis.
- **Push without compliance-PASS.** A push that fires before compliance-PASS means the master closes before the verifier confirms the applied instance conforms to the methodology. This is a v0.4.0 compliance-verifier failure (the original compliance-verifier was not yet extended by v0.5.0, but the rule was load-bearing). The ship-lane child blocks until compliance-PASS has landed.

---

## Part 7.5.1: Conditional Alice-only GitHub ship

This sub-section codifies the ship lane that fires when an Alice-doc amendment is in scope. The lane is **conditional on the master recording `## Alice amendment path` with at least one applied path**, AND **conditional on the operator approving the ship via the HITL gate**. Without either condition, the lane does not fire and the baseline META done-gate (Part 7.5 baseline) is the entire gate.

### Trigger and skip path

**Trigger.** The master ticket body records one or more entries under `## Alice amendment path` — each entry names the file path (e.g., `methodology/M-decide-x-article-review-flow.md`) + the disposition (the `Finding F-N` finding that motivated the amendment + the operator's `ACCEPTED` disposition line). When at least one such entry exists AND the file on disk at that path carries a higher `version:` field than the master ticket's `## Master version` baseline, the conditional ship lane is armed.

**Skip path.** When no `## Alice amendment path` entry exists (all ACCEPTED findings produced external-only outputs), the lane does not fire and the baseline gate closes the master. The skip path is the common case.

**Out-of-scope detection (armed-but-deferred).** When `## Alice amendment path` entries exist but the applied file's `version:` field has NOT been bumped (e.g., the doc-writer amended the file but did not bump the version), the lane is armed but pre-verify fails; the publish worker blocks with reason `version delta required — bump version: on <path>`; the operator decides whether to (a) ship with the post-amendment-version (push now), (b) revert the unversioned amendment and re-apply with a version bump, or (c) defer the amendment to the next Alice release.

### Pre-ship sequence (6 steps, worker-runnable but always operator-approved)

The pre-ship sequence is the technical prep that runs before the operator HITL gate. Steps 1-4 are coder-runnable; step 5 is the operator HITL approve; step 6 is the push + audit-trail. Each step has a verifier gate before the next step advances.

1. **Collect changed files + version delta.** The coder runs `git diff --stat HEAD~1 HEAD --stat` (or `git diff --stat` for an uncommitted change set) + `git status --short` + reads the current `CITATION.cff` version field + scans `methodology/*.md` + `templates/*.md.template` for `version:` field bumps + checks `CHANGELOG.md` for a single `## [v<X.Y.Z>]` entry per `M-decide-alice-publish-flow.md` STEP 4. The output is captured at the master ticket body's `## Verified state` section per op-guard-19 + `M-decide-alice-publish-flow.md` STEP 1.

2. **Topic extractor (forbid-list filtered).** The coder derives the topic list from the amended doc(s)' frontmatter + headings + version deltas. Topics are 1-3 word slugs (e.g., `conditional-ship`, `topic-only-commit`, `done-gate-extension`, `audit-trail`). The extractor runs the topic list through the forbid-list defined in Part 3 §Git commit + GitHub release-notes discipline: any token that matches the canonical forbid-list regex `(?:https?://|\bx\.com\b|\btwitter\.com\b|\bwww\.|\B@\w{2,}|\barticle[-_]author\b|\barticle[-_]title\b|\[v[0-9]+\.[0-9]+\.[0-9]+\]|[.;!?]$)` is rejected; the topic list is rebuilt from the frontmatter `tags:` + the headings after stripping forbid-list matches. The regex enforces word-boundary semantics for `x.com`, `twitter.com`, `www.`, `@handle`, and author/title tokens so they match at the topic-token level rather than as substrings inside larger slugs (e.g. `x.com-foundations` is rejected as a forbidden token, not as a forbidden-substring inside an unrelated slug). Output: `topics = [<slug-1>, <slug-2>, ...]` (max 5 topics per the per-line topic-count rule; 6+ topic releases split across two lines per the canonical continuation form).

3. **Commit-message drafter.** The coder formats the commit message:
   - Headline: `Alice v<X.Y.Z>: <topic-1>, <topic-2>, ..., <topic-N>` (N ≤ 5; 6+ continues on the next line).
   - Body: stats block + filing-ticket list + ship-via-loop pointer. Example body:
     ```
     Files changed: M (N files).
     Tickets: t_cd29b136, t_<ship-lane-child>.
     Ship via alice-publish loop (run_with_profile: coder + assignee: jarvis HITL).
     
     ## Source
     - methodology: methodology/M-decide-x-article-review-flow.md (v0.4.1 → v0.5.0)
     - paired instance: see Decisions log at 2-ATOMIC/decisions/article-<id>-release-notes.{md,pdf}
     ```
   - Lint pass: the drafter runs the commit-message headline through `topic_only_lint.py` (the pre-push linter from Part 3); a clean pass returns exit 0 + writes the lint output to the ship-lane child ticket body; a violation returns exit 1 + the matching token at `line:col` and blocks Step 4.

4. **PR diff packager.** The coder prepares the diff summary that the operator reviews at the HITL gate:
   - Diffstat (`git diff --stat HEAD~1 HEAD`)
   - Per-file summary (each file path + size delta + version delta if applicable)
   - Release notes draft (the canonical 5 sections per `M-decide-alice-publish-flow.md` STEP 10 — `## What's new` + `## Stats` + `## Source` + `## License` + `## Author`)
   - Tagged-release pointer: when the release introduces a new tag `v<X.Y.Z>`, the packager names the tag in the diffstat; existing tags are listed under `## Tags` in the release notes draft.
   - The packager also produces the release-notes PDF draft at `<workspaces>/<ship-lane-child>/release-notes.pdf` (paired with the canonical 8-section human-digest shape per `methodology/M-decide-human-digest.md` Part 3, NOT the retro specialization).

5. **Operator approve (HITL gate, **[HUMAN ACTION]** prefix).** The coder files a child ticket with title prefix `[HUMAN ACTION] ship approval — Alice v<X.Y.Z>` on the alice-framework board, body contains:
   - The commit-message headline (Step 3 output) verbatim, padded with the forbid-list rationale.
   - The release-notes draft (Step 4 output) verbatim, with section headings.
   - The diffstat (Step 4 output) verbatim.
   - The 3-option structure: approve / edit / reject.
   - Options:
     - **(1) Approve as-drafted** → operator replies `approve` (or clicks the canonical `Approve` option in the Discord buttons); the coder advances to Step 6.
     - **(2) Edit** → operator replies `edit: <note>` (e.g., `edit: drop topic-3 from headline, move to body`); the coder revises Steps 3-4 and re-files the approval ticket; no push yet.
     - **(3) Reject** → operator replies `reject: <note>`; the coder `kanban_block`s the ship-lane child with reason `operator-rejected: <note>` and files a follow-up child ticket (assignee=doc-writer or coder per the operator's edit notes); META stays open until the follow-up ships or the operator closes the master explicitly.
   
   The HITL child title prefix MUST be `[HUMAN ACTION]` per op-guard-3 so the operator-DM cron surfaces it; without the prefix, the operator does not see the approval ask and the ship stalls silently — a v0.5.0 anti-pattern.

6. **Push (worker-runnable, post-approval).** After the operator approves, the coder runs:
   - `git add -A` (or specific files per operator's surgical instruction).
   - `git commit -m '<headline>' -m '<body>'` (per Step 3 output; the forbid-list linter MUST return exit 0 before this runs; if the drafter drift-skips the lint, the coder re-runs `topic_only_lint.py` inline).
   - `git push` (or `git push origin main` if the remote is not the default).
   - When the release introduces a new tag: `git tag v<X.Y.Z> && git push --tags` (the tag MUST be on remote before `gh release create` runs; per `M-decide-alice-publish-flow.md` STEP 9 tag gate).
   - `gh release create v<X.Y.Z> --title 'Alice v<X.Y.Z>' --notes '<release notes draft>'` (per `M-decide-alice-publish-flow.md` STEP 10).
   - Audit-trail write (per the next sub-section).

### Audit-trail contract

The push MUST land three audit artefacts before the master can transition to `done`:

1. **`kanban_comment` on the master ticket.** Title: `## push-done — <commit-sha> at <ISO> — <release-url> — <topic-list>`. The comment is the durable record that the operator inspects on the master ticket itself; the audit-line below is the global record. Format:
   ```
   ## push-done — <commit-sha> at <YYYY-MM-DDTHH:MMZ> — <release-url> — <topic-1>, <topic-2>, ...
   
   - Commit: <commit-sha>
   - Tag: v<X.Y.Z> (created during ship / pre-existing)
   - Release URL: https://github.com/042Trix/Alice/releases/tag/v<X.Y.Z>
   - Topics: <topic-1>, <topic-2>, ...
   - Forbid-list lint: passed (exit 0)
   - CITATION.cff gate: closed / reopened per <ticket-id>
   - alice-publish loop run: run_with_profile=coder + assignee=jarvis HITL
   
   ## Source
   - alice-framework commit: <commit-sha>
   - ship-lane child: <ship-lane-child-ticket-id>
   ```

2. **Audit-line appended to `~/Documents/HermesVault/log.md` per op-guard-11.** The audit-line format:
   ```
   ## [YYYY-MM-DDTHH:MMZ] alice-publish — Alice v<X.Y.Z> — result=<success|failure|refused> — release=<release-url> — commit=<commit-sha> — topics=<topic-1>, <topic-2>, ...
   
   ## Source
   - ship-lane child: <ship-lane-child-ticket-id>
   - master ticket: <master-ticket-id>
   ```
   Appended via `~/.hermes/scripts/vault_log.py::append_audit_line` (NOT direct `open("a")` per op-guard-11; the appender holds the flock so concurrent writes do not truncate).

3. **Vault-copy pair at `~/Documents/HermesVault/2-ATOMIC/decisions/article-<id>-release-notes.{md,pdf}`.** The Markdown source (`article-<id>-release-notes.md`) carries the canonical release-notes draft + the commit-message + the diffstat + the audit-trail pointers. The PDF (`article-<id>-release-notes.pdf`) is the operator-facing reading surface, rendered via `~/.hermes/tools/md_to_pdf.py` using the canonical 8-section human-digest template (NOT the retro specialization — the release-notes PDF is operator-facing release info, not retro findings). The vault copy is durable; the operator can grep the vault directory for any article's release notes by `article-<id>`.

4. **Master ticket body update.** The master ticket body's `## Release notes` section records the commit SHA + release URL + topic list + audit-line reference, with the canonical 5 sections per `M-decide-alice-publish-flow.md` STEP 10.

### Push-PASS / Push-FAIL semantics

**Push-PASS** = the push completed without remote-side rejection AND the tag exists on remote AND `gh release create` returned a release URL AND the audit-trail artefacts all landed. Push-PASS is the precondition for META done (under the extended gate).

**Push-FAIL** = the push was refused, the remote rejected, the network errored, the tag was not created, `gh release create` returned non-zero, OR the operator HITL gate was rejected with options `(3)`. Push-FAIL does NOT auto-close META; the ship-lane child surfaces the failure to the operator-DM with assignee=jarvis; the master stays `running` until the operator's next action (re-push, revert, defer).

The push is **never** automatic on partial state — there is no "ship whatever made it through" mode. A refused push is a refusal; the operator decides what to do.

### Why the ship is operator-approved (HITL), not auto-pushed

The Alice repo is the project's public ledger. Auto-pushing an Alice amendment without operator eyes is the v0.5.0 anti-pattern: the operator may want to revise the commit-message (the topic list may have missed an angle), the release-notes (a `## What's new` may need rewording), or the diffstat (an unrelated file may have slipped into the diff). The HITL gate is the surface where the operator sees the actual artifacts-to-ship before they become permanent. Cost: a `kanban_create` + a Discord DM + a single human turn. Benefit: the operator never publishes a commit-message or release-notes that they haven't personally approved.

### Why the topic-only contract is enforced by a linter, not human judgment

The forbid-list (Part 3 §Git commit + GitHub release-notes discipline) is the rule; the linter (`~/.hermes/tools/topic_only_lint.py`) is the enforcement. The HITL gate's value is the operator's review of content (topic list relevance, release-notes accuracy, diffstat scope), not their review of the forbid-list (the forbid-list is mechanical). Hand-checking the forbid-list at the HITL gate would (a) make the forbid-list sub-enforceable on operator attention (brittle), (b) duplicate the linter's work, (c) make the forbid-list drift a v0.5.0 violation rather than a v0.4.0 violation. The linter exits 1 + names the matching token at line:col on any violation; the publish worker blocks; the operator sees the lint failure via `[HUMAN ACTION] ship-lint-failure: <token> at <line>:<col>` and either revises the commit-message or explicitly overrides (with operator quote in the master ticket comment).

### Anti-patterns (rejected)

1. **Ship without `## Alice amendment path` entry.** A push that does not record the amendment path in the master body is a v0.5.0 violation — the audit trail breaks; the operator cannot reconstruct which finding produced this commit.
2. **Skip the HITL gate.** A push that runs without the operator's approve (the `[HUMAN ACTION]` child) is a v0.5.0 violation — the operator's Discord history loses the approve signal; the audit-trail's `## Source` section cannot cite the operator's approve.
3. **Bypass the topic-only linter.** A commit-message that contains a forbidden token but is pushed anyway is a v0.5.0 violation — the linter must run + exit 0 BEFORE `git commit` is called.
4. **Push the operator-instance repo.** The ship lane is Alice-only by operator direction (`M-decide-x-article-review-flow.md` v0.5.0 out-of-scope: "Ship is Alice-only"); pushing to the operator-instance repo would surface the alice-framework changes into the operator's working tree.
5. **Skip the audit-trail writes.** A push that lands the GitHub release but does not append the audit-line / vault copy / master ticket comment is a v0.5.0 violation — the durability guarantee (vault-side audit trail) breaks. The compliance-verifier checks for all three audit artefacts (push-done comment + log.md audit-line + vault copy pair).
6. **Auto-close META on push-done without operator review.** The push-done is necessary but not sufficient for META done — the operator still owns the master close (per op-guard-10 v2 §1.1, work_shipped_via_<carrier> requires a verified artifact + downstream consumer). The push-done is the artifact; the operator's downstream-consumer signal (LGTM, reply-to-HITL, or explicit close) is the consumer.

### Cross-reference

The alice-publish-flow method spec lives in `methodology/M-decide-alice-publish-flow.md` v0.2.0 (the 10-step pipeline with 5 gates per op-guard-19). This Part 7.5.1 is the **conditional trigger + audit-trail contract** for that pipeline; the pipeline's per-step mechanics (pre-verify, work-complete check, verifier-ran check, CHANGELOG consolidation, per-file version reconciliation, CITATION.cff operator-gate check, commit, push, tag, release, audit-line, close) are unchanged from `M-decide-alice-publish-flow.md` STEP 1-10. The novelty in v0.5.0 is **when** the pipeline fires (conditional on `## Alice amendment path` + operator HITL approve) and **what** it produces beyond the existing `M-decide-alice-publish-flow.md` outputs (the master-ticket `## push-done` comment + the vault copy pair at `2-ATOMIC/decisions/article-<id>-release-notes.{md,pdf}`).

---

## Part 8: The child graph

```
[MASTER] article-review: <author> — <short title>
  assignee = jarvis|planner (orchestrator lane — NEVER a phase worker)
  │
  ├── [Phase 0] retro child              (verifier, blocked needs_input)
  │     parents=[master]
  │
  ├── phase 1: retrieve + quote          (web-research)
  │     parents=[master]
  │     └── V-1: quote is real body text (verifier)   ← hard gate
  │           parents=[P1]
  │
  ├── phase 2: survey prior art          (web-research)
  │     parents=[P1, V-1]                ← blocked until V-1 PASS (F-1, F-3)
  │     └── V-2: sources resolve         (verifier)   ← light gate
  │           parents=[P2]
  │
  ├── phase 3: compare to Alice          (doc-writer)
  │     parents=[P2, V-2]                ← P3.started_at ≥ P2.completed_at (F-3)
  │     └── V-3: claims about Alice true (verifier)   ← MANDATORY
  │           parents=[P3]
  │
  └── phase 4: council synthesis         (council)
        parents=[P3, V-3]
        └── operator disposition on master (blocked needs_input after P4)
```

**Strict sequential parents (F-3):** do **not** file P2/P3/P4 with only
`parents=[master]`. That pattern auto-promoted all children when the master
prematurely completed after Phase 1 on article 1. Each phase's `parents`
must include the prior phase id (and its verifier child when present).

Every child carries the six required metadata fields from `methodology/04a-decide-work-graph.md` Part 5: `goal`, `original_ask`/`context`, `task_specific_info`, `process`/`flow`, `skill_version`, `acceptance_criteria`. The `process` field may reference this doc by path rather than restating the phase inline.

Phases are sequential — each consumes the prior phase's output — so here the parent edge and the dependency edge coincide. That is a property of this flow, not a general rule; `04c` Part 5 keeps them distinct for good reason.

---

## Part 9: Anti-patterns

1. **Reviewing the preview card.** The title and the one-line preview are not the article. V-1 exists because this failure is invisible in the output — the review reads fine and is about nothing.
2. **Claiming a gap without searching.** Alice has 46 docs and templates. Search the *concept* under several vocabularies before asserting absence; V-3 checks this independently.
3. **Adopting because it was well-written.** Article quality is not evidence of fit. The Skeptic seat's job is to say so.
4. **Resolving a contradiction in phase 3.** The doc-writer flags; the operator decides. A doc-writer who resolves it has made a framework decision nobody delegated.
5. **Writing a separate findings file.** The master body is the findings doc. Two surfaces drift on the first disposition.
6. **Batching several links into one master.** Each link is independent evidence with independently dispositioned findings. One link, one master.
7. **Auto-accepting on operator silence.** Silence is DEFER. A flow that adopts on timeout lets an unread article edit the framework.
8. **Shipping an accepted finding straight into a public doc.** Acceptance opens a spec-first ticket; it does not bypass the normal gates.

---

## Part 10: Verification recipe

For a completed master:

1. The phase-1 child contains a verbatim body-text quote, not a title.
2. Every finding names a real Alice path — spot-check that each file exists.
3. Every "no coverage" claim has an independent second search recorded by V-3.
4. Every finding has exactly one classification and one disposition.
5. Every ACCEPTED finding has a child ticket that exists in the kanban DB.
6. Every REJECTED finding has a stated reason; every DEFERRED one has a date or condition.
7. Nothing repo-bound contains operator-instance content, per `M-decide-parity-check.md`.
8. Retro-A produced `human-digest.md` from `methodology/M-decide-human-digest.md`; the master comment begins with `Digest ready`; Retro-H opens with the digest pointer.
9. **(v0.3.0) Phase 4 verdict carries the per-finding source attribution.** Every finding has a populated `(b) Phase 2 sources considered` list with URLs; the bottom-tier `Phase 2 sources by finding` block is present and complete; the verdict does NOT carry Phase 2 URLs in the headline, action items, or operator-decision tiers. V-4 (Phase 4 verifier) confirms all three sub-checks. A verdict missing any of these is REFUSED with a `Verdict generation REFUSED — missing per-finding source attribution` pointer comment on the Phase 4 council ticket.
10. **(v0.5.0) Push-done evidence when Alice amendment in scope.** When the master ticket body records one or more `## Alice amendment path` entries, the master's transition to `done` requires the four-part push-done evidence: (a) `## push-done — <commit-sha> ... — <topic-list>` `kanban_comment` on the master ticket, (b) audit-line in `~/Documents/HermesVault/log.md` per op-guard-11 + op-guard-19 with `result=<success|failure|refused>` + release-url + commit-sha + topic-list, (c) vault-copy pair at `~/Documents/HermesVault/2-ATOMIC/decisions/article-<id>-release-notes.{md,pdf}`, (d) master ticket body `## Release notes` section populated with the canonical 5 sections per `M-decide-alice-publish-flow.md` STEP 10. Compliance-verifier confirms all four sub-checks. A master missing any of these on close is REFUSED with a `META close REFUSED — push-done evidence incomplete` pointer comment on the master ticket.
11. **(v0.5.0) Topic-only commit-message lint.** When the ship-lane ran, the commit-message headline AND the GitHub release-notes title + headline MUST each pass `~/.hermes/tools/topic_only_lint.py` (exit 0); the lint output is captured in the ship-lane child ticket body under `## Lint output` and cross-referenced from the master's `## push-done` comment. Compliance-verifier confirms both sub-checks.

Board-sweep line for an open master:

```text
[MASTER] article-review: <author> — running, 3/4 phases done
[MASTER] article-review: <author> — all phases done, operator disposition pending (F-1..F-4)
```

---

## Part 11: Relationship to the other external-source docs

Three docs touch external content; they do different jobs.

| Doc | Question it answers | Public? |
|---|---|---|
| `methodology/09-inbox-from-external-sources.md` | How does an external item get captured, filtered, and routed to a vault tier? | Yes |
| `references/x-ingestion-pipeline.md` | How does the 4-stage X pipeline operate at volume? | Yes |
| This doc | Does this one article change **Alice**? | No — META-internal |

The 09 doc handles volume, and its filter assumes most items are noise. This flow handles the opposite case: a single item the operator has already filtered by hand, where the analysis is deep rather than broad. An article that reaches this flow has skipped stages 1-2 of the pipeline — the operator's decision to post the link *is* the filter.

## Changelog

- **0.5.0** (2026-08-10, t_cd29b136, amendment 4): New `## Part 7.5 — META done-gate (extended)` codifies that the META done-gate gains a `push-done` precondition (in addition to Retro-A + Retro-H + compliance-PASS) when an Alice-doc amendment is in the apply set; the baseline gate (Retro-A + Retro-H + compliance-PASS) is the entire gate when no Alice amendment is in scope (common case). New `## Part 7.5.1 — Conditional Alice-only GitHub ship` codifies the 6-step pre-ship sequence (collect changed files + version delta → topic extractor with the forbid-list → commit-message drafter with the forbid-list + max-5-topic rule → PR diff packager → operator HITL approve gate with `[HUMAN ACTION]` prefix → `git push` + optional `v<X.Y.Z>` tag + `gh release create`) + the audit-trail contract (`## push-done — <commit-sha> at <ISO> — <release-url> — <topic-list>` `kanban_comment` on master + audit-line appended to log.md per op-guard-11 + op-guard-19 + vault-copy pair at `2-ATOMIC/decisions/article-<id>-release-notes.{md,pdf}` for durable operator-facing audit). Part 3 §Release-message discipline extended under new sub-section "Git commit + GitHub release-notes discipline" so the topic-only contract covers the git commit headline AND the GitHub release-notes title + headline (with the forbid-list enforced by `~/.hermes/tools/topic_only_lint.py` at pre-push — linter is a tool, not human judgment). Part 5 dispatch extended to record that the ship lane runs as a separate child ticket via the registered `alice-publish` loop with `run_with_profile: coder` for technical prep + `assignee: jarvis` for the HITL gate (loop yaml amendment for the `alice-publish` entry in `~/.hermes/loops/hermes.yaml` is a loop-updater instance child per op-guard-17). Part 10 verification recipe extended with two new checks: (a) push-done evidence when Alice amendment in scope (4-part audit trail: master `## push-done` comment + log.md audit-line + vault copy pair + master `## Release notes` section); (b) topic-only commit-message lint passes via `~/.hermes/tools/topic_only_lint.py`. Companion instance changes per op-guard-5 paired-wiki integrity + op-guard-17 alice-first/instance-second/compliance-gate: coder child ships `~/.hermes/tools/topic_only_lint.py` + the audit-line appender (extend `~/.hermes/scripts/vault_log.py`) + the GitHub push wrapper + the push-done evidence helper; loop-updater child amends `~/.hermes/loops/hermes.yaml` alice-publish entry to invoke the new ship lane; compliance-verifier child extends the META done-gate to require `push-done` when `## Alice amendment path` is non-empty. Per op-guard-5, no other methodology doc requires paired-wiki amendment for v0.5.0 (`M-decide-human-digest.md` is unchanged — release-notes PDF flows through its canonical 8-section shape, not the retro specialization). Reference: `/Users/homestead/alive_x_apply_graph_v2.{html,png}` (the v2 visual with the indigo conditional ship lane). Source: operator direction 2026-08-10 + `t_cd29b136`.
- **0.4.0** (2026-08-09, t_a92c1f88): New `## Release-message discipline` sub-section under Part 3 codifies that the operator-facing Discord message that announces an alice-framework release MUST describe the topic(s) changed, NOT the article author. Canonical format: `Alice v<X.Y.Z>: <topic-1>, <topic-2>, ...` (no article URL/handle, no brackets). The article stays in the master ticket body `## Source` block for the audit trail; the vault audit-line + methodology amendment rationale + CHANGELOG.md `## Source` sub-section + GitHub release notes may still cite the article as one source among several, but the operator-facing release message is topic-only. The rule is the canonical answer to Retro-H body observation (2026-08-09, operator verbatim: *"The message on the push should mention what topic was changed but not the article author, the article was just the inspiration for the change."*). Companion instance changes (per op-guard-16 + op-guard-17 alice-first / instance-second): `~/.hermes/loops/intents/alice-publish-intent.md` v0.3.0 (intent v0.2.0 → v0.3.0 ships the rule in the intent worker_prompt), `~/.hermes/loops/hermes.yaml` `alice-publish` v0.3.0 (loop yaml amendment ships in a coder child ticket; status remains `live` per the alice-publish-flow doc's Path C rule), `~/.hermes/tools/post_alice_release_discord.py` (NEW; ships the Discord delivery per the canonical format), `~/.hermes/tests/test_post_alice_release_discord.py` (NEW; 4-case regression suite per the ticket body). Per op-guard-5 paired-wiki integrity, the methodology + intent + loop-yaml + script + test ship in one change set. Per op-guard-17 compliance-gate, a verifier child confirms the instance matches the methodology rule on first publish under v0.4.0. Source: operator 2026-08-09 Retro-H body observation #3.
- **0.3.0** (2026-08-09, alice-ticket `t_c11e4845` / instance-ticket `t_673bcf04`): Phase 4 council verdict MUST surface the Phase 2 web_search findings per finding, not just the article URL. New mandatory sub-section §"Source-attribution discipline" under Part 7 codifies per-finding evidence anchors pointing to the Phase 2 sources the council actually considered. The `## Evidence anchors` section of the executive-reporting digest MUST list the Phase 2 web_search URLs by finding so the operator can audit the council's source set in 10 seconds. Paired with `~/.hermes/loops/intents/x-article-review-intent.md` v0.6.0 (intent worker_prompt Phase 4 + verify gates) + `~/.hermes/tools/post_council_verdict_pdf.py` (script that populates the Evidence anchors section). Per op-guard-5 paired-wiki integrity + op-guard-17 alice-first/instance-second/compliance-gate, the doc-writer ships the methodology amendment; the coder applies the spec to the script + intent in instance children. **v0.2.3** (2026-08-09, t_84782c7c): Part 3 rename discipline strengthened — a `## Rename` comment is now a request that MUST be applied via `hermes kanban edit --title` on the next orchestration tick, not a soft suggestion. Added a new `## Title-rendering discipline` sub-section under Part 3 codifying the orchestrator's rename-application contract + the failure surface when the rename cannot be applied. The 80-char title budget + the title-recovery ladder (xurl → web_search → browser → post-id fallback) are flow-agnostic in `methodology/04c-decide-master-ticket.md` Part 8 §Master Title Discipline (v0.1.3 amendment in the same change set, paired per op-guard-5). The companion instance change is `~/.hermes/tools/file_x_article.py` (8th-method title-recovery ladder + post-creation 80-char assertion + `## Title source` block); a coder child ticket ships the script + 6-case regression suite. **v0.... [truncated, 381 chars omitted]
- **0.2.3** (2026-08-09, t_84782c7c): Part 3 rename + title-rendering discipline. A `## Rename` ticket comment is now a rename request that MUST be applied via `hermes kanban edit --title` on the next orchestration tick (the comment is a request, not an application); orchestrator appends `## Rename applied` confirmation with old title + new title + operator-quoted rationale. New `## Title-rendering discipline` sub-section under Part 3 codifies the three application points (filing time, post-Phase 1 auto-recovery, operator rename) + the failure surface when `hermes kanban edit --title` fails. The 80-char title budget + the title-recovery ladder (method 1 xurl → method 2 web_search → method 3 browser_snapshot → method 4 post-id fallback) are flow-agnostic in `methodology/04c-decide-master-ticket.md` Part 8 §Master Title Discipline (v0.1.3 amendment in the same change set, paired per op-guard-5 paired-wiki integrity). Companion instance change: `~/.hermes/tools/file_x_article.py` ships the title-recovery ladder + post-creation 80-char assertion + `## Title source` block; coder child ticket ships the script + 6-case regression suite. Source: operator Retro-H body observation 2026-08-09 ("when we rename the master ticket, the name should reflect the topic + author").
- **0.2.2** (2026-08-07, t_e35032aa): Phase 4 council verdict is now an operator-review artifact. The 5-step delivery protocol from `methodology/03b-decide-operator-agent-interaction.md` Part 13 (Markdown → PDF → Discord attachment → ticket comment → vault copy) is owned at the methodology level under Part 7 ("The 5-step operator-review PDF delivery"). Implementation lives in `~/.hermes/tools/post_council_verdict_pdf.py` (paired with `post_graph_retro_human_digest.py` for Retro-A); the x-article-review intent doc v0.2.2 (`t_f99d8fb7`) is the wiring. Per op-guard-16, the doc-writer ships the methodology; the council/skill-curator lane applies the spec to the skill body. The 8-section PDF shape (per `templates/human-digest.md.template`) is mandatory.
- **0.2.1** (2026-08-07, t_ea62fcff): Phase 4 now selects council seats with the three-signal algorithm in `M-decide-council-sizing.md`; the ticket records reversibility, stakeholder breadth, dimensions, named seat justifications, and total count before deliberation. The verdict preserves the selected-seat composition rather than assuming five seats.
- **0.2.0** (2026-08-07, t_ba4b9a90): Promoted from `_inbox/` to
  `methodology/`. Applied article-1 retro F-1..F-8 ACCEPTED:
  - F-1: V-1 hard gate blocks P2–P4; 4-method retrieval ladder mandatory
  - F-2/F-8: master assignee = orchestrator lane (jarvis/planner); done-gate
    = operator-LGTM + blocked(needs_input) after P4
  - F-3: strict sequential `parents=[prior_phase_id, prior_V]`
  - F-4: 3-option X body-access preflight in master template
  - F-5: master body IS findings doc; classification enum
    covered|gap|contradiction; council seats selected per
    `methodology/M-decide-council-sizing.md` and the three-signal rule
    (3–5 seats; Operator + Product required for this framework/user-facing flow)
  - F-6: paired with `post_graph_retro.py` gate (suppress 0-INBOX when
    kanban retro child exists)
  - F-7: attribution requires primary URL + byline
  - Paired intent `x-article-review-intent.md` bumped 0.1.0 → 0.2.0
- **0.1.0** (2026-08-07): Initial draft in `_inbox/`.

## Open questions

- **Q1: Multi-article synthesis.** Three articles may each produce a weak gap that together form a strong one. One-master-per-link cannot see that. A future roll-up across masters may be warranted; deferred until enough masters exist to make it real.
- **Q2: Article durability.** x.com posts get deleted. The verbatim quote in phase 1 is the durable record; whether to archive full text and where is unresolved.
