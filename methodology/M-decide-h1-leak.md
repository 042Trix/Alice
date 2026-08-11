---
type: methodology
id: M-decide-h1-leak
version: 0.0.1
created: 2026-08-11T10:00:00Z
updated: 2026-08-11T10:00:00Z
status: active
kind: hermes-instance
source: hermes-instance
tags: [domain:test, kind:fixture, leak:deliberate]
confidence: 1.0
links: []
---

# Deliberate H1 instance-leak fixture

This file is a **deliberate instance-leak fixture** used by `~/.hermes/tests/test_check_instance_leaks.py` to confirm the H1 sub-pattern A (kind frontmatter) fires correctly.

The H1 pattern in `~/.hermes/tools/check_instance_leaks.py` matches any `methodology/M-decide-*.md` file whose frontmatter carries `kind: hermes-instance` or `kind: instance`. The check should fire `ERROR` / H1 / `MOVE to ~/.hermes/methodology/` on this file.

This fixture MUST remain in `alice-framework/methodology/` (the framework side) so the check has a real on-disk target to fire against. Tests delete-then-recreate it under a tempfile path; do NOT add it to `.gitignore` or the `M-decide-*` exclude list.

## Why this exists

Without a fixture, the regression suite would have to construct a synthetic file via StringIO + ad-hoc frontmatter parsing — and that's not how the dispatcher fires H1 in production. The on-disk fixture ensures the same code path is exercised end-to-end:

1. `walk_repo` finds the file.
2. `Frontmatter.parse` parses the frontmatter.
3. `classify_file` checks `M-decide-` prefix + `methodology/` parent + `kind` value.
4. `Finding(severity=ERROR, pattern_id=H1)` is appended.
5. `format_text` / `format_json` render it.

## Fixture contract

- File path: `alice-framework/methodology/M-decide-h1-leak.md`
- Frontmatter `kind`: `hermes-instance`
- Frontmatter `source`: `hermes-instance`
- Body: a single short paragraph + the explanatory text above. No real methodology content.

The fixture is paired with the test suite at `~/.hermes/tests/test_check_instance_leaks.py`. Update both in the same change set per op-guard-5 paired-wiki integrity.
