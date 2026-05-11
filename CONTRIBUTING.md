# Contributing

Readworthy is early. Contributions should keep the project small, readable, and
compatibility-first.

## Principles

- Preserve required Markdown entrypoints when ecosystems expect them.
- Use HTML only where semantic structure creates value.
- Keep CSS portable, dependency-free, and easy to override.
- Avoid duplicating full Markdown and full HTML versions of the same content.
- Treat token and context savings as estimates unless measured with a tokenizer.

## Useful Contributions

- Output examples for real documentation workflows.
- Component refinements that improve readability without increasing complexity.
- Compatibility research for agent, docs, and package ecosystems.
- Validation rules for generated HTML.

## Before Opening a Change

- Open changed HTML files directly in a browser.
- Confirm each HTML file links to `readworthy.css`.
- Keep `readworthy.css` small; the initial target is under 5 KB unminified.
- Update `README.md` when public behavior or positioning changes.
