---
id: alice-methodology-session-handoff
created: 2026-08-08T14:45:00Z
updated: 2026-08-08T20:44:00Z
title: "Methodology — Decide session handoff: when, where, format, and read-side bootstrap"
type: methodology
status: active
source: alice-framework
version: 0.1.0
tags: [kind:methodology, kind:session-handoff, kind:state-handoff, project:alice]
confidence: 0.9
links: ["[[methodology/M-decide-council-sizing.md]]", "[[methodology/M-decide-human-digest.md]]", "[[methodology/M-decide-x-article-review-flow.md]]", "[[_inbox/M-decide-spec-first-flow.md]]", "[[methodology/03b-decide-operator-agent-interaction.md]]", "[[methodology/06a-decide-retro-v2.md]]"]
---

# Methodology — Decide session handoff

> A session handoff is a state-handoff, not a context-handoff: it preserves the next actionable state so the next session can continue without re-asking for context.

## Part 1: Purpose

Long sessions consume working context and can end at a compaction or session boundary before the work is naturally complete. The handoff is the formal exit artifact: it records what shipped, what remains open, the user's stance, and the first action for the next session.

The handoff is intentionally lean. It transfers state rather than the entire conversation, reasoning history, or background context. The next session has its own context and should read the handoff as a launch point, not as a transcript substitute. Durable detail belongs in the work tracker or knowledge base and is referenced from the handoff when needed.

## Part 2: When to write one

Write a handoff when the operator signals a session boundary or asks for continuity. Triggers include:

- the user says “wrap up,” “make a handoff,” or “/handoff”;
- the user says “I’m out of time” or signals that the task/session is ending;
- the user reports repeated compaction warnings or the session is long enough that context is at risk;
- the user explicitly asks to preserve state before a restart, model change, or other context boundary.

The default at a long-session boundary is **opt-in**: ask before writing, then write the handoff when the operator agrees. Do not infer permission from elapsed time, a compaction warning, an end-of-day clock, or agent convenience. A handoff is not written automatically unless the operator has separately established that automation policy.

Do not use a handoff for ordinary mid-session updates, per-tool-call narration, or work that belongs in a task record. The handoff summarizes the state; it does not replace the task record.

## Part 3: Where it goes

The canonical single-session path is:

`~/Documents/HermesVault/2-ATOMIC/cross-agent/session-handoff.md`

The fixed path lets the next-session reader find the latest handoff without additional configuration. The single shared file follows last-writer-wins semantics. When multiple agents need distinct durable handoffs, use a neighboring per-agent filename such as `handoff-<agent>-<YYYY-MM-DD>.md`; the owning agent is responsible for surfacing that file to its next session.

Historical or grandfathered handoffs are reference material unless explicitly migrated. This methodology does not silently rewrite them.

## Part 4: Schema — the five-section contract

A conforming handoff uses this exact top-level shape and section order:

```markdown
# Session handoff: <ISO date YYYY-MM-DD>

## Last action shipped
<one or more lines>

## What I was doing
<one or more lines>

## Open items (queued but not shipped)
<zero or more lines>

## User stance at handoff
<one or more lines>

## First action for the next session
<one or more lines>
```

The five headings are mandatory and machine-greppable. `Open items (queued but not shipped)` is the only section that may be empty; the other four sections must contain useful state. Free-form prose outside the five-section contract is forbidden. Use task identifiers and durable file paths as evidence anchors rather than copying the session transcript.

## Part 5: Length budget

A handoff is limited to **30–60 lines maximum**. The budget enforces the state-handoff distinction: the next session needs a compact pickup surface, not a context dump. If the state cannot fit, move background and detailed evidence into task records or durable notes and link to them from the handoff. Do not expand the handoff to preserve narrative.

The canonical writer/validator refuses an over-budget handoff rather than truncating it. The implementation's validation command is:

`python3 ~/.hermes/tools/end_session_handoff.py --validate <path>`

The methodology defines the contract; the implementation ticket owns the exact enforcement details and error text.

## Part 6: Validation gate

Validate before treating a handoff as shipped. The validator must check, at minimum:

1. the canonical five headings exist in the required order;
2. the four required non-empty sections contain useful content, while Open items may be empty;
3. the rendered handoff is within the 30–60-line budget;
4. the path resolves to the intended handoff artifact; and
5. no free-form or malformed substitute is accepted as the handoff schema.

`python3 ~/.hermes/tools/end_session_handoff.py --validate <path>` returns exit 0 for a conforming handoff and exit 1 with specific errors for a non-conforming handoff. Bypass writes, including direct `write_text` to the canonical handoff path, are forbidden. The validator is a gate, not a formatter: it must refuse invalid writes rather than silently repair or shorten them.

## Part 7: Read-side companion

The read-side companion is the next-session bootstrap. On cold start it checks the canonical handoff and handles exactly three states:

- **File missing:** surface “no prior session; ask the operator” and begin without invented state.
- **File present with the sentinel `_no_prior_session_`:** surface the same fresh-start state; the sentinel means the previous state was intentionally cleared.
- **File present and conforming:** surface a structured summary of the five sections in the first turn, including the explicit first action. Read-side consumption is read-only and must not rewrite the handoff.

A present but invalid handoff is surfaced as a validation problem, with the specific errors and a request to repair or accept the loss of state. The bootstrap does not infer missing content and does not treat historical per-agent files as the canonical single-session handoff.

## Part 8: Distinction from the human-digest format

Session handoff and human digest are sibling formats with different audiences and jobs:

| Format | Canonical job | Shape |
|---|---|---|
| Session handoff | Transfer state to the next session | Five lean, machine-greppable sections; 30–60 lines |
| Human digest | Give the operator a review and decision surface | Ten operator-facing sections, including findings, evidence, decisions, and observations |

The handoff answers “where did the work stop, and what starts next?” The human digest answers “what did the review find, and what should the operator accept, reject, or defer?” Do not merge the formats, use a digest as a handoff, or inflate a handoff with review material. Human-digest requirements are defined by `[[methodology/M-decide-human-digest.md]]`.

## Part 9: Cross-references

- `[[methodology/M-decide-council-sizing.md]]` — selects proportional review breadth; a handoff records the resulting state, not the council's full deliberation.
- `[[methodology/M-decide-human-digest.md]]` — defines the operator-facing review format that must remain distinct from state handoff.
- `[[methodology/M-decide-x-article-review-flow.md]]` — example of a multi-phase, verifier-gated flow whose open state may be handed off.
- `[[_inbox/M-decide-spec-first-flow.md]]` — current Alice spec-first contract: doc first, verifier-gated transitions, environment follows the document. This reference remains in `_inbox/` pending canonical promotion.
- `[[methodology/03b-decide-operator-agent-interaction.md]]` — operator-agent interaction design and the chat-output budget that pairs with a compact handoff.
- `[[methodology/06a-decide-retro-v2.md]]` — the retro that follows the act leg; a handoff may point to its state but does not replace the retro.

This document is method-level guidance. Any implementation-specific writer, reader, profile, board, or automation must conform to this contract through the spec-first flow, rather than changing the methodology implicitly.

## Audit-line

`## [2026-08-08T20:44Z] doc-writer-ship — reworked session-handoff methodology to satisfy the 9-section contract, opt-in write rule, read/validation order, human-digest distinction, and resolved cross-references — source=t_d6b12927; parent=t_362f89dc.`

Operator source (Matthew, 2026-08-08): “Let’s also make sure we have something in Alice about performing session handoffs.”

Signature: doc-writer
