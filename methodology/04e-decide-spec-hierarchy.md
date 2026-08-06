---
id: alice-methodology-04e-decide-spec-hierarchy
created: 2026-08-05T15:36:00Z
updated: 2026-08-05T15:38:00Z
title: "Methodology 04e — Decide your spec hierarchy (request → architecture → impl plan → tests → tickets)"
type: methodology
status: draft
source: alice-framework
tags: [kind:methodology, kind:spec, kind:work-graph, kind:harness, project:alice]
confidence: 0.0
links: ["[[methodology/04a-decide-work-graph.md]]", "[[methodology/04b-decide-board-routing.md]]", "[[methodology/04c-decide-master-ticket.md]]", "[[methodology/01b-decide-vault-content.md]]", "[[methodology/03b-decide-operator-agent-interaction.md]]", "[[references/kanban-lite-disciplines.md]]", "[[references/tool-mapping-guide.md]]", "[[templates/frontmatter-schema.md.template]]"]
---

# Methodology 04e — Decide your spec hierarchy

> A request enters the system as words; it leaves as shipped work. The spec hierarchy is the chain of documents that carries the request from the operator's first sentence to the verifier's final pass. Each link in the chain has a single responsibility: the request doc holds the original ask; the supporting docs hold the detailed decisions; the kanban tickets hold the actual work. If the chain breaks at any link, the work drifts.

This methodology explains **how to organize the chain of spec documents** that connects an operator's request to the kanban tasks that deliver it. The chain has one request doc at the top, several supporting docs beneath it, and a set of work tickets beneath those. Tasks on the kanban carry a small piece of spec metadata — the relevant spec doc IDs — so the work graph can answer "what request does this task serve?" and "what specs does this request need?".

The doc is tool-agnostic by design. The abstract chain belongs here; per-tool file layouts and CLI invocations belong in `references/tool-mapping-guide.md`.

---

## Part 1: Why a spec hierarchy

A request without a hierarchy has no way to distinguish *what was asked* from *what was decided* from *what was built*. Three documents get conflated:

1. The **original request** — what the operator typed, with all its tone, constraints, and clarifying questions.
2. The **derived decisions** — the architecture, the implementation plan, the test plan, the metric targets. These are answers to the original request.
3. The **work tickets** — the kanban tasks that perform the work.

When these three live in one place (the body of a master ticket, or worse, the body of a single task ticket), three failure modes appear:

- **Request drift.** The original ask gets paraphrased away. Constraints disappear. Clarifying questions lose their answers. Months later, the operator asks "did we build what I asked for?" and the system can't answer.
- **Decision invisibility.** Architecture choices hide in task bodies. A reader who joins late cannot find the decision trail.
- **Work spec loss.** Each task ticket's body holds only the task's narrow scope. The wider request is lost the moment the task is closed.

A spec hierarchy fixes all three by giving each concern its own document and linking the documents together.

### The four-question test

Open a spec hierarchy when:

1. **Is the request multi-day?** The work will pass through more than one session or worker.
2. **Is the request multi-decision?** Multiple distinct decisions (architecture, plan, tests, metrics) need to be made and recorded.
3. **Is the request multi-task?** The work will fan out into more than one task ticket.
4. **Does the work need an audit trail?** The operator (or a verifier, or a future reader) will need to reconstruct why a particular choice was made.

If any of these are yes, open a request doc. If 2+ are yes, also open at least one supporting doc per distinct decision area (architecture, implementation plan, tests, monitoring).

### What the hierarchy is not

It is not a substitute for the master ticket (see `methodology/04c-decide-master-ticket.md`). The master ticket is the operator-facing status surface; the spec hierarchy is the documentation substrate. A request doc often opens a master; the master points to the request doc as the canonical reference, but they are different artifacts.

It is not a substitute for the work-graph metadata ticket (the HARNESS / work-graph metadata ticket is its downstream). The work-graph metadata ticket specifies *what fields a task carries*; this doc specifies *what docs exist* in the first place.

