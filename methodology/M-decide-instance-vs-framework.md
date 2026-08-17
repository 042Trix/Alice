---
id: alice-methodology-M-decide-instance-vs-framework
created: 2026-08-11T22:00:00Z
updated: 2026-08-17T10:50:00Z
title: "Methodology M — Decide framework-vs-instance: where does a new doc belong?"
type: methodology
status: draft
source: alice-framework
tags: [kind:methodology, kind:meta, kind:scope, kind:framework-vs-instance, kind:gate, kind:durable-fix, project:alice]
confidence: 0.9
version: 0.1.1
alice-ticket: t_7aa96032
companion: ["[[references/instance-leak-check-spec.md]]", "[[templates/instance-leak-check.py.template]]", "[[methodology/M-decide-alice-publish-flow.md]]", "[[methodology/M-decide-graph-audit-promotion-flow.md]]"]
links: ["[[methodology/00-decide-ticket-naming.md]]", "[[methodology/01-decide-vault-tier.md]]", "[[methodology/02-decide-skills.md]]", "[[methodology/04b-decide-board-routing.md]]", "[[methodology/05-op-guards.md]]", "[[methodology/09-inbox-from-external-sources.md]]", "[[methodology/M-decide-alice-publish-flow.md]]", "[[methodology/M-decide-graph-audit-promotion-flow.md]]"]
amended_by: ["t_7aa96032", "t_f38bd852", "t_0e7b941a"]
---

# Methodology M — Decide framework-vs-instance (v0.1.1)

> **The repo's job is to publish the framework canon. Instance operational flows belong next to the instance, not in the repo.** This methodology is the durable fix for the recurring pattern where new flows land in Alice because the publishing pipeline routes `~/Documents/alice-framework/methodology/*.md` to GitHub. Without a clear rule + an automated check, the line between "Alice framework concepts" and "Hermes-instance operational flows" blurs.

## Part 1: Why this methodology exists

Alice is published to https://github.com/042Trix/Alice as a public framework canon. The repo's job is to give a friend (operator running a different tool) a usable methodology, not to document the operator's specific instance. The two surfaces are different audiences and have different lifecycles:

| Surface | Audience | Lifecycle | Path |
|---|---|---|---|
| **Alice framework canon** | A friend adopting Alice's methodology in their own tool | Long-lived; versioned; semver | `~/Documents/alice-framework/` (published) |
| **Hermes-instance operational flows** | The operator's own dispatcher + workers | Short-lived; iterated; reversible | `~/.hermes/methodology/` (instance-local) |

**The recurring defect:** when the operator builds a new flow (e.g., x-article-review loop), the doc lands in `~/Documents/alice-framework/methodology/M-decide-*.md` because the publishing pipeline picks up everything in that directory. The next `alice-publish` run ships the instance-specific flow to GitHub as if it were Alice framework content.

**Verified case (2026-08-11, ticket `t_7aa96032`):** six `M-decide-*.md` files in `methodology/` describe Hermes-instance operational flows, not Alice framework concepts:
- `M-decide-x-article-review-flow.md` (Hermes loop flow)
- `M-decide-alice-publish-flow.md` (Hermes loop flow)
- `M-decide-council-sizing.md` (Hermes council algorithm)
- `M-decide-human-digest.md` (Hermes PDF format)
- `M-decide-navigation-aid-caption.md` (Hermes caption format)
- `M-decide-session-handoff.md` (Hermes handoff protocol)

This is the **second strike** in the session (per the three-op-guard rule `2-ATOMIC/rules/three-op-guard-rule-2026-07-20.md`). Two strikes = durable fix warranted. This methodology + the `instance-leak-check-spec.md` reference + the `instance-leak-check.py` script = the durable fix.

## Part 2: The distinction rule

**A new doc belongs in Alice (the published framework) IF AND ONLY IF it describes a tool-agnostic methodology that another operator could adopt as-is.** Otherwise, it belongs in the instance.

### Decision tree

For each new doc the operator (or doc-writer) considers adding to `~/Documents/alice-framework/`:

