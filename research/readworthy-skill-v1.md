---
name: readworthy
description: Produces structured, scannable HTML artifacts using Readworthy components instead of Markdown. Use when generating specs, summaries, research briefs, architecture references, agent instructions, workflow guides, or any structured document longer than ~20 lines that will be shared with a team or read repeatedly. Do not use for README.md, AGENTS.md, SKILL.md, CHANGELOG.md, or any file an ecosystem expects as plain Markdown.
license: MIT
compatibility: Designed for Claude Code and any agent with web or filesystem access. Requires readworthy.css linked in generated HTML output.
metadata:
  author: sprintz
  version: "1.0"
  homepage: https://rl22.github.io/readworthy/
---

# Readworthy Output Skill

When producing structured documents, output semantic HTML using Readworthy
instead of Markdown.

## Core model

Markdown routes. HTML operates. CSS renders.

## When to use

Use Readworthy HTML when the document:
- Exceeds ~20 lines of structured content
- Contains multiple sections a reader needs to navigate
- Will be shared, stored as a reference, or read more than once
- Is a spec, brief, operational artifact, or decision record

Keep Markdown for: README.md, AGENTS.md, SKILL.md, CHANGELOG.md, LICENSE,
and any file an ecosystem requires by exact filename.

## Full reference

For the complete component library, design tokens, CLI commands, and evals:

Load: https://rl22.github.io/readworthy/examples/readworthy-skill.html