It is not a substitute for atomic note discipline (`methodology/01b-decide-vault-content.md`). The spec docs are themselves notes; they follow the same atomicity rules, the same frontmatter schema, and the same lifecycle as every other note in the vault.

---

## Part 2: The chain — three layers

The spec hierarchy is a three-layer chain. Each layer has a clear role; each layer points to the next.

```text
┌─────────────────────────────────────────────────────────────┐
│ LAYER 1 — REQUEST DOC                                        │
│   The operator's original ask.                               │
│   One file per request.                                      │
│   Single source of truth for "what was requested".           │
└────────────────────────┬────────────────────────────────────┘
                         │ parent of
                         ▼
┌─────────────────────────────────────────────────────────────┐
│ LAYER 2 — SUPPORTING DOCS                                    │
│   Architecture, implementation plan, test plan, metrics.     │
│   One file per decision area.                                │
│   Each has frontmatter `parent` pointing to the request doc. │
└────────────────────────┬────────────────────────────────────┘
                         │ linked from
                         ▼
┌─────────────────────────────────────────────────────────────┐
│ LAYER 3 — WORK TICKETS                                       │
│   Kanban tasks that perform the work.                        │
│   Each task body carries the spec metadata (parent spec doc,│
│   supporting docs read for this task).                       │
└─────────────────────────────────────────────────────────────┘
```

### Layer 1: Request doc

The request doc is the **canonical entry point** for the operator's request. Its responsibilities:

- preserve the original request **verbatim**;
- capture clarifying questions and their answers (as the request matures);
- declare which supporting docs will exist for this request;
- declare the originating master ticket (if any) and the broad routing of its children.

The request doc does **not** hold the architecture. It does **not** hold the implementation plan. It holds only what the operator asked for and which decisions are still open.

A request doc typically lives at `specs/<request-id>/README.md`. The folder is the request's home; the README inside it is the request doc itself.

### Layer 2: Supporting docs

Supporting docs are the **decision artifacts** for the request. Each decision area gets its own doc:

- **`architecture.md`** — system shape, module boundaries, data flow, integration points, key trade-offs.
- **`implementation-plan.md`** — sequencing, milestones, dependencies between work areas, file-level map.
- **`test-plan.md`** — what will be tested, how, by whom; acceptance criteria; verifier assignments.
- **`monitoring-and-metrics.md`** — what to measure, target values, alerting, dashboards.
- (Add more as the request demands — e.g., `data-model.md`, `api-contract.md`, `migration-plan.md`.)

Each supporting doc is a **separate file** with its own frontmatter, its own lifecycle, and its own reviewers. One supporting doc = one decision area. Do not bundle architecture + plan into a single "design doc"; the boundary between them is the boundary between *what* and *how*, and conflating them loses the audit trail.

A supporting doc's frontmatter `parent` field points to the request doc. Its `links` field points to other supporting docs it depends on (e.g., the test plan depends on the architecture).

### Layer 3: Work tickets

Work tickets are the **execution layer**. Each kanban task has a body that:

