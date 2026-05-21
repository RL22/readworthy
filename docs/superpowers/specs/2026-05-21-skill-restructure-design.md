# Restructure Readworthy as an Agent Skill — Design Spec

**Date:** 2026-05-21
**Status:** Approved (pending user review of this doc)
**Supersedes:** nothing; the prior mobile-nav spec (`2026-05-21-mobile-site-nav-design.md`) remains valid follow-up work, with its CSS edits retargeted to `references/readworthy.css` after this restructure lands.

## Problem

The Readworthy repo currently presents itself as a CSS library with a
documentation website (`index.html`, `guide.html`, `examples/`, `research/`)
and an npm-publish path. The actual product direction is an **agent skill**
that emits structured Readworthy HTML: a `SKILL.md` at the root, a
`references/` folder for agent-loadable resources, and the existing site
retained as promotional and human-facing documentation around it.

The `agentskills.io` spec defines a canonical skill layout. The repo does not
follow it: there is no `SKILL.md` at the root, the CSS file lives at the root
rather than under `references/`, and there is no agent-grep-friendly component
or compatibility reference. A draft skill exists at
`research/readworthy-skill-v1.md` but is unpromoted.

## Goals

- Adopt the canonical Agent Skills layout at the repo root.
- Promote `research/readworthy-skill-v1.md` to `SKILL.md`, restructured to the
  recommended **When to use → How to use → Reference** shape with progressive
  disclosure.
- Move `readworthy.css` into `references/readworthy.css` so the skill ships a
  single source-of-truth for the render layer.
- Add two new markdown references the agent can grep / load on demand:
  `references/components.md` and `references/compatibility.md`.
- Keep the promotional/documentation site (`index.html`, `guide.html`,
  `caniuse-html-agent-docs.html`, `examples/`) functional by retargeting
  every `<link>` to `references/readworthy.css`.
- Drop the npm publish path (package.json, related TODO entries).
- Update `README.md` to frame the repo as **agent skill + promo site**, with a
  layout section reflecting the new structure.

## Non-goals

- Implementing the previously approved mobile-nav design.
- Authoring new Readworthy components or theme variants.
- Visual / typographic redesign of the site.
- Migrating the site to a static site generator.
- Adding any JavaScript.

## Target layout

```
readworthy/                          # repo root = skill root (dir name = SKILL `name`)
├── SKILL.md                         # NEW — promoted from research/readworthy-skill-v1.md + restructured
├── references/                      # NEW — agent-loadable bundle
│   ├── readworthy.css               # MOVED from root
│   ├── components.md                # NEW — distilled from guide.html
│   └── compatibility.md             # NEW — distilled from caniuse-html-agent-docs.html
├── index.html                       # KEPT — promo site (link path updated)
├── guide.html                       # KEPT — docs site (link path updated)
├── caniuse-html-agent-docs.html     # KEPT — human compat page (link path updated)
├── examples/                        # KEPT — output samples (link paths updated)
├── research/                        # KEPT (minus the promoted draft)
├── docs/superpowers/specs/          # KEPT — internal design docs
├── README.md                        # UPDATED — reframed as "skill + site"
├── CHANGELOG.md                     # UPDATED — restructure entry
├── CONTRIBUTING.md                  # UPDATED — new layout notes
├── PRODUCT.md                       # KEPT (unchanged)
├── LICENSE                          # KEPT
├── TODO.md                          # UPDATED — npm publish entry removed
└── .gitignore                       # KEPT
```

**Deletions:**
- `package.json` (skill is not an npm package).
- `readworthy.css` at the root (moved).
- `research/readworthy-skill-v1.md` (promoted to `SKILL.md`).

## SKILL.md

Source = `research/readworthy-skill-v1.md`. Frontmatter retained with a small
edit (`metadata.version` bumped to `"1.1"`, `compatibility` reworded to mention
the bundled `references/readworthy.css` instead of the hosted URL). Body
restructured to the agentskills.io recommendation: **When to use → How to use
→ Reference**, with progressive disclosure (short top, deep refs linked).