```
Q1. Does the doc describe a tool-agnostic methodology that another operator
    could adopt in their own tool (Hermes, Claude Code, Codex, OpenCode, etc.)?

    YES → Alice framework (A)
    NO  → Q2

Q2. Does the doc describe a specific operator-instance operational flow
    (a Hermes loop, profile, cron, skill, intent doc)?

    YES → Instance-local (H). Path: ~/.hermes/methodology/
    NO  → Q3

Q3. Does the doc describe BOTH — an abstract concept AND a concrete
    operational flow that depends on the operator's tool?

    YES → Split:
      • Alice doc = abstract concept (the friend-portable pattern)
      • Instance doc = concrete flow (the operator's binding of the pattern to Hermes)
    NO  → Q4

Q4. Is the doc purely an audit artifact, scratch, or local working note?

    YES → Local-only (X). Path: _inbox/ or delete after audit closes.
    NO  → STOP. Operator decision required; file a [HUMAN ACTION] ticket.
```

### Concrete examples

| Doc type | Goes to | Why |
|---|---|---|
| "How to name tickets" | Alice (`00-decide-ticket-naming.md`) | Tool-agnostic; another operator can adopt the AREA + topic-name format |
| "How the x.com article-review flow works" | Instance (`~/.hermes/methodology/M-decide-x-article-review-flow.md`) | The flow is specific to the operator's Hermes loop, intent doc, and dispatcher wiring |
| "How to ingest X posts into the vault" | Alice (`09-inbox-from-external-sources.md` + `references/x-ingestion-pipeline.md`) | The pattern is friend-portable; the tool binding is the operator's choice |
| "How the operator's Discord channel renders `clarify` calls" | Instance (`~/.hermes/methodology/M-decide-clarify-discord-render.md` or skill body) | Specific to the operator's Hermes gateway + Discord bot token |
| "Op-guards — what they are and how to write one" | Alice (`05-op-guards.md`) | Tool-agnostic pattern; the specific op-guard rules live in the operator's vault |
| "Specific op-guard-X-Y-Z rules" | Instance (`~/Documents/HermesVault/2-ATOMIC/rules/`) | Operator-specific rules; not Alice canon |
| "Council methodology — how to run a multi-perspective review" | Alice (`07-council-methodology.md`) | Friend-portable pattern |
| "The Hermes-specific council-sizing algorithm" | Instance (`~/.hermes/methodology/M-decide-council-sizing.md`) | Hermes-specific implementation |
| "Skill curator — maintenance cycle" | Alice (`references/skill-curator.md`) | "The role is tool-agnostic" — explicitly friend-portable |
| "Hermes-specific skill body for X" | Instance (Hermes skills dir) | Specific to a skill body, not the framework |

## Part 3: The naming convention

To make the distinction visible at a glance, Alice uses a numbering convention:

| Pattern | Lives in | Audience |
|---|---|---|
| `00-decide-*.md`, `01-decide-*.md`, ..., `09-decide-*.md` | Alice `methodology/` | Friend-portable framework |
| `M-decide-*.md` (when Alice framework) | Alice `methodology/` | Friend-portable meta methodology (e.g., spec-first flow, parity check, instance-vs-framework itself) |
| `M-decide-*.md` (when instance flow) | `~/.hermes/methodology/` | Operator-instance operational flow |

**Both Alice and the instance use the `M-decide-*.md` shape** because meta methodologies (how to make decisions about the framework) are framework content, but operator-instance flows (how the operator's loop works) are not. The distinction is the **content**, not the filename pattern. The doc-writer MUST classify the doc by walking Part 2's decision tree before assigning a path.

## Part 4: The publishing gate

**Every doc that lands in `~/Documents/alice-framework/methodology/` MUST pass the instance-leak check before publishing.** The check is codified in:

- `references/instance-leak-check-spec.md` — the canonical specification of what counts as an instance leak
- `templates/instance-leak-check.py.template` — the canonical CI script template
- `~/.hermes/tools/check_instance_leaks.py` — the instance-side implementation (the operator copies the template into the instance)

The check runs at three points:
1. **Local git pre-commit hook** — warns + blocks if the check finds errors
2. **GitHub Actions CI** — blocks PR if the check finds errors
3. **`alice-publish` loop Step 1** — runs the check before the operator's local publish to GitHub (per op-guard-19, the verify step precedes the ship)

If the check flags a doc, the operator either:
- Moves the doc to `~/.hermes/methodology/` (instance), OR
- Rewrites the doc to be friend-portable (Alice framework), OR
- Splits the doc into an Alice doc + an instance doc (per Q3)

## Part 5: The done-gate

A new doc is **durable-shipped** when ALL of:

1. ✅ The doc's path is classified per Part 2's decision tree (A or H; not U)
2. ✅ The doc's filename follows Part 3's convention
3. ✅ The doc's content passes the instance-leak check (Part 4)
4. ✅ The doc's references to other docs cite by full path (not by partial filename)
5. ✅ The CHANGELOG entry describes the doc + its classification (per op-guard-5 paired-wiki integrity)

A doc that fails any check is **rework**, not shipped.

## Part 6: Backwards compatibility

Existing docs in `~/Documents/alice-framework/methodology/` are classified per `t_7aa96032`'s audit (the file-by-file classification table in that ticket's body). The doc-writer + operator MUST execute the reorg (Part C of `t_7aa96032`) before the next alice-publish run.

