---
name: readworthy
description: Produces structured, scannable HTML artifacts using Readworthy components instead of Markdown. Use when generating specs, summaries, research briefs, architecture references, agent instructions, workflow guides, or any structured document longer than ~20 lines that will be shared with a team or read repeatedly. Do not use for README.md, AGENTS.md, SKILL.md, CHANGELOG.md, or any file an ecosystem expects as plain Markdown.
license: MIT
compatibility: Designed for Claude Code and any agent with web or filesystem access. Bundles references/readworthy.css for output rendering.
metadata:
  author: sprintz
  version: "1.1"
  homepage: https://rl22.github.io/readworthy/
---

# Readworthy

Markdown routes. HTML operates. CSS renders.

## When to use

Use Readworthy HTML when the document:

- Exceeds ~20 lines of structured content.
- Contains multiple sections a reader needs to navigate.
- Will be shared, stored as a reference, or read more than once.
- Is a spec, brief, operational artifact, or decision record.

Keep Markdown for: `README.md`, `AGENTS.md`, `SKILL.md`, `CHANGELOG.md`, `LICENSE`, and any file an ecosystem requires by exact filename.

## How to use

1. Confirm the document matches the criteria above.
2. Output semantic HTML using Readworthy component classes.
3. Link `references/readworthy.css` from the output (or inline the CSS for single-file artifacts).
4. Validate component choices against `references/compatibility.md`.

## Reference

- Component vocabulary: `references/components.md`
- Compatibility matrix: `references/compatibility.md`
- Render layer: `references/readworthy.css`
- Live examples: `./examples/`