```markdown
---
name: readworthy
description: Produces structured, scannable HTML artifacts using Readworthy
  components instead of Markdown. Use when generating specs, summaries,
  research briefs, architecture references, agent instructions, workflow
  guides, or any structured document longer than ~20 lines that will be shared
  with a team or read repeatedly. Do not use for README.md, AGENTS.md,
  SKILL.md, CHANGELOG.md, or any file an ecosystem expects as plain Markdown.
license: MIT
compatibility: Designed for Claude Code and any agent with web or filesystem
  access. Bundles references/readworthy.css for output rendering.
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

Keep Markdown for: README.md, AGENTS.md, SKILL.md, CHANGELOG.md, LICENSE, and
any file an ecosystem requires by exact filename.

## How to use

1. Confirm the document matches the criteria above.
2. Output semantic HTML using Readworthy component classes.
3. Link `references/readworthy.css` from the output (or inline the CSS for
   single-file artifacts).
4. Validate component choices against `references/compatibility.md`.

## Reference

- Component vocabulary: `references/components.md`
- Compatibility matrix: `references/compatibility.md`
- Render layer: `references/readworthy.css`
- Live examples: `./examples/`
```

## references/ payloads

### `references/readworthy.css`
File move only; contents unchanged.

### `references/components.md` (new)
A markdown reference enumerating every Readworthy component class with:
- class name
- purpose (one sentence)
- minimal markup snippet
- optional notes (a11y, when to prefer)

Source: walk through `guide.html`. Each `<section class="example">` and its
surrounding prose maps to one component entry. Designed for an agent to grep
or load on demand — plain text, no styling, no embedded HTML beyond fenced
code snippets.

Components to cover (existing classes spotted in `readworthy.css`):
`doc-hero`, `prose`, `toc`, `badge`, `eyebrow`, `lede`, `principle`, `note`,
`callout`, `warning`, `example`, `spec-card`, `agent-instruction`,
`validation-rule`, `timestamp`, `checklist`, `file-tree`, `prompt-block`,
`card-grid`, `status-grid`, `status` (with `supported`/`partial`/`required`
modifiers), `compact-list`.

### `references/compatibility.md` (new)
Markdown distillation of `caniuse-html-agent-docs.html`. Per-component table
of support / caveats. Page stays as the human-facing version on the site.

## Link path updates

Every HTML page currently uses `<link rel="stylesheet" href="readworthy.css" />`.
After the move:

| File                              | New href                          |
| --------------------------------- | --------------------------------- |
| `index.html`                      | `references/readworthy.css`       |
| `guide.html`                      | `references/readworthy.css`       |
| `caniuse-html-agent-docs.html`    | `references/readworthy.css`       |
| `examples/*.html`                 | `../references/readworthy.css`    |
| `research/*.html`                 | `../references/readworthy.css`    |

All identified pages with the link tag at the time of writing:
- root: `index.html`, `guide.html`, `caniuse-html-agent-docs.html`
- `examples/`: `architecture-spec.html`, `index.html`, `readworthy-skill.html`,
  `research-brief.html`, `thread-archive.html`, `video-summary.html`
- `research/`: `comp-analysis-research.html`, `html-first-agent-docs.html`,
  `ia-analysis.html`, `readworthy-naming-brief.html`, `sitemap.html`

A pre-flight `grep -rln 'href="readworthy.css"' --include='*.html'` will
confirm completeness on the day of execution.

## README.md update

Replace `Quick Start`, `Documentation`, and any references to `readworthy.css`
at the root with the new structure. Concrete changes:

1. **Opening framing** — keep the tagline and the `.md / .html / .css` block.
2. **Add a new section "Repo layout"** immediately after Why, listing the
   target layout above and labelling the repo as "an agent skill bundled with
   a promo + docs site".
3. **`Quick Start`** — update the HTML snippet so the `<link>` reads
   `<link rel="stylesheet" href="references/readworthy.css" />`. Add a one-line
   note above the snippet: "Drop the `references/` folder anywhere served
   alongside your HTML."
4. **`Customize`** — unchanged (token names are unaffected).
5. **`Components`** — append: "For an agent-friendly reference of every
   component class, see `references/components.md`."
