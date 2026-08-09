# Alice

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

Alice is a tool-agnostic methodology for designing a personal or small-team operating system supported by agents. It provides a decision process and fillable templates for organizing persistent knowledge, reusable skills, agent roles, scheduled work, work tracking, and rules derived from repeated failures. The result is an operating system adapted to the reader’s goals, tools, and constraints—not a copy of another setup.

**Alice is not an enterprise standards framework, not a turnkey installer, and not a vendor product.** It is a methodology you adapt; you provide the rest.

**Status:** v0.1.0 draft. The methodology is usable, but terminology and public-release packaging may still change.

## 1. What is Alice?

Alice is documentation-only: no build step, runtime, account, or private configuration is required. You read it, copy the templates that match decisions you have already made, and adapt them to your own context. Alice distinguishes the **methodology** (the decision process) from the **worked example** (an illustration of what that process can produce); the **method-not-instance** constraint keeps the method portable across tools and contexts. To use Alice you need Git, a Markdown editor, and a basic agent session you can describe. The first artifact typically takes 45–90 minutes to produce; the methodology is not "done" after that first pass.

### Non-goals

Alice explicitly is **not** any of the following:

- **An enterprise standards framework.** Alice documents a method, not a governed policy set for an organization.
- **A turnkey installer.** Alice has no install step, no account, no runtime — you copy templates and adapt them.
- **A vendor product.** Alice is tool-agnostic; it does not privilege one commercial agent or platform.

For the reader-facing "is Alice for me?" checklist, see §2 below; the methodology-level non-goals above are the positioning that anchors it.

### Ongoing cost

After the first artifact, the work continues. The recurring costs that dominate day-to-day operation are:

- **Routing accuracy.** Verifying that work items reach the right tier, agent, or skill.
- **Memory pruning.** Removing outdated notes, expired references, and superseded rules.
- **Scheduled work review.** Checking that recurring jobs still match the current operating context.
- **Skill updating.** Refreshing skill content when underlying tools or assumptions change.
- **Tool reconciliation.** Re-mapping Alice's abstract terms to changes in your tool's mechanisms.
- **Agent-behavior testing.** Spot-checking that downstream agents follow the rules and templates you wrote.
- **Op-guard conversion and deduplication.** Turning repeated failures into durable rules and merging or retiring rules that overlap.

The 45–90 minute figure covers only the first artifact. The ongoing-cost items above are what determine whether Alice remains useful after week one.

### How the pieces fit together

The diagram below is the system Alice is describing. Each box names one decision an operator must make; the table below the diagram points each box to the canonical methodology file that walks the trade-offs. The shape is fixed; the specific skills, agents, boards, and rules you instantiate at each box are yours to choose.

```
                       Inputs (X, articles, podcasts, conversations)
                                   │
                                   ▼
                       Inbox and routing
                                   │
                                   ▼
                       Work tracker
                                   │
                                   ▼
                       Orchestrator
                       ┌──────────┼──────────┬──────────┐
                       │          │          │          │
                       ▼          ▼          ▼          ▼
                      memory   knowledge   skills   downstream
                               vault                 agents
                                   │
                                   ▼
                       Verification
                                   │
                                   ▼
                       Failure capture
                                   │
                                   ▼
                       Operational guards and iteration
```

| Box | Canonical methodology file |
| --- | --- |
| Inputs | `methodology/09-inbox-from-external-sources.md` |
| Inbox and routing | `methodology/08-inbox-route.md` |
| Work tracker | `methodology/04a-decide-work-graph.md` |
| Orchestrator | `methodology/03-decide-agents.md` |
| memory | `methodology/01a-decide-memory.md` |
| knowledge vault | `methodology/01-decide-vault-tier.md` |
| skills | `methodology/02-decide-skills.md` |
| downstream agents | `methodology/03-decide-agents.md` |
| Verification | `methodology/04a-decide-work-graph.md` (verifier-gate pattern) |
| Failure capture | `methodology/06-iteration-loop.md` (detect → surface → act) and `methodology/06a-decide-retro-v2.md` |
| Operational guards and iteration | `methodology/05-op-guards.md` and `methodology/06-iteration-loop.md` |

The flow reads top-to-bottom: an external observation enters the system, is routed to a tier, becomes a tracked work item, is dispatched to an orchestrator that uses persistent knowledge, follows a procedure, and may spawn downstream agents. The output is verified independently; if it fails, the failure is captured as evidence; recurring failures are converted into operational guards that close the loop. The boxes are not stages in a pipeline that must always execute in order — they are the components a healthy system has, the relationships between them, and the order in which evidence propagates when something goes wrong.

### What Alice cannot verify

Alice is a methodology; methodologies do not verify that an implementation actually follows them. A reviewer who scores Alice 5/10 on testability is observing a real category limit, not a defect to fix inside the method: the verifier is the operator's tooling (the orchestrator, the cron surfaces, the op-guard loop), not Alice. Concretely, Alice does not and cannot check that any individual agent follows the routing rules, that the memory policy actually reduces retrieval errors, that the right skill is selected for a given task, that downstream agents receive the intended context, that scheduled work executes correctly in the operator's environment, or that operational guards prevent the recurrence of the failure they were written to address. These checks live in the operator's runtime, not in the documentation that describes the method.

