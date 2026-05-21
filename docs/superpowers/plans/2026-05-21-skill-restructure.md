# Readworthy Skill Restructure Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Restructure the Readworthy repo so it is a valid `agentskills.io` skill at the root (`SKILL.md` + `references/`) while keeping the existing promo/docs site fully functional.

**Architecture:** Move `readworthy.css` under `references/`, promote the v1 SKILL draft to `SKILL.md` at the root and restructure its body to When/How/Reference, author two new agent-loadable references (`components.md`, `compatibility.md`), retarget every site HTML `<link>` tag, drop the npm publish path, and update documentation (README, CHANGELOG, CONTRIBUTING, TODO).

**Tech Stack:** Plain HTML + CSS + Markdown. Git. No build step. No JavaScript.

**Spec:** `docs/superpowers/specs/2026-05-21-skill-restructure-design.md`

---

## File Structure

Files this plan creates, moves, or deletes:

| Action | Path | Purpose |
| --- | --- | --- |
| Create | `references/` | Agent-loadable bundle directory |
| Move   | `readworthy.css` → `references/readworthy.css` | Render layer, single source of truth |
| Move   | `research/readworthy-skill-v1.md` → `SKILL.md` | Skill manifest at repo root |
| Edit   | `SKILL.md` (after move) | Restructure body to When/How/Reference |
| Create | `references/components.md` | Per-class component reference |
| Create | `references/compatibility.md` | Markdown distillation of the caniuse page |
| Edit   | `index.html` | `<link>` href update |
| Edit   | `guide.html` | `<link>` href update |
| Edit   | `caniuse-html-agent-docs.html` | `<link>` href update |
| Edit   | `examples/*.html` (6 files) | `<link>` href update |
| Edit   | `research/*.html` (5 files) | `<link>` href update |
| Delete | `package.json` | Drop npm publish path |
| Edit   | `README.md` | Reframe as skill + site |
| Edit   | `CHANGELOG.md` | Restructure entry |
| Edit   | `CONTRIBUTING.md` | New layout paths |
| Edit   | `TODO.md` | Drop npm entries |

Files left untouched: `LICENSE`, `PRODUCT.md`, `.gitignore`, the unmodified contents of `research/`, all `docs/superpowers/specs/*`.

---

## Task 1: Move readworthy.css under references/ and retarget every HTML link

**Files:**
- Create: `references/` (directory)
- Move:   `readworthy.css` → `references/readworthy.css`
- Modify: `index.html`, `guide.html`, `caniuse-html-agent-docs.html`
- Modify: `examples/architecture-spec.html`, `examples/index.html`, `examples/readworthy-skill.html`, `examples/research-brief.html`, `examples/thread-archive.html`, `examples/video-summary.html`
- Modify: `research/comp-analysis-research.html`, `research/html-first-agent-docs.html`, `research/ia-analysis.html`, `research/readworthy-naming-brief.html`, `research/sitemap.html`

This task must complete in one commit. Splitting the move from the link updates leaves the site broken between commits.

- [ ] **Step 1: Confirm the current list of HTML files linking the stylesheet**

```bash
grep -rln 'href="\(\.\./\)*readworthy\.css"' --include='*.html' .
```

Expected output (14 files):

```
caniuse-html-agent-docs.html
examples/architecture-spec.html
examples/index.html
examples/readworthy-skill.html
examples/research-brief.html
examples/thread-archive.html
examples/video-summary.html
guide.html
index.html
research/comp-analysis-research.html
research/html-first-agent-docs.html
research/ia-analysis.html
research/readworthy-naming-brief.html
research/sitemap.html
```

If the list differs, stop and reconcile against the spec before continuing.

- [ ] **Step 2: Create the references/ directory and move the CSS**

```bash
mkdir -p references
git mv readworthy.css references/readworthy.css
```

Verify:

```bash
ls references/
git status --short
```

Expected: `references/readworthy.css` exists; `git status` shows `R  readworthy.css -> references/readworthy.css`.

- [ ] **Step 3: Update root-level HTML link paths**

In each of `index.html`, `guide.html`, `caniuse-html-agent-docs.html`, change:

```html
<link rel="stylesheet" href="readworthy.css" />
```

to:

```html
<link rel="stylesheet" href="references/readworthy.css" />
```

Use a single sed across just the root files:

```bash
sed -i '' 's|href="readworthy.css"|href="references/readworthy.css"|g' \
  index.html guide.html caniuse-html-agent-docs.html
```