**Grandfathered docs** (kept in Alice despite H-classification, with operator approval):
- None. Every H-classified doc gets moved. There are no exceptions.

**Superseded docs** (kept as historical, with explicit supersession link):
- `methodology/06a-decide-retro.md` — superseded by `06a-decide-retro-v2.md` (already has `supersedes:` in v2's frontmatter). Kept for git history; can be removed in a future cleanup once the v2 is stable for 2+ releases.

**Audit artifacts** (kept as evidence in `_inbox/`):
- `_inbox/method-not-instance-leak-scan-2026-08-04.md` — the original audit that flagged the pattern
- `_inbox/jargon-leak-scan-2026-08-04.md` — related jargon-leak audit
- `_inbox/link-audit-2026-08-04.md` — related link-rot audit
- `~/.hermes/decisions/council-logs/council-v0.1.0-GBU-review-2026-08-06.md` — council verdict input (moved from `_inbox/` on 2026-08-11 per t_275f1fbf-H, op-guard-17)
- `~/.hermes/decisions/council-logs/council-v0.1.1-build-vs-maintain-review-2026-08-06.md` — council verdict input (moved from `_inbox/` on 2026-08-11 per t_275f1fbf-H, op-guard-17)
- `~/.hermes/literature/article-verdicts/article-*-council-verdict.pdf` (2 files) — council verdict PDFs (moved from `_inbox/` on 2026-08-11 per t_275f1fbf-H, op-guard-17)

**Local scratch to delete:**
- `CHANGELOG.md.bak` — never committed; local backup from a `verify_artifact_state.py` patch
- `CITATION.cff.bak` — never committed; same
- `_inbox/_polish-archive/*` (7 files) — archived scratch from the v0.1.x polish arc

## Part 7: Cross-references

- **Companion reference:** `references/instance-leak-check-spec.md` — the canonical specification of the leak check
- **Companion template:** `templates/instance-leak-check.py.template` — the canonical script template
- **Companion rule:** `2-ATOMIC/rules/three-op-guard-rule-2026-07-20.md` — the 3-failure trigger that warranted this durable fix
- **Companion rule:** `2-ATOMIC/rules/op-guard-17-methodology-canonical-instance-conforms-2026-08-08.md` — alice-first, instance-second, compliance-gate
- **Companion rule:** `2-ATOMIC/rules/op-guard-16-spec-first-flow-2026-08-05.md` — doc-writer is spec-only; this methodology IS the doc
- **Companion rule:** `2-ATOMIC/rules/op-guard-21-chat-side-orchestrator-only-no-direct-edit-2026-08-09.md` — the chat-side files tickets; the worker executes
- **Audit ticket:** `t_7aa96032` — the file-by-file audit + the durable fix spec
- **Sibling methodology:** `methodology/05-op-guards.md` — the broader gate pattern (this methodology is a specialization for the framework-vs-instance question)
- **Sibling methodology:** `methodology/M-decide-spec-first-flow.md` (when promoted) — the 4-phase flow pattern
- **Sibling methodology:** `methodology/M-decide-parity-check.md` (when promoted) — the parity check pattern (a doc is shipped only after verifying the operator's environment actually does the thing)
- **Sibling methodology:** `methodology/M-decide-v0.1.1-defer-set.md` (when promoted) — the v0.1.1 defer-guardrail

## Audit-line

```
## [2026-08-11T22:00Z] doc-writer-ship — methodology/M-decide-instance-vs-framework.md v0.1.0 — distinction rule codifies the framework-vs-instance decision tree + the M-decide-*.md naming convention + the instance-leak check publishing gate. Source: t_7aa96032 (audit + durable fix). Companion files: references/instance-leak-check-spec.md, templates/instance-leak-check.py.template.
```
