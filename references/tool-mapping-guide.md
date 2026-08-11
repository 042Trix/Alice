---
id: alice-reference-tool-mapping
created: 2026-08-04T12:30:00Z
updated: 2026-08-11T23:30:00Z
title: "Tool mapping guide — Alice's abstract concepts in your tool"
type: reference
status: draft
source: alice-framework
tags: [kind:reference, kind:tool-mapping, project:alice]
confidence: 0.0
links: []
teaching-example: true
---

# Tool mapping guide

> Alice is tool-agnostic. This reference tells you how Alice's abstract concepts map to specific tools (Hermes, Claude Code, Codex, OpenCode, etc.). The mapping is **illustrative, not exhaustive** — your tool may have different names for the same concepts.

## Core concepts and their tool mappings

The table shows the canonical path for each concept in the column's tool. The same `<vault-root>/{tier-folders}/` shape applies to all tools; the tier folder names (`<vault-root>/0-inbox/`, `<vault-root>/1-literature/`, `<vault-root>/2-atomic/`, `<vault-root>/3-indexes/`, `<vault-root>/4-archive/`) follow the lowercase convention from methodology `01-decide-vault-tier.md`. The audit log path is your vault root's `log.md` (operator-chosen location).

| Alice concept | Hermes | Claude Code | Codex | OpenCode |
|---|---|---|---|---|
| **Vault tier (4-tier)** | `<vault-root>/{0-inbox,1-literature,2-atomic,3-indexes,4-archive}` | workspace folder + CLAUDE.md scope | workspace folder + AGENTS.md scope | workspace folder + AGENTS.md scope |
| **Skill** | `~/.hermes/skills/<name>/SKILL.md` | `.claude/skills/<name>/SKILL.md` | `~/.codex/skills/<name>/SKILL.md` | `~/.opencode/skills/<name>/SKILL.md` |
| **Agent profile** | `~/.hermes/profiles/<name>/` | `.claude/agents/<name>/` | `~/.codex/agents/<name>/` | `~/.opencode/agents/<name>/` |
| **Cron** | `~/.hermes/cron/jobs.json` (hermes cron add) | system-level cron + wrapper | system-level cron + wrapper | system-level cron + wrapper |
|| **Kanban / tickets** | `~/.hermes/kanban/boards/<board>/kanban.db` (see [Board creation](#board-creation)) | linear issues (see [Board creation](#board-creation)) | linear issues (see [Board creation](#board-creation)) | linear issues (see [Board creation](#board-creation)) |
| **Operational guard** | `<atomic-tier>/rules/op-guard-N-name.md` | `<atomic-tier>/rules/op-guard-N-name.md` | `<atomic-tier>/rules/op-guard-N-name.md` | `<atomic-tier>/rules/op-guard-N-name.md` |
| **Council output** | `<atomic-tier>/cross-agent/YYYY-MM-DD_council-<topic>.md` | `<atomic-tier>/cross-agent/YYYY-MM-DD_council-<topic>.md` | `<atomic-tier>/cross-agent/YYYY-MM-DD_council-<topic>.md` | `<atomic-tier>/cross-agent/YYYY-MM-DD_council-<topic>.md` |
| **Audit log** | `<vault-root>/log.md` | per-workspace `log.md` | per-workspace `log.md` | per-workspace `log.md` |

The **abstract concepts** are what Alice teaches. The **tool mappings** are how you implement them in your specific tool. Alice doesn't care which tool you use; the methodology is the same.

### Board creation

When the operator's first-time setup needs to create the project-named board (the `[your-project]` row in `references/board-routing.md`'s 3-board model), the CLI invocation depends on the tool. This is the only place the per-tool board-creation command lives; `board-routing.md` cross-references it instead of inlining.

- **Hermes:** `hermes kanban boards create <project>-framework` (creates `~/.hermes/kanban/boards/<project>-framework/kanban.db` and registers the board). Then set the board's default workspace to the repo root via `hermes kanban boards set-workdir <project>-framework <repo-root>`.
- **Claude Code:** no native board concept — use linear issues or a markdown-backed file under `<project-root>/.linear/`. Track the project-named board as a label or saved view in linear; the "default board" maps to a default linear project.
- **Codex / OpenCode:** same as Claude Code — no native board concept, treat the linear label/saved-view mapping as the implementation.
- **Other tools:** if the tool exposes `kanban boards create <name>`, the same Hermes shape applies with the tool's prefix; if it doesn't, treat board creation as a folder + index-file scaffold per the work-graph substrate in `methodology/04a`.

The `default` board is tool-provided (Hermes ships one; Claude Code/Codex/OpenCode map to the user's default linear project). The `[your-tool]-infrastructure` board may be tool-provided (Hermes's `<profile>`-rooted `agent-resources` board) or operator-created.

## Agent typology mapping

The four agent types in [`methodology/03-decide-agents.md`, Part 12](../methodology/03-decide-agents.md#part-12-agent-typology-glossary-orchestrator-downstream-agent-spawned-sub-agent-default-profile) (orchestrator, downstream agent, spawned sub-agent, default profile) map differently in each tool. Use this table when you need to translate Alice's mechanism into your tool's vocabulary.

| Alice term | Hermes | Claude Code | Codex | OpenCode |
|---|---|---|---|---|
| **Orchestrator** | the operator-facing profile | the main chat agent | the main chat agent | the main chat agent |
| **Downstream agent** | another persistent role profile | a sub-agent with its own role instructions | a sub-agent with its own role instructions | a sub-agent with its own role instructions |
| **Spawned sub-agent** | a one-shot agent invocation | the `Task` tool (sub-agent feature) | the `subagent` tool | the `subagent` tool |
| **Default profile** | the `default` profile | the implicit default concept | the implicit default concept | the implicit default concept |

**Note:** Claude Code's "sub-agent" maps to Alice's spawned sub-agent, NOT Alice's downstream agent. See the [Claude Code caveat](#claude-code) below for the full distinction.

## When the mapping is unclear

If your tool doesn't have a clear mapping for one of Alice's concepts, that's a gap in your tool, not in Alice. Three options:

1. **Build the mapping yourself.** A "skill" can be a markdown file with a clear name and trigger conditions; have the agent load it manually.
2. **Use a different concept.** If your tool doesn't have kanban, use a flat list of todos — the methodology still applies.
3. **Switch tools.** If your tool is missing too many concepts, you may need a more capable one.

## Tool-specific caveats

### Hermes
- Caveat: the tool's structured opinions (vault tiers, kanban, etc.) may not match every operator's context. If yours doesn't fit, fight the tool's defaults.
### Claude Code

- Claude Code is a coding-focused agent. Alice's methodology applies, but Claude Code's agent model differs from Alice's multi-agent model. See `methodology/03-decide-agents.md` Part 12 for the agent typology glossary.
- **Claude Code's "sub-agent" (via the `Task` tool) is Alice's spawned sub-agent**, NOT Alice's downstream agent. Claude Code sub-agents share the orchestrator's tools and context; they don't have persistent SOULs.
- For Alice's "downstream agent" (persistent profile with its own SOUL, dispatched via kanban), Claude Code doesn't have a direct equivalent. Use the `Task` tool for spawned sub-agents (ephemeral), and linear issues or markdown lists for kanban-style ticket tracking.
- The skill system is more limited than Hermes. You can have ~20 skills before context budget is exhausted.
- No native kanban. Use linear issues or a markdown list.

### Codex
- Codex is a coding-focused agent similar to Claude Code.
- The skill system is similar to Claude Code. ~20 skill limit before context budget is exhausted.
- No native kanban. Use linear issues or a markdown list.

### OpenCode
- OpenCode is similar to Claude Code and Codex.
- The skill system is more flexible. ~50+ skills possible.
- No native kanban. Use linear issues or a markdown list.

## Cross-tool compatibility

If you switch tools, the methodology is the same; the implementation differs:

- **Skills:** the file format differs, but the structure (name, trigger, inputs, outputs, procedure) is the same.
- **Agents:** the profile format differs, but the soul (identity, boundaries, tools, skills, audit) is the same.
- **Crons:** the registry differs, but the spec (name, schedule, detect, surface, act, cost, audit) is the same.
- **Kanban:** the ticket system differs, but the lifecycle (ready → running → done) is the same.

Alice is a **methodology** for setting up an agent-augmented operating system. The methodology is portable. The implementation is tool-specific.

## What's next

- `tier-routing-cheatsheet.md` — quick reference for 4-tier routing decisions
- `kanban-lite-disciplines.md` — minimal kanban rules for a small agent fleet
- `x-ingestion-pipeline.md` — the operating spec for X post ingestion