6. **New section "Using Readworthy as an agent skill"** — after Customize.
   Two paragraphs:
   - Point at `SKILL.md` and the `agentskills.io` spec.
   - Explain that the `references/` folder is the agent's payload, and the
     site at the root is for human readers.
7. **Planned CLI** — unchanged.
8. **Documentation** — rewrite the file list to reflect the new layout:
   - `SKILL.md` — agent skill manifest
   - `references/readworthy.css` — render layer
   - `references/components.md` — component reference
   - `references/compatibility.md` — compatibility matrix
   - `guide.html` — guide and component demo
   - `index.html` — browser homepage
   - `examples/` — output samples
   - `caniuse-html-agent-docs.html` — human-facing compatibility matrix
9. **License** — unchanged.

The npm-publish line referenced indirectly via `Quick Start` and the
`Documentation` file list is removed.

## Other doc updates

- **CHANGELOG.md** — new entry:
  > **Restructure as agent skill.** `readworthy.css` moved to
  > `references/readworthy.css`. New `SKILL.md` at the repo root. New
  > `references/components.md` and `references/compatibility.md`. Dropped
  > `package.json` and the npm publish path. Site HTML retargeted to the new
  > CSS path.
- **CONTRIBUTING.md** — update any path references that mention
  `readworthy.css` or `research/readworthy-skill-v1.md`.
- **TODO.md** — delete item 7 ("Publish `readworthy` to npm after the GitHub
  repo and webpage are verified."). Delete the "Add a minified
  `readworthy.min.css` for CDN usage" enhancement bullet (CDN is
  npm-adjacent and gone with npm). Other items are reviewed and kept.

## Migration sequence

1. `git mv readworthy.css references/readworthy.css`.
2. `git mv research/readworthy-skill-v1.md SKILL.md`; restructure its body to
   the When/How/Reference shape; bump `metadata.version` to `"1.1"`; reword
   `compatibility` field.
3. Author `references/components.md` from `guide.html`.
4. Author `references/compatibility.md` from `caniuse-html-agent-docs.html`.
5. Bulk update `<link>` paths in every HTML page (root, examples/,
   research/).
6. `git rm package.json`.
7. Update `README.md` per the section above.
8. Update `CHANGELOG.md`, `CONTRIBUTING.md`, `TODO.md`.
9. Verify (see Testing).

## Testing

- `grep -rn 'href="readworthy.css"' --include='*.html'` returns zero hits
  post-migration.
- `grep -rn 'href=".*references/readworthy.css"' --include='*.html'` covers
  every HTML page that previously linked the stylesheet.
- Open each HTML page in a browser, confirm CSS loads (no missing-stylesheet
  fallback rendering).
- `SKILL.md` validates against the agentskills.io frontmatter constraints:
  `name` is `readworthy` and matches the parent directory; `description` is
  ≤1024 chars; `compatibility` is ≤500 chars; no consecutive hyphens in
  `name`.
- `references/components.md` covers every component class defined in
  `references/readworthy.css`; spot-check with
  `grep -E '^\.\w[\w-]*\s*[,{]' references/readworthy.css` and confirm each
  user-facing class appears in components.md.
- README renders on GitHub without broken links.

## Risks / open questions

- **components.md scope creep** — easy to over-document. Constraint: one entry
  per user-facing class; under 80 lines per entry; no design rationale, only
  usage. If the file grows past ~600 lines, split per category.
- **compatibility.md drift** — the human-facing
  `caniuse-html-agent-docs.html` and the new `references/compatibility.md`
  will diverge over time. Acceptable for v1; later we can generate one from
  the other.
- **Hosted homepage URL** — `metadata.homepage` keeps the GitHub Pages URL.
  No change needed; GitHub Pages still serves `index.html` from the root.

## Relationship to prior mobile-nav spec

`docs/superpowers/specs/2026-05-21-mobile-site-nav-design.md` remains a valid
follow-up. After this restructure, its CSS edits target
`references/readworthy.css`. No structural rewrite of that spec is required;
the file path reference is implicit.
