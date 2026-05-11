# Readworthy

AI-native docs worth reading.

Readworthy is a compatibility-first documentation toolkit for AI workflows. It
keeps Markdown where ecosystems expect Markdown, moves high-structure material
into semantic HTML when that creates value, and uses `readworthy.css` as a tiny
render layer for readable, agent-friendly documents.

```text
.md   = compatibility, discovery, activation
.html = structured operational docs
.css  = presentation layer
```

## Why

AI-generated documentation often becomes bloated, repetitive, and hard to scan.
The problem is not Markdown itself. The problem is using unstructured prose for
documents that need validation rules, prompt blocks, warnings, status matrices,
component examples, and operational workflows.

Readworthy helps teams create docs that are:

- readable for humans
- structured for agents
- compatible with existing Markdown-first tooling
- lightweight enough to open as standalone HTML files
- brandable through CSS variables

## Quick Start

Copy `readworthy.css` into your project and link it from a semantic HTML file.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <link rel="stylesheet" href="readworthy.css" />
  <title>Project Spec</title>
</head>
<body class="doc">
  <header class="doc-hero">
    <p class="eyebrow">Project Spec</p>
    <h1>Readable Agent Docs</h1>
    <p class="lede">Structured documentation for humans and agents.</p>
  </header>

  <main class="prose">
    <section class="note">
      <h2>Note</h2>
      <p>Markdown can remain the routing layer while HTML carries structure.</p>
    </section>
  </main>
</body>
</html>
```

## Customize

Readworthy ships with neutral defaults. Override CSS variables to match your
brand or design reference.

```css
:root {
  --rw-bg: oklch(0.985 0.004 255);
  --rw-fg: oklch(0.19 0.012 255);
  --rw-muted: oklch(0.47 0.018 255);
  --rw-border: oklch(0.86 0.012 255);
  --rw-surface: oklch(0.955 0.008 255);
  --rw-accent: oklch(0.52 0.14 255);
  --rw-warn: oklch(0.58 0.16 62);
  --rw-font: ui-sans-serif, system-ui, sans-serif;
  --rw-mono: ui-monospace, monospace;
}
```

## Components

Readworthy is an atomic design-inspired documentation language.

- Base elements: headings, prose, tables, links, code, lists.
- Document components: badges, notes, warnings, examples, checklists, file
  trees, spec cards, prompt blocks, agent instructions, validation rules.
- Output templates: video summaries, compatibility matrices, product specs,
  agent docs, workflow references.

See the guide in `guide.html` and the output example in `video-summary.html`.

## Planned CLI

The CLI is planned as the deterministic analysis and conversion layer.

```text
readworthy init      # audit, classify, estimate, and plan
readworthy convert   # execute an approved plan
readworthy audit     # recalculate compatibility and context estimates
readworthy validate  # validate generated HTML and compatibility rules
```

The CLI should preserve required Markdown files by default. It should generate
HTML only where semantic structure creates enough value to justify it.

## Documentation

- `readworthy-naming-brief.html`: naming and positioning decision.
- `html-first-agent-docs.html`: research thesis.
- `caniuse-html-agent-docs.html`: compatibility matrix.
- `cli-architecture.html`: CLI architecture reference.
- `index.html`: browser homepage for the project.
- `guide.html`: guide and component demo.
- `video-summary.html`: standalone output example.

## License

MIT