## 2. Is Alice for me?

A 3-layer navigation aid (HARNESS / LOOP / GRAPH) is also available at `methodology/00-decide-ticket-naming.md` — useful for newcomers tracking the 2026-mid-year public discourse on layered agent systems, but **Alice's canonical schema is the AREA taxonomy** (decision-ordered, in `METHODOLOGY.md` and the `methodology/` directory). The diagram is a reading-onramp, not a replacement for the AREA decision-order.

Alice may fit if you:

- already use an agent with persistent context or file access;
- want a repeatable way to decide what belongs in memory, a skill, an agent, a scheduled job, or a rule;
- maintain a personal or small-team knowledge base;
- want to turn recurring work and observed failures into a system you can improve;
- prefer a methodology you can adapt over a turnkey product tied to one vendor.

Alice is probably not the right fit if you:

- want a package that installs and configures an agent system automatically;
- do not yet have a basic agent session or equivalent workflow running;
- need a framework optimized for one specific commercial product;
- want to copy another operator's private boards, agents, paths, or rules unchanged.

## 3. Getting started

The path below leads to one concrete result: a first, context-specific operating artifact based on an Alice template. You will also identify the next methodology decision to make and map Alice's abstract terms to your own tool.

1. **Clone or download Alice.**

   ```sh
   git clone <repository-url> alice-framework
   cd alice-framework
   ```

   Replace `<repository-url>` with the clone URL shown by the repository host. If you downloaded an archive instead, extract it and open the resulting `alice-framework` directory.

   **Expected observation:** the directory contains `README.md`, `METHODOLOGY.md`, a `methodology/` folder, a `templates/` folder, a `references/` folder, and a `worked-examples/` folder.

2. **Read the linear walkthrough.**

   Open [METHODOLOGY.md](METHODOLOGY.md). It introduces the decisions in their recommended order and links to the detailed methodology documents.

   **Expected observation:** you can identify the first decision that applies to your current system—for example, memory tiers, reusable skills, agent roles, scheduled work, or work tracking.

3. **Walk the first detailed decision.**

   Read [Methodology 01 — Decide your vault tier](methodology/01-decide-vault-tier.md). Answer its five questions about where unfinished ideas, external sources, finished notes, indexes, and archived material should live.

   **Expected observation:** you have a rough tier design with an access rule for each tier, even if your design uses a different number of tiers than Alice's default.

4. **Fill one template with your context.**

   Copy [templates/AGENTS.md.template](templates/AGENTS.md.template) into your own workspace, rename it as appropriate for your tool, and replace the bracketed prompts in at least these sections:

   - Source of truth
   - Routing tiers
   - Retrieval
   - End-to-end verification

   Keep the result in your own workspace rather than editing Alice's template in place.

   **Expected observation:** you have a new Markdown file that names your source of truth, your initial routing rules, and at least one way to verify the system's behavior.

5. **Map the method to your tool.**

   Open the [tool mapping guide](references/tool-mapping-guide.md). Find your tool, or use the abstract concepts in the first column to identify equivalent files, commands, or workflows in a tool not yet listed. Record those mappings in the artifact you created in step 4.

   **Expected observation:** your artifact uses paths and mechanisms that exist in your environment; it does not require Alice's author's private setup.

### Verify your first pass

Your first pass is complete when you can answer all four questions:

- Where is your new operating artifact stored?
- What source of truth and routing rules does it name?
- Which Alice methodology decision will you work through next?
- How does at least one Alice concept map to your actual tool?