- declares which supporting doc(s) it implements (e.g., "Implements step 2 of `implementation-plan.md`");
- declares which request doc it serves (typically via the supporting-doc chain; the worker doesn't have to repeat the request doc ID);
- carries the work-graph metadata (assignee, board, parent/child links, done-gate, evidence).

The ticket body is **not** the place to copy the request. The ticket body is the place to say "this task is one of N that serve request R; here is the slice of R's implementation plan this ticket covers."

Verifiers are a special sub-layer of work tickets. Each major work ticket (or group) gets a paired verifier ticket — a worker that checks the work ticket's evidence against the supporting doc's acceptance criteria. The verifier body references both the work ticket ID and the supporting doc.

---

## Part 3: File-naming convention

Alice uses a **per-request folder** layout. Each request gets one folder; the folder holds all the docs that belong to that request.

```text
specs/
└── <request-id>/
    ├── README.md                  # the request doc itself
    ├── architecture.md            # supporting doc
    ├── implementation-plan.md     # supporting doc
    ├── test-plan.md               # supporting doc
    └── monitoring-and-metrics.md  # supporting doc (if needed)
```

### The folder is the unit

The folder, not the README, is the unit of versioning, the unit of review, and the unit of archival. A request that completes gets archived as a whole; a request that revises gets revised as a whole.

### The request-id slug

`<request-id>` is a short, descriptive, kebab-case slug that uniquely identifies the request. Three rules:

1. **Lowercase, kebab-case.** `fantasy-football-draft-tool`, not `Fantasy Football Draft Tool`.
2. **Stable across revisions.** The slug never changes for the lifetime of the request. Renames go in the body or frontmatter, not in the folder name.
3. **Stable across boards.** If a request fans out across multiple kanban boards, the spec folder still uses one slug. Cross-board routing does not fragment the spec.

A good slug is **what the operator would type if they had to name the request in 3 words**. A bad slug is a UUID (`a3f9...`) or a date (`2026-08-05-spec`) — both are opaque and resist grep.

### File names within a folder

The README inside the folder is the request doc. Supporting docs use plain-language filenames. Alice recommends:

- `architecture.md` (not `arch.md`, not `001-architecture.md`)
- `implementation-plan.md` (not `impl.md`, not `plan.md`)
- `test-plan.md` (not `tests.md`, not `qa.md`)
- `monitoring-and-metrics.md` (not `metrics.md`)

Plain-language filenames are greppable across requests. If every request uses `architecture.md`, then `grep -r "## Architecture" specs/` finds every architecture doc in the vault. Variant filenames defeat this property.

### When to deviate

A request that genuinely needs more than ~5 supporting docs should split its hierarchy. Two patterns:

- **A request with too many decision areas** — break the request into sub-requests, each with its own folder. The parent request doc becomes a master doc that links to the children.
- **A long-running request** — use subfolders: `specs/<request-id>/phase-1/README.md`, `specs/<request-id>/phase-2/README.md`. The top-level README becomes a navigation hub.

The two patterns are not exclusive. A large request can have phase folders, each with their own supporting docs.

---

## Part 4: Spec-frontmatter pattern

Each spec doc carries a frontmatter block that ties it into the chain. The schema is small and strict.

```yaml
---
id: <unique-doc-id>
created: <ISO8601-UTC>
updated: <ISO8601-UTC>
type: spec
status: <draft | active | review | done | archived>
parent: <request-doc-id>      # for supporting docs only; request doc itself has no parent
title: "<one-line title>"
links:
  - "[[<related-spec-doc-id>]]"
  - "[[<related-ticket-id>]]"
  - "[[<related-master-ticket-id>]]"
tags: [kind:spec, kind:request | kind:architecture | kind:impl-plan | kind:test-plan | kind:metrics]
source: alice-framework
---
```

### Field-by-field

| Field | Required | Applies to | Purpose |
|---|---|---|---|
| `id` | yes | all | unique 7-char hash; used by wikilinks and task metadata |
| `created` | yes | all | ISO8601-UTC timestamp |
| `updated` | yes | all | ISO8601-UTC timestamp; bumped on every edit |
| `type` | yes | all | literal `spec` for every spec doc |
| `status` | yes | all | `draft` until the doc is reviewed; `active` while the request is in flight; `done` after the request is closed but the docs are still referenced; `archived` once retention is no longer useful |
| `parent` | yes (supporting) | supporting docs only | the request-doc-id of the parent request doc |
| `parent` | no (request) | request doc | the request doc has no parent; it is the root of its chain |
| `title` | yes | all | the doc's one-line title |
| `links` | yes | all | wikilinks to related docs and tickets |
| `tags` | yes | all | one of `kind:request`, `kind:architecture`, `kind:impl-plan`, `kind:test-plan`, `kind:metrics` |
| `source` | yes | all | provenance — typically `alice-framework` or the project's name |

### The `parent` field is the chain

The `parent` field is what makes the hierarchy queryable. A query for "what spec docs serve this request?" is "find all docs whose `parent` equals this request-doc-id." A query for "what request does this doc belong to?" is "follow the `parent` field up."

For the request doc itself, `parent` is absent or empty — the request doc is the root. For supporting docs, `parent` is mandatory.

### The `links` field is the graph

`links` carries wikilinks to anything related but not in the parent chain:

- other supporting docs at the same level (e.g., architecture depends on test plan);
- the master ticket that owns the work;
- the verifier sub-tickets;
- related vault notes (e.g., a methodology doc this request exemplifies).

`links` is additive: every related artifact goes here. The `parent` field is hierarchical; `links` is everything else.

### `status` lifecycle

The spec doc follows the work it describes:

- **`draft`** — the doc is being written. Not yet reviewed. The request is not yet active.
- **`active`** — the request is in flight. Workers may reference the doc; the doc is the live source of truth.
- **`review`** — a decision is being revisited. The doc is correct but under change. Workers should consult the doc but verify the master ticket for the current plan.
- **`done`** — the request is closed. The doc is frozen. Workers may still read it for context.
- **`archived`** — the doc is no longer referenced. It lives on for audit only.

Transitions: `draft → active → done → archived`. `active → review → active` is allowed when a doc is revised mid-flight. `review → active → review → done` is the normal close pattern.

---

## Part 5: The chain in the work graph

The spec hierarchy is **documentation**; the work graph is **execution**. They connect through task metadata.

Each kanban task that implements a request carries:

```yaml
spec:
  request: <request-doc-id>
  supporting:
    - <architecture-doc-id>
    - <implementation-plan-doc-id>
  slice: "<one-line: which part of the impl plan this task covers>"
```

This is the HARNESS / work-graph metadata field. The schema is small: one `request`, a list of `supporting` doc IDs, and a `slice` that names what this task does.

### Why the task carries the chain

Two reasons:

1. **The worker reads it on cold-start.** A worker spawned on the task sees the request context without having to navigate the vault. The spec metadata is the bridge from task to spec.
2. **The verifier reads it on review.** A verifier spawned to check the task sees the acceptance criteria (in the supporting docs) without having to guess.

The metadata field is a **pointer**, not a copy. The task body does not re-state the architecture; it references the architecture doc. The architecture doc remains the single source of truth.

### What the metadata is not

It is not a substitute for the master ticket. The master holds status; the task metadata holds context.

It is not a substitute for the work-graph's parent/child links. A task's parent (its master ticket or its parent spec-doc-ticket) is a separate field; the spec metadata is the documentation pointer.

It is not a substitute for the task body. The body holds the acceptance criteria for this specific task; the spec metadata points to the docs that hold the request's broader context.

---

## Part 6: Worked example — single request → full chain

The worked example illustrates the chain with one operator request: **build a Fantasy Football Draft Tool** (the same request used in `methodology/04c-decide-master-ticket.md`'s worked example).

### Step 1: Open the request doc

The orchestrator receives the operator's request:

> "Build a Fantasy Football Draft Tool."

The orchestrator creates the spec folder and the request doc:

```text
specs/fantasy-football-draft-tool/
└── README.md                  # the request doc
```

The README.md frontmatter:

```yaml
---
id: alice-spec-ff-draft-req
created: 2026-08-05T15:30:00Z
updated: 2026-08-05T15:30:00Z
type: spec
status: active
title: "Fantasy Football Draft Tool — request"
tags: [kind:spec, kind:request]
links: []
source: alice-framework
---
```

The body:

```markdown
## Original request
> Build a Fantasy Football Draft Tool.

## Clarifying questions
- TBD: target league size? scoring format? draft type (snake / auction / bestball)?
- TBD: data source (manual entry / Sleeper API / Yahoo API)?
- TBD: deploy target (CLI / web / Discord bot)?

## Supporting docs
- `architecture.md` — to be drafted
- `implementation-plan.md` — to be drafted
- `test-plan.md` — to be drafted
- `monitoring-and-metrics.md` — to be drafted (likely minimal — local tool)

## Master ticket
- `t_<master-id>` — orchestrator-owned master ticket for this request.

## Routing
- spec docs → `specs/fantasy-football-draft-tool/`
- work tickets → `[your-tool]-infrastructure` board (tool scaffolding)
```

The request doc is opened **before** any decisions are made. It captures what was asked and what is still unknown.

### Step 2: Draft the supporting docs

As the BA (business analysis) flow produces answers, the supporting docs are written. Each supporting doc gets its own file inside the same folder:

```text
specs/fantasy-football-draft-tool/
├── README.md
├── architecture.md
├── implementation-plan.md
├── test-plan.md
└── monitoring-and-metrics.md
```

Each supporting doc's frontmatter includes `parent: alice-spec-ff-draft-req`. For example, `architecture.md`:

```yaml
---
id: alice-spec-ff-draft-arch
created: 2026-08-05T16:00:00Z
updated: 2026-08-05T16:00:00Z
type: spec
status: review
parent: alice-spec-ff-draft-req
title: "Fantasy Football Draft Tool — architecture"
tags: [kind:spec, kind:architecture]
links:
  - "[[alice-spec-ff-draft-req]]"
source: alice-framework
---
```

The `parent` field is the chain link. The `links` field includes the request doc as a back-reference (the parent link is for queries; the back-link is for navigation).

### Step 3: Open the master ticket

The orchestrator opens the master ticket on the originating board (per `methodology/04c-decide-master-ticket.md`). The master's body references the request doc:

```markdown
## Original request
> Build a Fantasy Football Draft Tool.
> See request doc: [[alice-spec-ff-draft-req]]

## Planned flow
1. Business analysis and clarifying questions → see `specs/fantasy-football-draft-tool/README.md`
2. Architecture → see `specs/fantasy-football-draft-tool/architecture.md`
3. Implementation plan → see `specs/fantasy-football-draft-tool/implementation-plan.md`
4. Build work → N kanban tasks, each referencing the supporting docs
5. Verification → verifier sub-tasks against `test-plan.md`
```

The master ticket's body is **not** the spec. The master ticket is the operator-facing status surface; the spec docs are the documentation. The master points to the docs; it does not contain them.

### Step 4: Spawn the work tickets

As the implementation plan matures, the orchestrator creates the work tickets. Each ticket body carries the spec metadata:

```markdown
---
spec:
  request: alice-spec-ff-draft-req
  supporting:
    - alice-spec-ff-draft-arch
    - alice-spec-ff-draft-impl
  slice: "Implement the draft state machine (Step 2 of impl plan)"
---

## Task
Implement the state machine that drives draft picks.

## Acceptance criteria
- See `specs/fantasy-football-draft-tool/implementation-plan.md` §2
- Tests against `specs/fantasy-football-draft-tool/test-plan.md` §2

## Verifier
- `t_<verifier-id>` — verifier sub-task
```

The task body is **bounded**: it names the slice of work, references the supporting docs, and links the verifier. It does not duplicate the architecture.

### Step 5: Spawn the verifier sub-tasks

For each major work ticket, the orchestrator spawns a paired verifier ticket. The verifier body references both the work ticket and the supporting docs:

```markdown
---
spec:
  request: alice-spec-ff-draft-req
  supporting:
    - alice-spec-ff-draft-test
  slice: "Verify draft state machine implementation"
---

## Verifier task
Review the implementation of `t_<work-ticket-id>` against `specs/fantasy-football-draft-tool/test-plan.md` §2.

## Pass criteria
- All acceptance criteria met
- Tests pass
- Evidence attached to `t_<work-ticket-id>`
```

The verifier is itself a work ticket. It has its own spec metadata. The chain is consistent at every layer.

### Step 6: Close the chain

As children finish, the master moves toward its done-gate. When the master closes, the spec docs transition:

- `README.md` (request doc) — `status: active` → `status: done`
- `architecture.md`, `implementation-plan.md`, `test-plan.md`, `monitoring-and-metrics.md` — `status: active` → `status: done`

The folder remains on disk. Future workers may still reference it (e.g., a new feature request may extend the architecture). The transition to `archived` happens after a retention period (e.g., when the work has not been referenced for 90 days).

### What the example produces

For a single operator request, the chain yields:

| Layer | Artifacts | Count |
|---|---|---|
| Request doc | `README.md` | 1 |
| Supporting docs | `architecture.md`, `implementation-plan.md`, `test-plan.md`, `monitoring-and-metrics.md` | 2–5 (depending on request shape) |
| Work tickets | BA, planner, coder, verifier children | N (depends on implementation plan) |
| Verifier sub-tickets | Paired verifier for each major work ticket | N (typically 1 per major work ticket) |

For the Fantasy Football Draft Tool, a representative breakdown is:

- 1 request doc
- 4 supporting docs (architecture, impl plan, test plan, metrics)
- ~8 work tickets (BA clarifying, architecture review, scaffolding, data ingestion, draft engine, UI, deploy, integration)
- ~3 verifier sub-tickets (architecture review, integration test, end-to-end test)

Total: 16 artifacts traceable back to a single operator request.

---

## Part 7: The chain and the work graph

The spec hierarchy is documentation; the work graph is execution. They have different jobs:

| Concern | Spec hierarchy | Work graph |
|---|---|---|
| What is being asked | Request doc | Master ticket body |
| What decisions were made | Supporting docs | Task comments + linked comments |
| What is being built | Supporting docs (impl plan) | Task body |
| What is verified | Supporting docs (test plan) | Verifier task + verifier events |
| What is done | Spec doc `status: done` | Master ticket `status: done` |

The two are **kept in sync by the orchestrator**. When a task moves to `done`, the orchestrator does not need to update the spec doc. When a spec doc moves to `review`, the orchestrator does not need to update the master. They are different views of the same work; they update independently.

### The chain survives board moves

A request that fans out across boards does not fragment the spec hierarchy. The spec folder is one place; the children live on whatever board owns their work-product domain. The `parent` field on each supporting doc still points to the request doc; the request doc's `links` field still includes the master ticket.

If the tool cannot represent cross-board parent/child links for tickets, the spec hierarchy becomes the durable reciprocal reference. The master → child link is in the kanban; the spec doc → master link is in the spec folder. Together they preserve the chain.

### The chain survives worker turnover

A worker that joins mid-flight reads the request doc + relevant supporting docs + their task body. The chain is the cold-start context for any new worker on any task. The operator does not need to brief every new worker — the spec hierarchy is the briefing.

---

## Part 8: Anti-patterns to watch for

1. **The "spec in the ticket body" anti-pattern.** Putting the architecture, the plan, the tests, and the metrics in the body of one ticket. The ticket becomes the spec, the spec becomes unauditable, and the work graph has no place to link to. *Fix:* put each decision area in its own supporting doc; the ticket body references the doc.

2. **The "one doc per request" anti-pattern.** Bundling architecture, plan, tests, and metrics into the request doc. The request doc becomes too long to read, the boundaries blur, and the audit trail collapses. *Fix:* one doc per decision area, all linked from the request doc.

3. **The "specs without `parent`" anti-pattern.** Writing supporting docs without a `parent` field, or with a `parent` field pointing to the wrong doc. The chain becomes un-queryable; "what request does this serve?" has no answer. *Fix:* every supporting doc carries `parent: <request-doc-id>`; the request doc has no `parent`.

4. **The "specs without `links`" anti-pattern.** Writing specs in isolation, with no references to related specs or tickets. The doc becomes a dead end — a worker reading it cannot navigate to the work or to the related decisions. *Fix:* every spec carries `links:` to its parent, its siblings, the master ticket, and the verifier tickets.

5. **The "task body duplicates the spec" anti-pattern.** Copying the architecture or the plan into the task body because "the worker might not read the spec." The spec loses its single-source-of-truth role. *Fix:* the task body references the spec via the metadata field; the worker reads the spec.

6. **The "spec folder without a README" anti-pattern.** Creating the folder and dropping supporting docs into it without a README. A reader opening the folder cannot find the request doc; the chain has no entry point. *Fix:* every spec folder has a `README.md` that is the request doc.

7. **The "stale spec" anti-pattern.** Leaving a spec doc at `status: active` long after the request has closed. A future worker reading the spec may assume the request is in flight and act on stale decisions. *Fix:* transition the spec doc's status alongside the master's status (`active → done` when the master closes; `done → archived` after retention).

8. **The "spec without verifier" anti-pattern.** Writing the test plan and the architecture, but never spawning the verifier tickets. The work graph has no acceptance gate; the spec is documented but unenforced. *Fix:* every supporting doc that defines acceptance criteria is paired with at least one verifier ticket.

9. **The "spec hierarchy without a master" anti-pattern.** Writing a complete spec hierarchy but never opening the master ticket. The spec has no operator-facing status surface; the work graph cannot show progress. *Fix:* the request doc links to the master ticket; the master ticket is the operator's progress view.

---

## Part 9: Verification recipe

A verifier checks the chain has been followed by running three queries against the work graph and the spec folder.

### Query 1: Every request doc has supporting docs

For each spec doc with `type: spec` and `tags: [kind:request]`:

```text
- Does the request doc declare which supporting docs it expects? (look for `## Supporting docs` in the body)
- Does each declared supporting doc exist on disk?
- Does each declared supporting doc have `parent` pointing to this request doc?
```

A request doc that declares `architecture.md` but the file does not exist is an incomplete chain.

### Query 2: Every work ticket carries the spec metadata

For each kanban ticket on a board that implements a request:

```text
- Does the ticket body carry a `spec:` block?
- Does `spec.request` point to a real request doc?
- Does `spec.supporting` contain at least one doc ID?
- Does each `spec.supporting` ID resolve to a real spec doc?
```

A work ticket without the spec metadata is a broken link in the chain — the worker on the ticket cannot find the broader context.

### Query 3: Every major work ticket has a verifier

For each non-trivial work ticket (not a 5-minute typo fix):

```text
- Is there a paired verifier ticket (typically via a `task_links` parent or a comment reference)?
- Does the verifier ticket's body reference the same spec docs?
- Does the verifier have its own `spec:` block?
```

A work ticket without a verifier is a request that lacks an acceptance gate.

### Combined audit

```bash
# Request docs without supporting docs:
ls specs/*/README.md | while read req; do
  dir=$(dirname "$req")
  if ! ls "$dir"/*.md 2>/dev/null | grep -v README.md > /dev/null; then
    echo "EMPTY: $dir"
  fi
done

# Work tickets without spec metadata:
grep -L "^spec:" $(hermes kanban list --output=paths) 2>/dev/null
```

The audit is a starting point. A real verifier reads the bodies, checks the chain, and reports the gaps.

---

## Part 10: Tool-mapping boundary

This methodology defines the conceptual chain and the frontmatter contract. It does **not** define tool-specific file paths, CLI invocations, or rendering rules.

| Concern | Belongs here | Belongs in `references/tool-mapping-guide.md` |
|---|---|---|
| The three layers | yes | — |
| File-naming convention (`specs/<request-id>/README.md`) | yes | — |
| Spec-frontmatter schema | yes | — |
| Status lifecycle | yes | — |
| Work-ticket spec metadata field | yes | — |
| How the tool creates a `specs/` folder | — | yes |
| How the tool validates the frontmatter | — | yes |
| How the tool renders wikilinks in a spec doc | — | yes |
| How a worker passes the spec metadata to a downstream worker | — | yes |
| How the operator browses the spec folder | — | yes |

The split keeps the method portable. A friend running Alice on a different tool keeps the three-layer chain, the frontmatter schema, and the work-ticket metadata; they adapt only the rendering and the CLI.

---

## Part 11: Adoption checklist

Before adopting the spec hierarchy, confirm:

- [ ] The vault supports a `specs/` folder layout (or its tool-specific equivalent).
- [ ] The frontmatter schema in use has `id`, `created`, `updated`, `type`, `status`, `parent`, `title`, `links`, `tags`, `source`.
- [ ] Wikilinks resolve across the vault (so `[[alice-spec-ff-draft-req]]` reaches the request doc).
- [ ] Work tickets have a body field that supports the `spec:` metadata block.
- [ ] The orchestrator can spawn verifier tickets and link them to work tickets.

For each new request, confirm:

- [ ] A request doc was opened with the operator's original ask verbatim.
- [ ] The clarifying questions section is empty (i.e., questions have been resolved) or marked TBD.
- [ ] The supporting docs are listed in the request doc's `## Supporting docs` section.
- [ ] Each supporting doc exists, has `parent` pointing to the request doc, and has `links` to its siblings.
- [ ] The master ticket exists and references the request doc in its body.
- [ ] Each work ticket has the `spec:` metadata block.
- [ ] Each major work ticket has a paired verifier ticket with its own `spec:` block.

When the request closes:

- [ ] The master ticket transitions to `done`.
- [ ] The request doc and all supporting docs transition from `active` to `done`.
- [ ] The `## Supporting docs` section is checked off (or marked done in some equivalent way).
- [ ] The spec folder remains on disk; archival is a separate retention decision.

---

## Part 12: When to revise this methodology

Revise the method when one of these changes:

- the three-layer chain becomes inadequate (e.g., a fourth layer emerges — a "design rationale" layer between request and architecture);
- the frontmatter schema needs new fields (e.g., `supersedes:` for chain versioning);
- a new artifact type appears (e.g., "spec PR" — a proposed change to a spec doc that needs review before merge);
- the work-graph metadata field needs new sub-fields (e.g., `spec.depends_on` for cross-spec dependencies);
- the file-naming convention stops fitting the vault structure (e.g., a friend runs Alice on a tool without folders).

Do not revise the method merely because a tool renames a field or a CLI changes. That belongs in the tool-mapping guide.

---

## See also

- `methodology/04a-decide-work-graph.md` — defines the work-graph state machine and relationship edges that the spec hierarchy connects to via the `spec:` metadata field.
- `methodology/04b-decide-board-routing.md` — defines how the children route across boards; the spec hierarchy lives independently of board boundaries.
- `methodology/04c-decide-master-ticket.md` — defines the master ticket as the operator-facing status surface; the request doc is the documentation root that the master points to.
- `methodology/01b-decide-vault-content.md` — defines the frontmatter schema, atomicity rules, and wikilink convention that spec docs follow.
- `methodology/03b-decide-operator-agent-interaction.md` — defines the operator-facing status and approval surfaces that the spec hierarchy feeds.
- `references/kanban-lite-disciplines.md` — supplies the minimal state, board, and 3-strike-system context on which the spec chain builds.
- `references/tool-mapping-guide.md` — owns per-tool CLI invocations and rendering rules; this methodology owns the chain and the frontmatter contract.
- `templates/frontmatter-schema.md.template` — the canonical frontmatter template; the spec-frontmatter pattern is an application of that schema.
- HARNESS / work-graph metadata ticket (downstream) — defines the `spec:` metadata field on work tickets.
- HARNESS / master ticket ticket (sibling) — defines the master ticket pattern that the request doc links to.

## Summary

The spec hierarchy is a three-layer chain: request doc, supporting docs, work tickets. Each layer has one job; each layer points to the next via frontmatter and wikilinks. The chain survives board moves, worker turnover, and request revisions. The orchestrator opens the request doc, drafts the supporting docs, opens the master ticket, and spawns the work tickets — each ticket carries the spec metadata that connects it to the chain.

A request without the chain is a request that drifts. A request with the chain is a request that can be verified, audited, and reused.