---
id: alice-reference-instance-leak-check
created: 2026-08-11T22:15:00Z
updated: 2026-08-17T22:00:00Z
title: "Instance-leak check — specification for the framework-vs-instance CI gate"
type: reference
status: draft
source: alice-framework
tags: [kind:reference, kind:ci, kind:gate, kind:instance-leak, kind:scope, project:alice]
confidence: 0.9
alice-ticket: t_7aa96032
companion: ["[[methodology/M-decide-instance-vs-framework.md]]", "[[templates/instance-leak-check.py.template]]"]
links: ["[[methodology/M-decide-instance-vs-framework.md]]", "[[methodology/05-op-guards.md]]", "[[references/board-routing.md]]", "[[references/tool-mapping-guide.md]]"]
teaching-example: true
---

# Instance-leak check — specification for the framework-vs-instance CI gate

> **Every doc that lands in `~/Documents/alice-framework/` MUST pass this check before publishing.** The check catches the recurring pattern where Hermes-instance operational flows slip into the Alice framework repo. This reference is the canonical specification; the implementation lives in `templates/instance-leak-check.py.template` (the canonical script template) and `~/.hermes/tools/check_instance_leaks.py` (the operator's instance-side implementation).

## Why this check exists

The publishing pipeline routes everything in `~/Documents/alice-framework/methodology/*.md` to https://github.com/042Trix/Alice as the public framework canon. When an instance-specific flow (e.g., a Hermes loop flow) lands in that directory, the next publish ships it to GitHub as if it were Alice framework content. The check catches this before publishing.

The check is **automated and load-bearing** — every PR to alice-framework that fails this check is blocked. There is no manual override.

## What counts as an instance leak

The check classifies each file in the repo as one of:

- **A** — Alice framework content (keep in `~/Documents/alice-framework/`)
- **H** — Hermes-instance content (must NOT be in `~/Documents/alice-framework/`; move to `~/.hermes/`)
- **U** — Unclear (operator decision required; the check warns but does not block)
- **X** — Local artifact (must NOT be committed; delete or add to `.gitignore`)

### Pattern catalog

The check applies the following patterns to each file in the repo. A file matches an H-pattern if ANY pattern fires:

#### H1: Hermes-instance operational flow files (filename-based)

Files matching `methodology/M-decide-*.md` whose frontmatter includes:
- `kind: hermes-instance` or `kind: instance` → **ERROR** (H)
- `source: hermes-instance` or `source: hermes-council` → **ERROR** (H)
- `intent: ~/.hermes/loops/intents/*.md` → **ERROR** (H; intent doc is instance-local)
- `loop: hermes` or `loop: <loop-id>` → **ERROR** (H)

**Why:** `M-decide-*.md` files can be either Alice framework meta-methodologies OR Hermes-instance operational flows. The filename alone is ambiguous; the frontmatter distinguishes. If the frontmatter identifies an instance loop, the file is H.

#### H2: References to instance paths (content-based)

Any file whose body contains references to instance-specific paths:
- `~/.hermes/profiles/<name>/` (jarvis, coder, planner, etc.) → **ERROR** (H)
- `~/.hermes/loops/intents/<instance-only>.md` (e.g., x-article-review-intent) → **ERROR** (H)
- `~/.hermes/cron/jobs.json` → **ERROR** (H)
- `~/Documents/HermesVault/2-ATOMIC/rules/<specific-op-guard>.md` (when the doc is shipping the rule, not referencing it as a companion) → **WARNING** (cross-reference to operator-specific rule; should be genericized or moved to instance)

**Why:** Alice framework docs reference concepts, not specific operator paths. A doc that names "jarvis", "coder", or "hermes-loops" is documenting the operator's instance, not the framework.

#### H3: Hermes-specific profile names without genericizing

Any file that mentions specific Hermes profile names (`jarvis`, `coder`, `planner`, `verifier`, `doc-writer`, `business-analyst`, `council`, `marketing`, `msaa-scout`, `web-research`, `default`) without also providing the tool-agnostic abstract concept:
- Profile name appears 3+ times in body without a `<concept>` gloss → **WARNING** (likely H)
- Profile name appears 1-2 times → **INFO** (cross-reference is fine; e.g., "per the op-guard-X rule, the doc-writer role...")

**Why:** A passing reference is fine (companion rule, role name). A heavy reliance on profile names is instance-specific.

#### H4: Tool-specific CLI invocations

Any file that documents tool-specific CLI invocations:
- `hermes kanban ...`, `hermes cron ...`, `hermes profile ...` → **WARNING** (probably belongs in instance or should genericize to "your tool's CLI")
- Specific tool version pins (e.g., "Hermes v0.3.0") → **WARNING** (Alice framework is tool-agnostic)

**Why:** Alice is tool-agnostic. Specific CLI commands are operational, not methodological.

#### H5: Frontmatter with operator-instance tags

Files whose frontmatter includes:
- `tenant: <operator-instance>` → **ERROR** (H)
- `board: hermes` or `board: <instance-board>` → **ERROR** (H)
- `assignee: <operator-profile>` → **ERROR** (H)

**Why:** Alice framework docs don't carry tenant/board/assignee metadata — those are kanban-DB-specific, not framework.

#### X1: Local artifacts (filename-based)

Files matching:
- `*.bak` (anywhere in the repo) → **ERROR** (X; delete)
- `.DS_Store`, `*.swp`, `*.swo`, `*~`, `*.pyc`, `__pycache__/` → **ERROR** (X; add to `.gitignore` if not already)
- `_inbox/_polish-archive/*` → **ERROR** (X; archived scratch, delete)
- `_inbox/scribe-*`, `_inbox/polish-*`, `_inbox/voice-*`, `_inbox/final-*` (the polish-arc scratch) → **WARNING** (U; operator decides if any contain durable content; default = delete)

**Why:** The repo's `.gitignore` already excludes `_inbox/`, `_polish/`, and `*.bak`, but operators sometimes bypass git via `write_file`. The check catches these as a backstop.

#### U: Unclear cases

Files that don't match H or X patterns but contain:
- Generic operational language without specifying tool → **INFO** (cross-reference only)
- Cross-references to operator-specific paths but the doc itself is friend-portable → **INFO** (cross-reference is acceptable)

## False-positive filter

The check classifies every file by content patterns. Teaching-example docs that legitimately reference instance ops in body prose (e.g., the CHANGELOG when it documents historical changes, or a methodology that teaches the framework-vs-instance distinction by citing the instance side) would otherwise fire false-positive ERRORs. Two frontmatter shapes suppress those:

### `teaching-example: true`

Opts the file out of H2, H3, and H4 (the content patterns). Use this when the file is friend-portable framework content that legitimately references instance paths, profile names, or tool CLI invocations in body prose. The H1 / H5 / X1 patterns still fire (those are structural and indicate a real leak).

```yaml
---
id: alice-methodology-00-decide-ticket-naming
title: "Methodology 00 — Decide ticket naming"
source: alice-framework
teaching-example: true
---
```

### `instance-leak-exceptions: [H2, H4]`

Finer-grained opt-out: only suppresses the listed pattern IDs. Accepts a YAML list or a flat string. Use this when a file should remain visible to one pattern but not others.

```yaml
---
id: alice-template-with-h4-example
title: "Template demonstrating H4 CLI usage"
source: alice-framework
instance-leak-exceptions: [H4]
---
```

Flat-string form is also accepted: `instance-leak-exceptions: "[H2, H4]"` (the YAML-lite parser treats inline lists as strings; the script splits on comma/whitespace and strips brackets).

### Why `teaching-example: true` does NOT suppress H1 / H5

H1 (M-decide frontmatter `kind`/`source`/`intent`/`loop`) is structural — a file with `kind: hermes-instance` is genuinely a leak, not a teaching example. H5 (operator-instance `tenant`/`board`/`assignee`) is also structural. X1 (filename patterns for `*.bak` and OS cruft) is filename-based and unaffected by frontmatter. The four exceptionable pattern IDs are {H2, H3, H4}.

## Deliberate-fixture opt-out (`--allow-deliberate-fixtures`)

Some M-decide files are **deliberate fixtures** — they intentionally trigger the H1 ERROR pattern so the regression suite (`~/.hermes/tests/test_check_instance_leaks.py`) has a real on-disk target to exercise end-to-end. Per `kanban:t_764d71c9`, the fixtures MUST stay on disk and MUST stay ERROR-flagged when checked without the opt-out flag; they cannot be removed or fixed because the regression suite depends on them.

To avoid blocking every commit on the fixtures (which would force operators to `--no-verify` on every commit), the canonical opt-out is the **`--allow-deliberate-fixtures`** flag:

```bash
python3 ~/.hermes/tools/check_instance_leaks.py ~/Documents/alice-framework/ --allow-deliberate-fixtures
```

When set, the flag downgrades findings for files matching the **canonical fixture pattern** from `ERROR` → `INFO` (with a `[deliberate-fixture]` message prefix). All three conditions must hold:

1. `file_path.name` starts with `M-decide-` AND parent is `methodology/`
2. Frontmatter `kind` ∈ {`hermes-instance`, `instance`}
3. Frontmatter `tags` contains BOTH `kind:fixture` AND `leak:deliberate` (list form OR inline-bracket form)

```yaml
---
type: methodology
id: M-decide-h1-leak
version: 0.0.1
kind: hermes-instance
source: hermes-instance
tags: [domain:test, kind:fixture, leak:deliberate]
---
```

### When to use this flag

- **`~/.hermes/hooks/instance-leak-pre-commit`** always passes this flag. The hook is wired to the canonical opt-out surface so the fixtures don't block every commit.
- **Manual local runs** (`hermes-cli`-style dev loop): pass the flag when checking a tree that contains the fixtures.
- **`alice-publish` loop Step 1** (the publish gate) does NOT pass the flag. The publish gate enforces a stricter contract — if a real H1 leak has shipped into the framework repo, the publish must block. Operators can override the publish gate manually with `--allow-deliberate-fixtures` for the same reason the local pre-commit hook does.
- **GitHub Actions CI**: same as `alice-publish` Step 1 — does NOT pass the flag by default.

### What the flag does NOT do

- It does NOT remove the fixtures from the on-disk tree. They are still the regression suite's target.
- It does NOT change the default behavior of the check. Without the flag, the fixtures still fire `ERROR` (regression suite's contract per `t_764d71c9`).
- It does NOT suppress genuine H1 leaks. A file matching the H1 pattern structurally (M-decide-* + kind: hermes-instance) but lacking the canonical fixture tags (`kind:fixture` + `leak:deliberate`) still fires `ERROR` regardless of the flag.
- It does NOT bypass the gate for H2/H3/H4/H5/X1 — those patterns have their own opt-out surfaces (`teaching-example: true` or `instance-leak-exceptions`) and are not affected by this flag.

## Check output format


```
$ python3 ~/.hermes/tools/check_instance_leaks.py ~/Documents/alice-framework/

INSTANCE-LEAK CHECK — ~/Documents/alice-framework/
==================================================

[ERROR] methodology/M-decide-x-article-review-flow.md
  H1: intent: ~/.hermes/loops/intents/x-article-review-intent.md (instance loop intent)
  H2: references ~/.hermes/profiles/coder/ (3 occurrences)
  → Action: MOVE to ~/.hermes/methodology/

[ERROR] methodology/M-decide-alice-publish-flow.md
  H1: intent: ~/.hermes/loops/intents/alice-publish-intent.md
  → Action: MOVE to ~/.hermes/methodology/

[WARNING] methodology/06a-decide-retro-v2.md
  H3: "doc-writer" appears 4 times (likely operator-role-specific; consider genericizing)
  → Action: REVIEW (operator decides: rewrite or accept as framework)

[INFO] methodology/M-decide-instance-vs-framework.md
  Pass: classification matches path; no instance-specific patterns detected.

[ERROR] CHANGELOG.md.bak
  X1: *.bak (local artifact)
  → Action: DELETE

Summary: 6 ERRORs (must fix), 1 WARNING (review), 1 INFO (pass)
Exit code: 1 (errors found)
```

## Exit codes

- **0** — no errors; warnings are surfaced but don't block
- **1** — one or more ERRORs found (must fix before publish)
- **2** — script error (e.g., can't read repo, malformed frontmatter)

## Acceptance criteria (Part 7)

The implementation is complete when ALL of the following hold:

1. `--allow-deliberate-fixtures` flag is wired into `~/.hermes/tools/check_instance_leaks.py` (the `argparse` parser + `classify_file()` downgrade + the CLI exit-code path).
2. The canonical fixture pattern (per the section above) is enforced by a dedicated `is_deliberate_fixture()` helper; the helper accepts both list-form and inline-bracket-form `tags:` values (matching the YAML-lite parser's two output shapes).
3. With the flag, files matching the canonical fixture pattern downgrade from `ERROR` → `INFO` and the report adds a `[deliberate-fixture]` prefix on the message.
4. Without the flag, default behavior is unchanged — fixtures still fire `ERROR` (regression suite's contract per `t_764d71c9`).
5. Genuine H1 leaks (matching the H1 pattern but lacking the canonical fixture tags) still fire `ERROR` regardless of the flag.
6. `~/.hermes/hooks/instance-leak-pre-commit` always passes the flag and exits 0 on a working tree that contains the fixtures + genuine Alice-framework content.
7. `~/.hermes/tests/test_check_instance_leaks.py` includes at minimum: (a) a positive case asserting the flag downgrades the fixture; (b) a negative case asserting genuine H1 leaks still ERROR; (c) a default-behavior case asserting no flag = fixture still ERROR.
8. Spec (`references/instance-leak-check-spec.md`) documents the flag in the section above + acceptance criteria updated here.
9. Audit-line appended to `~/Documents/HermesVault/log.md` per op-guard-11.
10. Compliance-verifier child ticket (per op-guard-17) confirms the flag works end-to-end on the alice-framework repo working tree.

## Where the check runs

| Surface | When | Block? |
|---|---|---|
| **Local git pre-commit hook** (`~/.git/hooks/pre-commit`) | Every `git commit` | YES (block commit if ERRORs) |
| **GitHub Actions CI** (`.github/workflows/ci.yml`) | Every PR + push to `main` | YES (block PR if ERRORs) |
| **`alice-publish` loop Step 1** | Before each publish | YES (block publish if ERRORs; per op-guard-19 the verify step precedes the ship) |
| **Operator manual run** | On demand | NO (informational; surfaces all findings) |

## Cross-references

- **Companion methodology:** `methodology/M-decide-instance-vs-framework.md` — the distinction rule
- **Companion template:** `templates/instance-leak-check.py.template` — the canonical script template
- **Companion rule:** `2-ATOMIC/rules/op-guard-19-pre-verify-artifact-state-2026-08-08.md` — the verify-before-ship pattern; this check IS the verify step for alice-publish
- **Companion rule:** `2-ATOMIC/rules/op-guard-20-use-existing-loops-not-bespoke-2026-08-08.md` — the check is wired into the alice-publish loop, not run bespoke
- **Companion ticket:** `kanban:t_98f2f220` — the `--allow-deliberate-fixtures` flag ticket (this Part 2 fix for the v0.2.0 commit message "follow-up ticket TBD")
- **Companion ticket:** `kanban:t_764d71c9` — the H1-fixture origination; codifies that fixtures MUST stay ERROR-flagged
- **Companion ticket:** `kanban:t_d70c695f` — the pre-commit hook origination ticket
- **Audit ticket:** `t_7aa96032` — the file-by-file audit that established the patterns

## Audit-line

`## [2026-08-17T22:00Z] instance-leak-check-spec-v0.3.0 — --allow-deliberate-fixtures opt-out flag added (Part 2 fix for v0.2.0 commit message 7a34a64 follow-up). Canonical fixture pattern: M-decide-*.md in methodology/ + frontmatter kind ∈ {hermes-instance, instance} + tags contains kind:fixture + leak:deliberate. Pre-commit hook always passes the flag; alice-publish Step 1 does NOT pass the flag (publish gate enforces stricter rule). 10 acceptance criteria listed in Part 7. Source: kanban:t_98f2f220 + kanban:t_b3a6ceb9 (PART 2 of the v0.9.53 amend-61 instance-leak fix).`

```
## [2026-08-11T23:30Z] instance-leak-check-spec-v0.2.0 — false-positive filter section added (teaching-example: true + instance-leak-exceptions). pattern exemption list = {H2, H3, H4}; H1/H5/X1 remain structural. Source: t_764d71c9 (Part A rework per op-guard-17 instance-conforms-to-Alice-doc).
```

```
## [2026-08-11T22:15Z] doc-writer-ship — references/instance-leak-check-spec.md v0.1.0 — specification codifies H1-H5 + X1 patterns + U-bucket handling + check output format + exit codes + where-the-check-runs. Source: t_7aa96032 (audit + durable fix).
```
