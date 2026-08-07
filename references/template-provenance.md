---
id: alice-reference-template-provenance
created: 2026-08-06T15:30:00Z
title: "Template provenance — derived_from frontmatter convention"
type: reference
status: draft
source: alice-framework
tags: [kind:reference, kind:provenance, kind:frontmatter, project:alice]
confidence: 0.0
links: ["[[methodology/01b-decide-vault-content.md]]", "[[templates/frontmatter-schema.md.template]]"]
---

# Template provenance — `derived_from` frontmatter convention

> When a reader copies an Alice template into their own vault, the copy loses its link to the source template and version. The `derived_from:` frontmatter convention gives the copy a durable, machine-readable pointer back to its origin so the reader can answer four questions over time: **Has the source changed? Did a newer version fix a flaw in my copy? Which sections are local customizations vs source? Can I migrate safely?**

## Why this matters at team/enterprise scale

For one operator, copying a template is invisible drift — the copy diverges from the source a few lines at a time and the operator notices only when something breaks. For a team or enterprise, every copy is a fork with no merge strategy: the source evolves, the copies don't, and six months later nobody knows which copy is canonical. `derived_from:` is the smallest intervention that lets the reader detect drift before it causes a failure. It is a convention, not enforcement — there is no required tooling, no schema validator, no migration script. The reader fills in the frontmatter when they customize a template; they leave the placeholder (or omit the field entirely) when the note is original work.

## The schema

```yaml
---
derived_from:
  project: Alice
  template: templates/<name>.md.template
  version: <semver>
  customized_on: <YYYY-MM-DD>
---
```

| Field | Type | Required? | Format | Purpose |
|---|---|---|---|---|
| `project` | string | required | project slug | the upstream methodology / template repo this template came from |
| `template` | string | required | repo-relative path | the exact source file the copy was derived from (e.g. `templates/AGENTS.md.template`) |
| `version` | string | required | semver | the version of the upstream template when the copy was made |
| `customized_on` | string | optional | YYYY-MM-DD | the date the reader filled in the field; leave blank or omit if the copy is still pristine |

**When `project` is `Alice`**, the `template` value points at an Alice file under `templates/`. When the reader is using Alice's methodology to derive a non-Alice system (a team wiki, an agent standards repo), `project` carries the reader's project name and `template` carries the reader's source path.

**`version` is semver of the upstream project**, not of the reader's copy. A reader who has customized a copy heavily may want to bump `version` locally to mark a fork point, but the convention is to leave `version` pinned to the upstream version and record local forks in `customized_on` or in a separate `changelog` field the reader defines for themselves.

## Where the field belongs in frontmatter

Add `derived_from:` as a new top-level key in the existing frontmatter block, alongside `id`, `created`, `updated`, `type`, `status`, `source`, `tags`, `confidence`, `links`. The order does not matter; convention is to place it after `source` (since both are provenance) and before `tags`. Example for a customized AGENTS.md:

```yaml
---
id: a1b2c3d
created: 2026-09-01T10:00:00Z
updated: 2026-09-15T14:30:00Z
type: atomic
status: active
source: operator
derived_from:
  project: Alice
  template: templates/AGENTS.md.template
  version: 0.1.1
  customized_on: 2026-09-01
tags: [domain:operating-system, kind:schema, project:myproject]
confidence: 0.9
links: []
---
```

## When to fill in vs when to leave the placeholder

The convention is opt-in. Filling in `derived_from:` is a deliberate act that signals "this note is a derivative of an upstream template I want to track." Leaving the field out (or leaving the placeholder values blank) is correct for original work.

| Situation | Action | Why |
|---|---|---|
| Note is original work, not derived from any template | Omit `derived_from:` entirely | The convention is for derivatives, not originals. Original notes do not need provenance metadata. |
| Note was copied from an Alice template but is still pristine (no local changes) | Add `derived_from:` with all four fields filled; set `customized_on` to the copy date | The reader wants to know when the copy diverges from upstream, which is the moment they customize, not the moment they copy. The copy date is the closest stable proxy until customization begins. |
| Note was copied and then customized | Add `derived_from:`; set `customized_on` to the date of first local edit | `customized_on` marks the fork point. The reader can compare `customized_on` to upstream release dates to see how stale the fork is. |
| Note was forked from a template the reader wrote themselves (not Alice) | Fill `project` with the reader's project name and `template` with the reader's path | The schema is generic. The Alice-specific example is illustrative; the convention works for any methodology that ships templates. |
| Note was forked from a non-template source (a Slack thread, an article, a conversation) | Use `source` instead of `derived_from:` | `source` is for one-shot provenance (where the note came from); `derived_from` is for ongoing provenance (which template this note tracks). They are different concerns. |
| Reader does not know the upstream `version` | Leave `version` blank; do not invent one | A wrong `version` is worse than no `version` — the reader will trust the wrong value and miss drift. Use `unknown` if the reader wants to mark the gap explicitly. |

## How the reader uses the field over time

Three routine uses, in increasing order of effort:

1. **On read** — the reader's tooling (or the reader themselves) checks `derived_from.template` to know which upstream template this note tracks. Reading is unchanged; the field is metadata.
2. **On upstream release** — when the upstream project releases a new version, the reader greps their vault for `derived_from: project: Alice` to find every Alice-derived note, then compares each note's `version` field against the latest release. Notes with stale `version` get reviewed for drift.
3. **On migration** — when the reader wants to merge upstream changes into their fork, `derived_from.version` and `derived_from.customized_on` together tell the reader "this fork was made from version X on date Y; upstream is now version Z; review what changed between X and Z before merging." The field does not automate the merge; it gives the reader the data to make the merge decision.

## Out of scope

- **No enforcement.** `derived_from:` is a convention. The reader's tooling is not required to validate it, query it, or block on its absence. Adding enforcement tooling is a different ticket (drift-detection / validation), not this convention.
- **No automated drift detection.** A reader who wants drift detection builds it themselves; Alice does not ship a drift scanner. The frontmatter is the data layer; tooling is the reader's responsibility.
- **No required field beyond `project`, `template`, `version`.** `customized_on` is optional. A reader who does not want to track fork dates can omit it.
- **No method-not-instance violation.** The convention describes a generic schema (any project, any template path, any semver). It does not bake in Alice's specific filenames, version numbers, or operator paths.

## Cross-references

- `methodology/01b-decide-vault-content.md` — vault content decisions (frontmatter schema, lifecycle).
- `templates/frontmatter-schema.md.template` — the frontmatter schema the reader designs for their own vault; `derived_from:` extends the optional-fields section.
- `templates/AGENTS.md.template` and 13 other templates in `templates/` — each now ships the `derived_from:` placeholder.

## Approval

- [ ] Schema is generic (any `project`, any `template` path, any semver)
- [ ] Required vs optional fields are documented
- [ ] "When to fill in vs when to leave the placeholder" is enumerated by situation
- [ ] Out-of-scope items are listed (no enforcement, no drift tooling, no method-not-instance leak)
- [ ] Cross-references to methodology and templates are present