If one answer is missing, return to the corresponding getting-started step. For terminology that may be easy to conflate—**orchestrator**, **downstream agent**, **spawned sub-agent**, and **default profile**—start with the [agent typology glossary](methodology/03-decide-agents.md#part-12-agent-typology-glossary-orchestrator-downstream-agent-spawned-sub-agent-default-profile). The canonical public glossary lives at [references/glossary.md](references/glossary.md); a glossary entry never replaces an inline definition.

## 4. Repository map

The repository is organized around the distinction between **methodology** and **worked example**: the documents teach a reusable decision process, while the worked-example skeletons illustrate what that process can produce for one fictionalized context. The four top-level directories below hold that distinction in different forms.

### [`methodology/`](methodology/)

This directory holds the detailed decision processes that the framework is built on. Each file names one system-design decision—vault tiers, memory, skills, agents, scheduled work, work routing, operational guards, iteration, council method, and inbox routing—and walks the trade-offs an operator must make before recording a choice in their own artifact. Read these in the order suggested by [METHODOLOGY.md](METHODOLOGY.md): start with [01-decide-vault-tier.md](methodology/01-decide-vault-tier.md) and [01a-decide-memory.md](methodology/01a-decide-memory.md) to settle where knowledge lives, then [02-decide-skills.md](methodology/02-decide-skills.md), [03-decide-agents.md](methodology/03-decide-agents.md), and [04-decide-crons.md](methodology/04-decide-crons.md) to settle who does recurring work and on what cadence, and finally [05-op-guards.md](methodology/05-op-guards.md) and the iteration and inbox files to close the loop.

### [`templates/`](templates/)

This directory holds fillable operating artifacts—Markdown files with bracketed prompts an operator copies into their own workspace and rewrites for their context. Reach for a template when you have already worked through the relevant methodology decision and are ready to record the resulting choice in a form your orchestrator or downstream agents can read. Each template pairs with one or more methodology files; for example, [templates/AGENTS.md.template](templates/AGENTS.md.template) and [templates/agent-soul.md.template](templates/agent-soul.md.template) instantiate the agent decisions from `methodology/03-decide-agents.md`, while [templates/op-guard.md.template](templates/op-guard.md.template) pairs with `methodology/05-op-guards.md`. Do not edit Alice's templates in place; copy them into your own workspace and rewrite the prompts there.

### [`references/`](references/)

This directory holds supporting documents that complement the methodology without being decisions in their own right. The two most-used files are [references/glossary.md](references/glossary.md), which keeps the canonical orchestrator / downstream agent / spawned sub-agent / default profile typology stable across the corpus, and [references/tool-mapping-guide.md](references/tool-mapping-guide.md), which translates Alice's abstract terms into mechanisms in specific tools. Other files cover board routing, a tier-routing cheatsheet, kanban-lite disciplines, and the external-ingestion pipeline. Treat references as something you consult after reading the relevant methodology file and before filling a template; they make the abstract terms operational.

### [`worked-examples/`](worked-examples/)

This directory holds concrete instances that show the method applied to a fictionalized context. The current examples are [`worked-examples/01-solo-founder-skeleton/`](worked-examples/01-solo-founder-skeleton/) and [`worked-examples/02-research-analyst-skeleton/`](worked-examples/02-research-analyst-skeleton/), each with an `AGENTS.md` and a `methodology-notes.md` that records the choices the example made and why. Use a worked example as a reference for shape—how a finished artifact can look, how a methodology decision can resolve in a particular operator's context—but do not copy it into your own workspace. Worked examples illustrate the method; they are not normative defaults.

The documents describe a **methodology**—a reusable decision process. The worked examples are **instances**—illustrations of what that process can produce for a particular fictionalized context. Treat examples as evidence and inspiration, not defaults.

## 5. License and citation

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

Alice is available under the [MIT License](LICENSE). The full text is in the [`LICENSE`](LICENSE) file at the root of this repository. SPDX identifier: `MIT`.

Canonical citation metadata lives in [CITATION.cff](CITATION.cff). If you use Alice in academic work, in a methodology you publish, or as a framework you cite, please cite it using the metadata in that file — it is the authoritative source for title, author, version, release date, and license.

If you cannot read the CFF directly, your repository host's **Cite this repository** button (GitHub, GitLab, and Codeberg all surface one) renders it into APA, BibTeX, and other formats automatically. The open-source [`cffconvert`](https://pypi.org/project/cffconvert/) tool does the same offline.

### Rendered citation (v0.1.0, 2026-08-04)

The block below is rendered from `CITATION.cff` and is kept in sync with that file. It is a fallback for contexts where the CFF cannot be read directly; the CFF remains the source of truth.

**BibTeX**

```bibtex
@software{alice_0_1_0,
  title        = {Alice — A framework for deriving your own agent-augmented operating system},
  author       = {{042Trix}},
  year         = {2026},
  version      = {0.1.0},
  date-released = {2026-08-04},
  license      = {MIT},
  url          = {[added at tag time; see CITATION.cff]},
  note         = {Public repository URL and Zenodo DOI are added at public tag time; see CHANGELOG.md for the release-readiness checklist.}
}
```

**APA-style (approximate)**

> 042Trix. (2026). *Alice — A framework for deriving your own agent-augmented operating system* (Version 0.1.0) [Computer software]. MIT License. [Public URL added at tag time; see CITATION.cff.]

**Plain text**

> Alice — A framework for deriving your own agent-augmented operating system, v0.1.0, released 2026-08-04 by 042Trix. Available under the MIT License. Canonical metadata: `CITATION.cff`.

### Tag-time fields

Two fields remain deliberately empty in `CITATION.cff` until the public repository and `v0.1.0` tag exist: the public `repository-code` URL and the Zenodo DOI. Both are added by the release card that signs the tag, and are not invented from internal context. The inline fallback above marks these fields with `[added at tag time; see CITATION.cff]` rather than guessing values; the CFF itself stays blank in those fields until the release card fills them in.

Version history is in [CHANGELOG.md](CHANGELOG.md).

## 6. Contributing

Bug reports, documentation corrections, reusable methodology proposals, templates, references, and worked-example skeletons are welcome. Contributions must teach a method rather than expose or prescribe one operator's private system.

See [CONTRIBUTING.md](CONTRIBUTING.md) for issue guidance, the method-not-instance checklist, and pull request conventions. By participating, you agree to the [Contributor Covenant Code of Conduct](CODE_OF_CONDUCT.md).