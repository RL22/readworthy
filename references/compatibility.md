# Compatibility Matrix

Markdown distillation of `caniuse-html-agent-docs.html`. Tells the agent when Readworthy HTML can replace, supplement, or should not be used in place of a conventional Markdown file.

The HTML version at `/caniuse-html-agent-docs.html` is the human-facing source.

## Status key

| Status              | Meaning                                                                       |
| ------------------- | ----------------------------------------------------------------------------- |
| Supported           | HTML can be used directly with low compatibility risk.                        |
| Partial             | HTML can be canonical or supplemental, but Markdown is still needed for ecosystem routing. |
| Markdown required   | The tool or ecosystem expects Markdown or plain text.                         |
| Not recommended     | HTML adds more markup than value for this file type.                          |

## Matrix

| File                | Status              | Keep Markdown?                       | HTML opportunity                                                                          | Agent loading guidance                                            |
| ------------------- | ------------------- | ------------------------------------ | ----------------------------------------------------------------------------------------- | ----------------------------------------------------------------- |
| `README.md`         | Markdown required   | Yes                                  | Add `docs/index.html` for a richer landing-style version.                                 | Load README for discovery only.                                   |
| `SKILL.md`          | Markdown required   | Yes                                  | Use `references/skill.html` for deeper operational instructions.                          | Load tiny SKILL first; HTML only on relevant tasks.               |
| `AGENTS.md`         | Markdown required   | Yes                                  | Use HTML references for complex workflows or architecture docs.                           | Keep concise; route to targeted files.                            |
| `install.md`        | Partial             | Usually                              | HTML is useful for checklists, setup states, troubleshooting cards.                        | Keep Markdown for quick setup; load HTML for guided install docs. |
| `design.md`         | Partial             | Yes if tools expect plain context    | Strong candidate for `design.html` with tokens, swatches, examples, components.            | Load Markdown summary first; HTML only for visual decisions.       |
| `brand.md`          | Partial             | Yes for voice/tone summaries         | HTML works well for voice cards, examples, do/don't tables, brand assets.                  | Load relevant brand section only.                                  |
| `product.md`        | Partial             | Optional                             | HTML works well for feature cards, requirements, status matrices, priorities.              | Use HTML when product specs are complex or visual.                 |
| `components.md`     | Supported           | Optional                             | Strong HTML candidate because components benefit from rendered examples.                   | Load only component docs relevant to the UI task.                  |
| `validation.md`     | Supported           | Optional                             | Strong HTML candidate for structured validation-rule sections.                             | Load only validation rules for the current output type.            |
| `prompts.md`        | Partial             | Often                                | HTML helps when prompts need metadata, variants, examples, and usage rules.                | Index prompts; load only the selected prompt block.                |
| `workflows.md`      | Supported           | Optional                             | HTML is useful for phased flows, decision trees, and operational states.                   | Load workflow sections by task trigger.                            |
| `architecture.md`   | Partial             | Often                                | HTML helps for diagrams, system cards, dependency tables.                                  | Load specific subsystem docs only.                                 |
| `CHANGELOG.md`      | Not recommended     | Yes                                  | HTML rarely adds enough value.                                                              | Do not load unless version history matters.                        |
| `CONTRIBUTING.md`   | Not recommended     | Yes                                  | HTML may help for public docs, but not as agent context.                                   | Keep as Markdown.                                                  |
| `LICENSE`           | Markdown required   | Yes                                  | Do not convert legal source files.                                                          | Never rewrite unless explicitly requested.                         |

## Current limitations

- **HTML-only is not a universal replacement.** Many tools discover instructions by exact filenames such as `README.md`, `AGENTS.md`, or `SKILL.md`. A standalone `new-skill.html` will not satisfy those requirements unless the host explicitly supports it.
- **HTML can introduce waste.** Full Markdown plus full HTML duplicates increase context and maintenance cost. Recommended: a lightweight Markdown loader plus targeted HTML references.
- **CSS is not reasoning context.** Agents usually do not need the full stylesheet. CSS is for rendering, visual inspection, and publication — not default task reasoning.

## Compatibility contract

1. Preserve required Markdown files.
2. Keep Markdown loaders concise.
3. Generate HTML only where structure creates value.
4. Never duplicate full canonical content across MD and HTML.
5. Use task-triggered progressive disclosure.
6. Exclude CSS from default agent context.

## Decision checklist

- Does an ecosystem require this exact Markdown filename?
- Does the doc benefit from semantic sections, cards, or validation rules?
- Will HTML reduce prose or merely wrap it?
- Can the agent load only the relevant section?
- Can the Markdown file remain a short compatibility layer?