(Note: macOS BSD sed uses `-i ''`. On GNU sed remove the empty string.)

Verify:

```bash
grep -n 'readworthy.css' index.html guide.html caniuse-html-agent-docs.html
```

Expected: every match shows `references/readworthy.css`.

- [ ] **Step 4: Update `examples/` link paths**

In each `examples/*.html`, change:

```html
<link rel="stylesheet" href="../readworthy.css" />
```

(or `href="readworthy.css"` if that variant appears) to:

```html
<link rel="stylesheet" href="../references/readworthy.css" />
```

Use sed:

```bash
sed -i '' 's|href="\.\./readworthy.css"|href="../references/readworthy.css"|g; s|href="readworthy.css"|href="../references/readworthy.css"|g' \
  examples/*.html
```

Verify:

```bash
grep -n 'readworthy.css' examples/*.html
```

Expected: every match shows `../references/readworthy.css`.

- [ ] **Step 5: Update `research/` link paths**

```bash
sed -i '' 's|href="\.\./readworthy.css"|href="../references/readworthy.css"|g; s|href="readworthy.css"|href="../references/readworthy.css"|g' \
  research/*.html
```

Verify:

```bash
grep -n 'readworthy.css' research/*.html
```

Expected: every match shows `../references/readworthy.css`.

- [ ] **Step 6: Confirm zero stale references remain**

```bash
grep -rn 'href="\(\.\./\)*readworthy\.css"' --include='*.html' .
```

Expected: **no output** (every link now goes through `references/`).

Also:

```bash
grep -rln 'href=".*references/readworthy\.css"' --include='*.html' . | wc -l
```

Expected: `14`.

- [ ] **Step 7: Smoke-test in a browser**

Open `index.html`, `guide.html`, `caniuse-html-agent-docs.html`, `examples/video-summary.html`, and `research/sitemap.html` directly with `open <file>` (macOS) or your browser. Each page must render styled (not as raw HTML). If any page renders unstyled, fix that file’s link path before continuing.

- [ ] **Step 8: Commit**

```bash
git add -A
git commit -m "Move readworthy.css to references/ and retarget site links

CSS becomes the single source of truth bundled with the skill.
All 14 HTML pages updated to the new path.
"
```

---

## Task 2: Promote and restructure SKILL.md

**Files:**
- Move:   `research/readworthy-skill-v1.md` → `SKILL.md`
- Modify: `SKILL.md` (rewrite body, bump frontmatter version, reword `compatibility`)

- [ ] **Step 1: Move the draft to the repo root**

```bash
git mv research/readworthy-skill-v1.md SKILL.md
```

Verify:

```bash
git status --short
ls SKILL.md
```

Expected: `R  research/readworthy-skill-v1.md -> SKILL.md`.

- [ ] **Step 2: Replace the entire contents of `SKILL.md`**

Open `SKILL.md` and replace its full contents with:

```markdown
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
```

- [ ] **Step 3: Validate frontmatter constraints**

The `agentskills.io` spec sets the following limits. Check each:

```bash
# name field equals the parent directory name
basename "$(pwd)"
# Expected: readworthy

# description length ≤ 1024
awk '/^description:/{flag=1} /^[a-z-]+:/{if(NR>1 && !/^description:/) flag=0} flag' SKILL.md | wc -c

# compatibility length ≤ 500
awk '/^compatibility:/{flag=1} /^[a-z-]+:/{if(NR>1 && !/^compatibility:/) flag=0} flag' SKILL.md | wc -c
```

Expected: parent dir is `readworthy`; `description` byte count is well under 1024; `compatibility` byte count is well under 500.

- [ ] **Step 4: Commit**

```bash
git add SKILL.md research/readworthy-skill-v1.md
git commit -m "Promote SKILL.md to repo root and restructure body

Body reorganized to agentskills.io recommended shape:
When to use -> How to use -> Reference. Frontmatter bumped to
version 1.1 and the compatibility field now points at the bundled
references/readworthy.css.
"
```

---

## Task 3: Author references/components.md

**Files:**
- Create: `references/components.md`

The file is a per-class reference an agent can grep. Every Readworthy class defined in `references/readworthy.css` must have one entry. Read each class from `references/readworthy.css` and the matching demo in `guide.html`.

- [ ] **Step 1: Enumerate every user-facing class to cover**

The following classes must each get one entry, in this order:

1. `doc` (body class)
2. `doc-hero`
3. `prose`
4. `toc`
5. `badge`
6. `eyebrow`
7. `lede`
8. `principle`
9. `note`
10. `callout`
11. `warning`
12. `example`
13. `spec-card`
14. `agent-instruction`
15. `validation-rule`
16. `prompt-block`
17. `timestamp`
18. `checklist`
19. `file-tree`
20. `card-grid`
21. `status-grid`
22. `status` (with modifiers `supported`, `partial`, `required`, `neutral`)
23. `compact-list`

`.site-nav` is intentionally omitted; it is a markup hook with no CSS treatment yet, and the follow-up mobile-nav spec will introduce its rules.

If a class appears in `references/readworthy.css` but is missing from this list, add it; if a listed class is no longer in the CSS, drop it.

- [ ] **Step 2: Create `references/components.md` with this header**

```markdown
# Readworthy Components

Per-class reference for the Readworthy render layer. Pair with `references/readworthy.css` and `references/compatibility.md`.

Each entry lists the class, its purpose, a minimal markup snippet, and any usage notes. Entries are short by design — keep one component’s entry under 80 lines.

---
```

- [ ] **Step 3: Add one entry per class using this template**

Template:

```markdown
## `.<class-name>`

**Purpose.** One sentence describing what the component is for.

**Markup.**

\`\`\`html
<!-- minimal usable example -->
\`\`\`

**Notes.** (Optional, one or two lines.) Accessibility, when to prefer, available modifiers.
```

Worked example (paste this entry verbatim for `.callout`):

```markdown
## `.callout`

**Purpose.** A short emphasized line break used to flag the headline takeaway of a section.

**Markup.**

\`\`\`html
<section class="callout">
  <strong>Short answer:</strong> Readworthy works today as a reference layer.
</section>
\`\`\`

**Notes.** Sits at `font-size: 1.05rem` with a hairline top border. Use sparingly — one or two per document.
```

Author every other entry by reading the matching markup in `guide.html` and matching CSS rule in `references/readworthy.css`. Do not embed raw HTML beyond fenced snippets. Do not include design rationale or color values.

- [ ] **Step 4: Verify coverage**

```bash
# Every class in the CSS should appear as a heading in components.md.
grep -Eo '^\.[a-z][a-z0-9-]*' references/readworthy.css | sort -u > /tmp/css-classes.txt
grep -Eo '^## `\.[a-z][a-z0-9-]+`' references/components.md \
  | sed 's/^## `\.\(.*\)`/.\1/' | sort -u > /tmp/doc-classes.txt
diff /tmp/css-classes.txt /tmp/doc-classes.txt
```

Expected: diff shows no missing user-facing class. (CSS-only helpers like `.matrix` used inside `caniuse-html-agent-docs.html` that aren’t intended as a public class may be intentionally absent — note them in `components.md`’s closing comment if so.)

- [ ] **Step 5: Commit**

```bash
git add references/components.md
git commit -m "Add references/components.md (per-class component reference)"
```

---

## Task 4: Author references/compatibility.md

**Files:**
- Create: `references/compatibility.md`

Distill `caniuse-html-agent-docs.html` into a markdown file an agent can grep.

- [ ] **Step 1: Create `references/compatibility.md` with this exact content**

```markdown
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
| `brand.md`          | Partial             | Yes for voice/tone summaries         | HTML works well for voice cards, examples, do/don’t tables, brand assets.                  | Load relevant brand section only.                                  |
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
```

- [ ] **Step 2: Confirm the file lints as Markdown**

Open `references/compatibility.md` in a Markdown previewer (or `npx markdownlint references/compatibility.md` if available). Tables must render with aligned columns.

- [ ] **Step 3: Commit**

```bash
git add references/compatibility.md
git commit -m "Add references/compatibility.md (markdown distillation of caniuse page)"
```

---

## Task 5: Drop package.json and prune npm references

**Files:**
- Delete: `package.json`
- Modify: `TODO.md`
- Modify: `CONTRIBUTING.md`

- [ ] **Step 1: Remove the npm manifest**

```bash
git rm package.json
```

Verify:

```bash
git status --short
ls package.json 2>/dev/null
```

Expected: `D  package.json`; no such file remains.

- [ ] **Step 2: Update `TODO.md`**

Open `TODO.md` and replace its contents with the following (the v1 list with item 7 and the npm/CDN enhancement bullet removed; remaining items renumbered to stay contiguous):

