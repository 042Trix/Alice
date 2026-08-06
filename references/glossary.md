# Glossary

Alice uses a small set of terms with precise meanings. This glossary is a quick reference; the methodology documents remain the source of truth for the decisions behind each term.

A glossary entry does not replace an inline definition. Define a task-critical term the first time it appears in a document, even when that term is listed here.

## Agent typology

Alice distinguishes four mechanisms that are often grouped under the word “agent.” The detailed typology and its design implications live in [`methodology/03-decide-agents.md`, Part 12](../methodology/03-decide-agents.md#part-12-agent-typology-glossary-orchestrator-downstream-agent-spawned-sub-agent-default-profile).

| Canonical term | What it is | Persistence | Context | Tools | Memory | Communication |
|---|---|---|---|---|---|---|
| **Orchestrator** | The main agent the operator talks to | Persistent across sessions | Full conversation history plus filtered sub-agent output | Full tool set plus filter tools | Full memory budget | DM (operator), comment (downstream agents), kanban event (system) — see [`methodology/03c-decide-agent-communication-channels.md`](../methodology/03c-decide-agent-communication-channels.md) |
| **Downstream agent** | A persistent worker profile with a defined role | Persistent across sessions | Task-scoped per work item | Profile-specific tool set | Profile-specific memory budget | Comment (orchestrator, other downstream agents on the ticket), kanban event (system) — see [`methodology/03c-decide-agent-communication-channels.md`](../methodology/03c-decide-agent-communication-channels.md) |
| **Spawned sub-agent** | An ephemeral, task-scoped instance of the orchestrator | Ephemeral for one task | Fresh, task-scoped context | Tools granted by the spawning mechanism | No persistent memory; output returns to the orchestrator | Returns results through the spawning or work-item channel |
| **Default profile** | The catch-all profile for one-off work | Ephemeral for one session | Session-scoped | Default tool set | Minimal persistent context, if any | Ticket comments and work-system events |

### Do not conflate these terms

- Do not say **sub-agent** when you mean **downstream agent**. A sub-agent is spawned for a bounded task; a downstream agent is a persistent role with its own profile.
- Do not say **agent** when the specific mechanism matters. Name the orchestrator, downstream agent, spawned sub-agent, or default profile.
- Do not say **main agent** when you mean **orchestrator**. “Main agent” is a recognized alias, but “orchestrator” states the coordination role.
- Do not say **worker** without qualification when persistence, memory, or communication matters. Several agent types can perform work.
- Do not use a tool vendor’s label as though it were an Alice term. Map the tool’s mechanism to this typology using the [tool-mapping guide](tool-mapping-guide.md).

## Method-not-instance

- **Definition:** **Method-not-instance** means documenting a reusable decision process rather than one operator’s particular setup.
- **Use:** Apply this constraint to Alice’s methodology, templates, and references so another operator can derive a system suited to their own context.
- **Six constraints:**
  1. Document the method, not the originating operator’s implementation.
  2. Keep the method tool-agnostic; put necessary product mappings in the tool-mapping guide.
  3. Keep drafts local until the operator approves publication.
  4. Use the operator-chosen project name rather than a tool-derived name.
  5. Disambiguate terms that collide across documents.
  6. Integrate a discovered documentation gap in the same work cycle instead of merely logging it for later.
- **Avoid:** **Snapshot**, **copy this setup**, and **reference implementation** when they imply that a reader should reproduce a specific operator’s boards, profiles, paths, or routing rules. A worked example may illustrate the method, but it is not normative.
- **Related terms:** **Tool-agnostic**, **worked example**, **methodology**, **instance**.

## Skill, rule, cron, and agent

| Canonical term | Definition | Use | Avoid | Methodology |
|---|---|---|---|---|
| **Skill** | A packaged, repeatable procedure an agent can invoke. | Use for bounded work that recurs, can be repeated, and has a verifiable result. | Do not use **skill** for a one-off prompt, a task, a person, or an unbounded workflow. | [`02-decide-skills.md`](../methodology/02-decide-skills.md) |
| **Agent** | A persistent worker profile with a defined role, domain, tools, and operating rules. | Use when a stable role recurs often enough to justify its own persistent context. Qualify the term using the agent typology when the mechanism matters. | Do not use **agent** as a synonym for a procedure, schedule, chat lane, or every ephemeral execution. | [`03-decide-agents.md`](../methodology/03-decide-agents.md) |
| **Cron** | A recurring task triggered on a fixed schedule. | Use when cadence, cost, and output are bounded and the task has a complete detect → surface → act path. | Do not use **cron** for an event-triggered hook, personal reminder, one-off schedule, or persistent worker. | [`04-decide-crons.md`](../methodology/04-decide-crons.md) |
| **Strike rule** | A constraint derived from repeated failures of the same shape. | Use to prevent a known behavioral failure from recurring. | Do not use **rule** for a procedure, preference, speculative risk, or single isolated failure. | [`05-strike-rules.md`](../methodology/05-strike-rules.md) |

## Aliases and deprecated forms

| Form | Status | Use instead | Reason |
|---|---|---|---|
| **Main agent** | Alias | **Orchestrator** | The canonical term names the coordination role. |
| **Agent profile** | Contextual alias | **Downstream agent**, **orchestrator**, or **default profile** | A profile describes configuration, not which role the mechanism plays. |
| **Persistent worker** | Descriptive alias | **Downstream agent** when discussing the typology | The canonical term distinguishes it from ephemeral workers. |
| **Spawned agent**, **spawned instance** | Alias | **Spawned sub-agent** | Use one form consistently across the documentation. |
| **Subagent**, **sub agent** | Deprecated spelling | **Sub-agent**; preferably **spawned sub-agent** | The qualified form prevents confusion with a downstream agent. |
| **Default agent** | Deprecated | **Default profile** | The catch-all is a profile used for one-offs, not a separate agent category. |
| **Scheduled agent** | Deprecated unless literally accurate | **Cron** for the schedule; name the invoked agent separately | A schedule and a worker are different mechanisms. |
| **Rule** when referring to a repeated-failure constraint | Ambiguous shorthand | **Strike rule** | The qualifier distinguishes governance constraints from general instructions. |
| **Snapshot** as a synonym for Alice | Deprecated | **Methodology** or **framework** | Alice teaches a derivation method rather than prescribing one implementation. |

## See also

- [`methodology/03-decide-agents.md`](../methodology/03-decide-agents.md) — authoritative agent-selection method and full four-part typology.
- [`references/tool-mapping-guide.md`](tool-mapping-guide.md) — maps Alice’s abstract terms to mechanisms in specific tools.
- [`METHODOLOGY.md`](../METHODOLOGY.md) — linear walkthrough of the complete framework.
