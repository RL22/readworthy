# Readworthy Components

Per-class reference for the Readworthy render layer. Pair with `references/readworthy.css` and `references/compatibility.md`.

Each entry lists the class, its purpose, a minimal markup snippet, and any usage notes. Entries are short by design — keep one component's entry under 80 lines.

---

## `.doc`

**Purpose.** The body-level class that activates the Readworthy render layer, setting background, foreground, font family, line-height, and responsive padding.

**Markup.**

```html
<body class="doc">
  <!-- ... -->
</body>
```

**Notes.** Must be on `<body>`. All other Readworthy components assume `.doc` is present on an ancestor.

---

## `.doc-hero`

**Purpose.** A centered, max-width header block used at the top of a document to hold the eyebrow label, page title, and lede.

**Markup.**

```html
<header class="doc-hero">
  <p class="eyebrow">Architecture Spec</p>
  <h1>Page Title</h1>
  <p class="lede">Short summary sentence.</p>
</header>
```

**Notes.** Constrained to `--rw-measure` (72ch) and centered with `margin-inline: auto`. Typically contains `.eyebrow`, an `<h1>`, and `.lede`.

---

## `.prose`

**Purpose.** The main reading column — constrains content width, spaces headings and paragraphs, and styles inline code, tables, and code blocks.

**Markup.**

```html
<main class="prose">
  <h2>Section Title</h2>
  <p>Body copy goes here.</p>
  <pre><code>code example</code></pre>
</main>
```

**Notes.** Constrained to `--rw-measure`. Wrap the page's primary body content in a single `.prose` container; nest component sections inside it.

---

## `.toc`

**Purpose.** A horizontal pill-link navigation bar used for table-of-contents or site-level nav links.

**Markup.**

```html
<nav class="toc" aria-label="Table of contents">
  <a href="#overview">Overview</a>
  <a href="#components">Components</a>
  <a href="#examples">Examples</a>
</nav>
```

**Notes.** Links share the same pill style as `.badge`. Add `aria-label` for accessibility. Use `class="toc site-nav"` for the persistent site-level nav variant.

---

## `.badge`

**Purpose.** A small monospaced pill for version labels, status tags, model compatibility indicators, and short metadata.

**Markup.**

```html
<span class="badge">Beta</span>
<span class="badge">GPT-5 Ready</span>
```

**Notes.** Shares the same border-radius and monospaced type as `.toc` links. Suitable inline alongside headings or body text.

---

## `.eyebrow`

**Purpose.** An all-caps, monospaced label displayed above a heading to identify the document type or section category.

**Markup.**

```html
<p class="eyebrow">Video Summary</p>
<h1>Rethinking How We Build</h1>
```

**Notes.** Rendered in the accent color with tight letter-spacing. Use once per document or section header — not for repeated labeling in body copy.

---

## `.lede`

**Purpose.** A slightly enlarged, muted paragraph used immediately after the page `<h1>` to deliver the document's one-sentence summary.

**Markup.**

```html
<p class="lede">
  A two-phase CLI for auditing Markdown documentation ecosystems.
</p>
```

**Notes.** Shares its `font-size: 1.12rem` and muted color with `.principle`. Use once per document, inside `.doc-hero`.

---

## `.principle`

**Purpose.** A pull-quote-style paragraph that highlights a key insight or design rule inline within body copy.

**Markup.**

```html
<p class="principle">
  <strong>The CLI is the engine.</strong> The skill is the interface.
</p>
```

**Notes.** Same muted, enlarged style as `.lede`, but intended for use inside `.prose` sections — not in the document header. Use `<strong>` for the bolded lead-in phrase.

---

## `.note`

**Purpose.** A supplemental aside for low-priority context, best practices, or optional guidance that should not interrupt reading flow.

**Markup.**

```html
<aside class="note">
  <p>Token estimation falls back to a characters-divided-by-4 heuristic on Node 16.</p>
</aside>
```

**Notes.** Rendered in the muted foreground color. Use `<aside>` as the semantic element. Tone should be informational and calm.

---

## `.callout`

**Purpose.** A short emphasized line break used to flag the headline takeaway of a section.

**Markup.**

```html
<section class="callout">
  <strong>Short answer:</strong> Readworthy works today as a reading layer.
</section>
```

**Notes.** Sits at `font-size: 1.05rem` with a hairline top border. Use sparingly — one or two per document.

---

## `.warning`

**Purpose.** A high-visibility block for critical caveats, breaking changes, security concerns, or operations that must not proceed without review.

**Markup.**

```html
<section class="warning">
  <h3>Unrecoverable errors exit non-zero</h3>
  <p>Do not expose API keys in public repos.</p>
</section>
```

**Notes.** Rendered in the warning color (`--rw-warn`). Reserve for genuinely critical information — overuse reduces impact.

---

## `.example`

**Purpose.** A tinted, rounded block for concrete demonstrations: code snippets, HTML patterns, prompt examples, or API requests.

**Markup.**

```html
<section class="example">
  <pre><code>npm install readworthy</code></pre>
</section>
```

**Notes.** Receives a subtle surface tint and `border-radius`. Use to visually separate a demo from surrounding prose without the severity of `.warning`.

---

## `.spec-card`