```markdown
# TODO

1. Confirm the public GitHub Pages URL renders `index.html`.
2. Review each linked HTML page from the hosted site.
3. Add social preview metadata to `index.html`.
4. Add a dedicated `components.html` reference page if the guide gets too long.
5. Add more output examples: `agent-skill.html`, `project-spec.html`, and `validation-rules.html`.
6. Build the first CLI command: `readworthy init`, with a non-mutating repo audit.

## Enhancement Ideas

- Add a GitHub Pages screenshot to the README.
- Add a copy-paste starter template.
- Add accessibility notes for every component class.
- Add validation fixtures for generated HTML.
- Add a short “when not to use HTML” section to the homepage.
```

- [ ] **Step 3: Update `CONTRIBUTING.md` path references**

Open `CONTRIBUTING.md` and replace any reference to a root-level `readworthy.css` with the new path. Specifically, replace:

```markdown
- Open changed HTML files directly in a browser.
- Confirm each HTML file links to `readworthy.css`.
- Keep `readworthy.css` small; the initial target is under 5 KB unminified.
- Update `README.md` when public behavior or positioning changes.
```

with:

```markdown
- Open changed HTML files directly in a browser.
- Confirm each HTML file links to `references/readworthy.css`.
- Keep `references/readworthy.css` small; the initial target is under 5 KB unminified.
- Update `README.md` when public behavior or positioning changes.
```

- [ ] **Step 4: Commit**

```bash
git add TODO.md CONTRIBUTING.md package.json
git commit -m "Drop npm publish path

Remove package.json. Strip npm + CDN entries from TODO.md.
Update CONTRIBUTING.md paths to references/readworthy.css.
"
```

---

## Task 6: Update README.md

**Files:**
- Modify: `README.md`

- [ ] **Step 1: Replace the `Quick Start` section**

Find:

```markdown
## Quick Start

Copy `readworthy.css` into your project and link it from a semantic HTML file.

\`\`\`html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <link rel="stylesheet" href="readworthy.css" />
  <title>Project Spec</title>
</head>
```

Replace with:

```markdown
## Quick Start

Drop the `references/` folder anywhere served alongside your HTML, then link the stylesheet.

\`\`\`html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <link rel="stylesheet" href="references/readworthy.css" />
  <title>Project Spec</title>
</head>
```

(The rest of the snippet — `<body class="doc">` through `</html>` — is unchanged.)

- [ ] **Step 2: Insert a new `Repo layout` section between `Why` and `Quick Start`**

Add immediately after the `Why` section:

```markdown
## Repo layout

Readworthy is an agent skill bundled with a promo + docs site.

\`\`\`text
readworthy/
├── SKILL.md                 agent skill manifest
├── references/              agent-loadable bundle
│   ├── readworthy.css       render layer (single source of truth)
│   ├── components.md        per-class component reference
│   └── compatibility.md     markdown compatibility matrix
├── index.html               browser homepage
├── guide.html               component demo
├── caniuse-html-agent-docs.html  human-facing compatibility page
├── examples/                output samples
├── research/                analysis + drafts
├── docs/                    internal specs and plans
└── README.md, CHANGELOG.md, CONTRIBUTING.md, PRODUCT.md, LICENSE
\`\`\`
```

- [ ] **Step 3: Append a "Using Readworthy as an agent skill" section after `Customize`**

Insert this section between `Customize` and `Components`:

```markdown
## Using Readworthy as an agent skill

The repo root is a valid skill in the [`agentskills.io`](https://agentskills.io) sense: `SKILL.md` provides the manifest, and `references/` contains the agent-loadable payload (render CSS plus markdown references for components and compatibility).

The HTML files at the root are for human readers — they are not loaded by the agent at runtime. Point your agent at `SKILL.md`; it will pull `references/` on demand.
```

- [ ] **Step 4: Update the `Components` section closing sentence**

Find:

```markdown
See the guide in `guide.html` and the output example in `video-summary.html`.
```

Replace with:

```markdown
See the guide in `guide.html` and the output example in `examples/video-summary.html`. For an agent-friendly reference of every component class, see `references/components.md`.
```

- [ ] **Step 5: Replace the `Documentation` section**

Find:

```markdown
## Documentation

- `readworthy-naming-brief.html`: naming and positioning decision.
- `html-first-agent-docs.html`: research thesis.
- `caniuse-html-agent-docs.html`: compatibility matrix.
- `architecture-spec.html`: CLI architecture reference.
- `index.html`: browser homepage for the project.
- `guide.html`: guide and component demo.
- `video-summary.html`: standalone output example.
```

Replace with:

