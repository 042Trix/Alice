# Contributing to Alice

Thanks for helping improve Alice. Contributions may add or clarify a methodology document, template, reference, or worked-example skeleton. Alice is documentation-only: there is no build step or runtime to install.

## Read the project first

1. Clone the repository: `git clone <repository-url>`.
2. Read [README.md](README.md), including its install/read path from Card A.
3. Read [METHODOLOGY.md](METHODOLOGY.md) for the linear walkthrough.

> **Card A placeholder:** The canonical README/install path is maintained by Card A. If that path changes, follow the instructions in `README.md`; do not duplicate them here.

## Issues

Use the repository issue tracker. Use the `documentation` label for documentation, methodology, template, reference, or worked-example issues; if that label is unavailable, use the closest repository-provided documentation label. Follow an existing issue template when one is offered. A useful issue states the problem, affected path, expected behavior, and a minimal example or proposed wording.

Do not put credentials, private operator data, access tokens, or other sensitive information in a public issue. Report vulnerabilities privately through [SECURITY.md](SECURITY.md).

## Proposing a methodology document

A methodology contribution must pass this method-not-instance rule before review. It must teach a reusable decision process that another operator can apply to their own context—not document, prescribe, or expose one operator's exact system.

The proposal must explicitly satisfy this checklist:

1. **Method, not instance:** explain the decision process, applicability, steps, trade-offs, anti-patterns, and revision signals. Do not snapshot a private setup.
2. **Tool-agnostic:** describe concepts independently of a particular product; put tool-specific mappings in references.
3. **Generic naming:** do not include private board names, agent names, IDs, credentials, or operator-specific paths.
4. **Terminology:** define colliding terms and link the relevant glossary or reference.
5. **Reusable shape:** show how a reader adapts the method to their own context.
6. **Cross-references:** include a `## See also` section and update affected indexes or links in the same pull request.
7. **Examples stay separate:** instance-specific illustrations belong under `worked-examples/` and must be clearly labeled skeletons.

If the material only describes one deployment, move it to a worked-example skeleton rather than the methodology tree. Keep changes focused and do not rewrite unrelated methodology.

## Pull requests

- Use a focused branch such as `docs/add-methodology-guide` or `docs/fix-broken-link`.
- Use concise conventional commit subjects, for example `docs: add methodology guide` or `docs: fix broken link`.
- Check every internal link touched; there is no build or test command.
- Open a focused pull request and request review from the maintainer/operator. Include the problem addressed, files changed, method-not-instance checklist confirmation, and related issue references.
- Wait for review. Do not bundle unrelated cleanup.

A change is done when its documentation is clear and portable, the method-not-instance checklist passes, terminology and cross-references are updated, internal links are checked, and the maintainer has accepted it.

## Project policies

- [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md)
- [SECURITY.md](SECURITY.md)
- [GOVERNANCE.md](GOVERNANCE.md)

## License

Contributions are intended to be distributed under the project's [MIT License](LICENSE), once Card C adds the license file and the repository's release posture is finalized. Until then, this relative anchor is the canonical placeholder; the operator must confirm or revise the license before publication.