**Purpose.** A self-contained tinted card for defining a system component, CLI command, API endpoint, or feature spec.

**Markup.**

```html
<section class="spec-card">
  <span class="badge">Read-only</span>
  <h3>readworthy init</h3>
  <p>Audits the repository and outputs a migration plan.</p>
</section>
```

**Notes.** Commonly used inside `.card-grid` for multi-column layouts. Receives the same surface tint and border-radius as `.example`.

---

## `.agent-instruction`

**Purpose.** A monospaced block for operational directives that define how an agent should behave, sequence tasks, or use tools.

**Markup.**

```html
<section class="agent-instruction">
  <p>Always run readworthy init before convert. Do not run convert without user approval.</p>
</section>
```

**Notes.** Rendered in `--rw-mono` at `0.95rem`. Distinct from `.prompt-block`: agent instructions define execution logic; prompt blocks hold reusable prompt text.

---

## `.validation-rule`

**Purpose.** A tinted block for formal output constraints, QA requirements, accessibility checks, or schema validation rules.

**Markup.**

```html
<section class="validation-rule">
  <h3>One h1 per page</h3>
  <p>Each generated HTML file must have exactly one <code>h1</code> element.</p>
</section>
```

**Notes.** Shares surface tint and border-radius with `.example` and `.spec-card`. Useful in automated review pipelines and agent-readable specs.

---

## `.prompt-block`

**Purpose.** A formatted container for reusable system prompts, agent templates, or Codex skill definitions.

**Markup.**

```html
<section class="prompt-block">
  <h3>Example invocation prompt</h3>
  <pre>You are a frontend architect.
Summarize this transcript as semantic HTML.</pre>
</section>
```

**Notes.** The `<pre>` inside `.prompt-block` is rendered in `--rw-mono` with `white-space: pre`. Use for prompt text that agents or humans will copy and reuse — not for arbitrary code.

---

## `.timestamp`

**Purpose.** A section marking a discrete moment in a timeline, transcript, or video summary — groups a time value, heading, and supporting notes.

**Markup.**

```html
<section class="timestamp">
  <span class="badge">Context</span>
  <time>00:00</time>
  <h3>The gap nobody names</h3>
  <p>Opening remarks establishing the problem space.</p>
</section>
```

**Notes.** Receives a hairline top border and block padding. Use a `<time>` element for the time value and pair with `.badge` for a topic label.

---

## `.checklist`

**Purpose.** An unstyled ordered list that prepends a bold accent-colored checkmark to each item, used for procedural steps or task-oriented flows.

**Markup.**

```html
<ul class="checklist">
  <li>Install dependencies</li>
  <li>Configure tokens</li>
  <li>Run validation</li>
</ul>
```

**Notes.** `list-style` is removed; the `✓` marker is injected via `::before`. Use `<ul>` when order is not critical, `<ol>` when sequence matters (both receive the same styling).

---

## `.file-tree`

**Purpose.** A dark-background monospaced block for rendering project directory structures with ASCII tree characters.

**Markup.**

```html
<pre class="file-tree">.readworthy/
├── plan.json
├── backups/
└── logs/</pre>
```

**Notes.** Shares the dark code surface (`--rw-code-bg`) with `.prose pre`. Use `<pre>` directly with the class — no inner `<code>` needed.

---

## `.card-grid`

**Purpose.** A responsive multi-column grid container for laying out `.spec-card` or other card-type children side by side.

**Markup.**

```html
<section class="card-grid">
  <section class="spec-card">
    <h3>Card One</h3>
    <p>Description.</p>
  </section>
  <section class="spec-card">
    <h3>Card Two</h3>
    <p>Description.</p>
  </section>
</section>
```

**Notes.** Uses `grid-template-columns: repeat(auto-fit, minmax(14rem, 1fr))` — columns reflow to a single column at narrow widths automatically.

---

## `.status-grid`

**Purpose.** A responsive grid container for compatibility tables built from `.status` pill children, typically pairing an environment name with a status pill.

**Markup.**

```html
<section class="status-grid">
  <div>Node 20 LTS <span class="status supported">supported</span></div>
  <div>Node 16 <span class="status partial">partial</span></div>
  <div>Windows <span class="status required">required</span></div>
</section>
```

**Notes.** Shares the same grid definition as `.card-grid`. Intended for compatibility overviews; `.status` pills inside a `<table>` are also common.

---

## `.status`

**Purpose.** An inline pill that conveys a compatibility or support state for an environment, feature, or platform.

**Markup.**

```html
<span class="status supported">supported</span>
```

**Notes.** Always combine `.status` with one modifier class:
- `supported` — green-on-accent tint; indicates full support.
- `partial` — muted on surface tint; indicates partial or limited support.
- `required` — amber-on-warn tint; indicates a dependency or prerequisite.
- `neutral` — muted on surface tint; same visual as `partial`, used for informational or N/A states.

---

## `.compact-list`

**Purpose.** A standard list with reduced left padding, suitable for tight layouts where the default browser indent is too wide.

**Markup.**

```html
<ul class="compact-list">
  <li>First item</li>
  <li>Second item</li>
</ul>
```

**Notes.** Sets `padding-left: 1.1rem`. Retains browser default list markers. Use when standard `<ul>` indentation feels excessive within a card or narrow column.