```markdown
## Documentation

- `SKILL.md`: agent skill manifest.
- `references/readworthy.css`: render layer.
- `references/components.md`: per-class component reference.
- `references/compatibility.md`: markdown compatibility matrix.
- `guide.html`: guide and component demo.
- `index.html`: browser homepage.
- `caniuse-html-agent-docs.html`: human-facing compatibility page.
- `examples/`: standalone output samples.
- `research/readworthy-naming-brief.html`: naming and positioning decision.
- `research/html-first-agent-docs.html`: research thesis.
- `examples/architecture-spec.html`: CLI architecture reference.
```

- [ ] **Step 6: Verify no stale references**

```bash
grep -n 'readworthy.css' README.md
```

Expected: every match shows `references/readworthy.css`. No bare `readworthy.css` references remain.

- [ ] **Step 7: Commit**

```bash
git add README.md
git commit -m "Reframe README as agent skill + promo site

Add Repo layout section. Update Quick Start to link
references/readworthy.css. New 'Using Readworthy as an agent skill'
section. Documentation section rewritten to match the new layout.
"
```

---

## Task 7: Update CHANGELOG.md

**Files:**
- Modify: `CHANGELOG.md`

- [ ] **Step 1: Prepend a new release section**

Open `CHANGELOG.md` and insert the following immediately after the `# Changelog` heading and before the `## 0.1.0` section:

```markdown
## 0.2.0

- Restructured the repo as an agent skill at the root.
- Promoted `SKILL.md` from `research/readworthy-skill-v1.md`; reorganized body to When/How/Reference.
- Moved `readworthy.css` to `references/readworthy.css` (single source of truth).
- Added `references/components.md` (per-class component reference).
- Added `references/compatibility.md` (markdown distillation of the caniuse page).
- Retargeted all 14 site HTML pages to `references/readworthy.css`.
- Dropped `package.json` and removed the npm/CDN publish path.
- Updated `README.md`, `CONTRIBUTING.md`, and `TODO.md` to match the new layout.

```

- [ ] **Step 2: Commit**

```bash
git add CHANGELOG.md
git commit -m "Document 0.2.0 restructure in CHANGELOG"
```

---

## Task 8: Final verification

**Files:** none modified.

- [ ] **Step 1: Confirm no stale CSS link paths remain anywhere in the repo**

```bash
grep -rn 'href="\(\.\./\)*readworthy\.css"' --include='*.html' .
grep -rn '"readworthy.css"' . --exclude-dir=.git --exclude-dir=node_modules
```

Expected: both commands return **no output**. (The second command catches any stray references in markdown, JSON, or config.)

- [ ] **Step 2: Confirm all 14 HTML pages now link the moved CSS**

```bash
grep -rln 'href="\(\.\./\)*references/readworthy\.css"' --include='*.html' . | wc -l
```

Expected: `14`.

- [ ] **Step 3: Confirm skill layout matches the agentskills.io spec**

```bash
ls SKILL.md references/readworthy.css references/components.md references/compatibility.md
basename "$(pwd)"        # must equal the `name:` value in SKILL.md
test ! -e package.json && echo "package.json removed ✓"
test ! -e readworthy.css && echo "root readworthy.css removed ✓"
test ! -e research/readworthy-skill-v1.md && echo "v1 draft promoted ✓"
```

Expected: all four files exist, parent dir is `readworthy`, and all three checks print their `✓` lines.

- [ ] **Step 4: Browser smoke test**

Open in a browser and confirm correct styling (no unstyled fallback):

- `index.html`
- `guide.html`
- `caniuse-html-agent-docs.html`
- `examples/index.html`
- `examples/video-summary.html`
- `research/sitemap.html`

If any page renders unstyled, the link path on that page is wrong. Fix and recommit before continuing.

- [ ] **Step 5: Confirm clean tree**

```bash
git status
git log --oneline -8
```

Expected: working tree clean; the last seven commits are tasks 1–7 in order.

- [ ] **Step 6: Final commit (only if any verification fixes were needed)**

If Step 4 required fixes, commit them now:

```bash
git add -A
git commit -m "Fix CSS link paths surfaced by smoke test"
```

Otherwise skip — no commit needed.

---

## Done

The repo now satisfies the spec: valid agent skill layout at the root, working promo site, no npm publish path, all docs updated. The follow-up mobile-nav implementation (separate spec at `docs/superpowers/specs/2026-05-21-mobile-site-nav-design.md`) can proceed against `references/readworthy.css` whenever you’re ready.
