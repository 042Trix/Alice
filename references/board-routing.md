---
id: alice-reference-board-routing
created: 2026-08-04T12:30:00Z
updated: 2026-08-05T00:00:00Z
title: "Board routing — where to file tickets for project work"
type: reference
status: draft
source: alice-framework
tags: [kind:reference, kind:board-routing, project:alice]
confidence: 0.0
links: ["[[methodology/04b-decide-board-routing.md]]", "[[templates/AGENTS.md.template]]", "[[references/tool-mapping-guide.md]]"]
---

# Board routing — where to file tickets for project work

> Alice is **a project, not infrastructure.** The routing decision for "where does my project work go?" is one of the first decisions a friend makes when applying Alice. The methodology says "design your routing system for your context"; this reference says "here's the canonical routing for project work specifically."

## The 3-board model for project work

| Board | What goes here | Why |
|---|---|---|
| **`[your-project]`** | **Project repo work**: methodology docs, templates, references, worked-examples, OSS prep, polish, glossary, link audit, packaging | The board name matches the repo and the default workdir points to the repo root, so a worker spawned here starts in the right place. |
| **`[your-tool]-infrastructure`** (or `<vendor>-infra`) | **Tool scaffolding work**: skills, crons, vault, kanban, profiles, model swaps, agent lane management | This is work on the tool that hosts your project. The tool's default board handles it. Concrete tool names (Hermes, Claude Code, Codex, OpenCode) map to this row from `tool-mapping-guide.md`. |
| **`default`** | **Cross-cutting one-offs** that don't fit either of the above | Catch-all. Use sparingly; most project work belongs on the project board. |

**The friend-portable rule:** "If the work touches the project repo, file on the project-named board. If the work touches the tool's infrastructure, file on the tool's infrastructure board. If the work is neither, file on `default`."

## Why this matters

- **Env-var isolation.** A worker spawned on board A cannot see tasks on board B. Misfiling strands the work — the worker can't find the ticket, and the dispatcher loses the routing signal.
- **Workspace alignment.** The project board's default workdir is the repo root, so the worker starts in the right place. Filing project work on `default` (which has a different default workdir) means the worker starts elsewhere and has to navigate.
- **Audit clarity.** `[your-project]/t_X` reads as project repo work; `default/t_X` reads as catch-all. Scanning the queue by board quickly tells you what's relevant.
- **Cross-team portability.** When you apply Alice to a different project, you don't have a fixed project-named board, you have `[your-project]`. The pattern: **project-named boards for project work, infra boards for infra, default for catch-all.**

## The 5-question routing test (friend-portable)

When filing a ticket, ask:

1. **Does this work touch the project repo's files?** → Yes → `[your-project]` board
2. **Does this work touch the tool's skills, crons, vault, profiles, or kanban infra?** → Yes → `[your-tool]-infrastructure` board
3. **Is this work a one-off that doesn't fit either?** → Yes → `default` board
4. **Is the work a card in the project's OSS prep plan?** → Yes → `[your-project]` board (or whichever project board matches)
5. **Is the work a new agent profile / model swap?** → Yes → `[your-tool]-agent-profiles` board (or the tool's profile/resources board — whichever hosts agent scaffolding)

If you answered "`[your-project]`" for any of these, that's the right board.

## Common routing mistakes

- **Filing project OSS prep on the tool infrastructure board.** OSS prep is project repo work, not agent-profile work. The tool infrastructure board is for **scaffolding new agent profiles, model swaps, and agent souls** (see `templates/agent-soul.md.template`). Filing project docs work there strands the worker (env-var isolation) and confuses the queue.
- **Filing project docs work on `default`.** `default` is the catch-all, and project work is repo-specific. Filing on `default` means the worker starts in the `default` board's workdir instead of the project repo, and the queue becomes hard to scan.
- **Filing tool infra on `[your-project]`.** Symmetric mistake. Tool skills, crons, and profiles belong on the tool's infra board — they are not project work, even if the project references them.
- **Creating a new board for every sub-project.** `[your-project]-oss-prep` is the same project as `[your-project]`. One board per project; one project per board.

## What the friend does

1. **Create the project board** if it doesn't exist. Name it for the repo (substituting `[your-project]` with the friend's project name). Tool-specific CLI commands for board creation live in `references/tool-mapping-guide.md` (see the *Kanban / tickets* row for the canonical CLI invocation per tool).
2. **Set the board's default workdir** to the repo root, using whichever command your tool exposes for setting a board's default workspace.
3. **Document the routing rule** in the project's `AGENTS.md` (or equivalent cold-start schema). Future work in the project lands on the project board by default.
4. **Migrate any in-flight work** that was misfiled. Per the example, a chat-side agent should not modify tickets already filed (per the operator's standing rule), but a follow-up cleanup ticket can be filed to migrate or close them.

## Worked example (skeleton)

The 2 worked examples (solo-founder, research-analyst) show the routing applied to a specific instance. The examples are skeletons, not snapshots.

The friend reads the methodology, then applies it to their own context. The friend does NOT copy the worked example's board names; they apply the method to derive their own.

## Anti-patterns to watch for

1. **Filing all work on `default`.** → The queue becomes un-scannable; misfiling is invisible.
2. **Creating a new board for every micro-project.** → Board proliferation; the friend can't find their way.
3. **Filing project work on the tool infrastructure board because related skill work lived there.** → The skills are done; project work is repo work, not skill work.
4. **Filing tool infra on a project board because "the project uses the tool."** → Tool infra is the tool's responsibility, not the project's.
5. **Filing operator-only requests on a project board.** → Operator-action items go on `default` (or the operator's lane). Project boards are for the project's work.

## Touch-points with other methodology docs

- `methodology/04b-decide-board-routing.md` — the general board-routing methodology (5-question test, keyword-routing table pattern, when to add a board)
- `templates/AGENTS.md.template` — the cold-start schema; section 7 documents the project-specific routing rule
- `references/tool-mapping-guide.md` — how Alice's abstract boards map to specific tools (Hermes, Claude Code, etc.); includes the per-tool CLI for board creation under the *Kanban / tickets* row
