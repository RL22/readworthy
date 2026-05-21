# Mobile Site Nav — Design Spec

**Date:** 2026-05-21
**Status:** Approved (pending user review of this doc)
**Scope:** Readworthy CSS library + site pages (`index.html`, `guide.html`, and any `examples/` page using site nav). Also restyles in-page `.toc` (table-of-contents) links to text-only and tightens `guide.html`’s page TOC.

## Problem

The Readworthy site (`index.html`, `guide.html`) uses `<nav class="toc">` rendered as a horizontal flex row of pill links. On narrow viewports the pills wrap onto multiple lines, consuming vertical space above the hero. There is no proper collapsed mobile navigation.

Readworthy is itself a CSS library; the fix should be added to `readworthy.css` as a reusable pattern so library consumers benefit, then applied on the site.

## Goals

- Provide a collapsible mobile navigation pattern that lives in `readworthy.css`.
- Apply the pattern on the Readworthy site’s cross-page navigation only.
- Stay JS-free, semantic, accessible by default (WCAG AA).
- Preserve the existing desktop appearance of the site nav (horizontal pill row).
- Restyle in-page `.toc` (table-of-contents) links as plain text — no border, no pill.
- Drop the redundant "← Home" link from `guide.html`’s page-level TOC.

## Non-goals

- In-page `.toc` collapse on mobile — they remain visible (text links wrap).
- Off-canvas / overlay / animated drawer menus.
- Theming or alternate visual styles (tracked separately).
- Any JavaScript.

## Approach

Use the native HTML `<details>` / `<summary>` disclosure element. Below the breakpoint, the nav collapses to a single "Menu" pill that expands a vertical link list when tapped. At and above the breakpoint, the disclosure is forced open and the summary is hidden, restoring the existing horizontal pill row.

### Why `<details>` over alternatives

- **Zero JS, zero state management** — open/closed state handled by the browser.
- **Native a11y** — `<summary>` exposes a button role, `aria-expanded`, and is keyboard-operable (Enter / Space).
- **Semantic** — matches Readworthy’s "information before decoration" principle.
- **Progressive** — works without CSS (still a usable disclosure).

Rejected: CSS checkbox hack (non-semantic, focus-trap quirks). Hamburger overlay (visually heavier, off-brand). Horizontal scroll (doesn’t actually solve the discoverability problem).

## Markup

Consumer-facing pattern, replacing the current inline pill row for site navigation:

```html
<nav aria-label="Site navigation">
  <details class="toc site-nav">
    <summary>Menu</summary>
    <a href="index.html">Home</a>
    <a href="guide.html" aria-current="page">Guide</a>
    <a href="examples/">Examples</a>
    <a href="caniuse-html-agent-docs.html">Compatibility</a>
    <a href="https://github.com/RL22/readworthy">GitHub</a>
  </details>
</nav>
```

Notes:
- `<details class="toc site-nav">` replaces the flex container previously played by `<nav class="toc">`.
- The outer `<nav aria-label="…">` preserves the landmark for assistive tech (since `<details>` is not a landmark).
- `aria-current="page"` is preserved on the active link.
- The `.toc` class is kept on `<details>` so existing link styles (pill, mono font, border, hover) continue to apply to the anchor children unchanged.

## CSS additions to `readworthy.css`

### New token

Add to `:root`:

```css
--rw-nav-breakpoint: 40rem; /* 640px */
```

### In-page `.toc` restyle (text-only)

The existing rule `.toc a, .badge { border: 1px solid var(--rw-border); border-radius: 999px; … }` applies the pill look to every `.toc` link. The pill belongs to the site nav, not to in-page TOCs. Scope the pill to `.site-nav` + `.badge`, and add a text-only treatment for in-page `.toc a`.

Replace:

```css
.toc a,
.badge { /* …pill rule… */ }

.toc a:hover { /* …pill hover… */ }
```

With:

```css
.site-nav a,
.site-nav > summary,
.badge {
  border: 1px solid var(--rw-border);
  border-radius: 999px;
  color: var(--rw-muted);
  display: inline-block;
  font-family: var(--rw-mono);
  font-size: .78rem;
  padding: .25rem .6rem;
}

.site-nav a:hover,
.site-nav > summary:hover {
  border-color: var(--rw-accent);
  color: var(--rw-fg);
}

/* In-page TOC: text only (excludes site nav) */
.toc:not(.site-nav) a {
  color: var(--rw-muted);
  font-family: var(--rw-mono);
  font-size: .78rem;
  text-decoration: none;
}

.toc:not(.site-nav) a:hover {
  color: var(--rw-accent);
  text-decoration: underline;
}

.toc:not(.site-nav) a[aria-current="page"] {
  color: var(--rw-fg);
}
```

Using `:not(.site-nav)` keeps the two patterns cleanly separated and removes any rule-order dependency.

### Rules

```css
/* Mobile-first: collapsed disclosure */
.site-nav {
  display: block;
  margin-block: 2rem;
}

/* Disclosure-specific additions for <summary>.
   Pill border/radius/color/font already applied via the shared rule above. */
.site-nav > summary {
  align-items: center;
  cursor: pointer;
  display: inline-flex;
  gap: .4rem;
  list-style: none;            /* kill default disclosure triangle */
  padding: .35rem .75rem;      /* override shared padding for tap target */
}

.site-nav > summary::-webkit-details-marker { display: none; }

.site-nav > summary::after {
  content: "▾";
  display: inline-block;
  transition: transform .15s ease;
}

.site-nav[open] > summary::after { transform: rotate(180deg); }

.site-nav > summary:focus-visible {
  outline: 2px solid var(--rw-accent);
  outline-offset: 2px;
}

/* hover state shared with .site-nav a — defined in the pill rule above */

/* Open state: links stack vertically below summary */
.site-nav[open] {
  display: flex;
  flex-direction: column;
  gap: .5rem;
  align-items: flex-start;
}

/* Desktop: force-open horizontal pill row, hide summary */
@media (min-width: 40rem) {
  .site-nav,
  .site-nav[open] {
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    gap: .5rem;
    align-items: center;
  }
  .site-nav > summary { display: none; }
}

/* Reduced motion */
@media (prefers-reduced-motion: reduce) {
  .site-nav > summary::after { transition: none; }
}
```

### Behavior matrix

| Viewport | State | Result |
|---|---|---|
| < 640px, closed | default | `Menu ▾` pill only |
| < 640px, open  | user tapped | `Menu ▾` (rotated 180°) pill + vertical link list below |
| ≥ 640px        | any | Horizontal pill row; summary hidden |

## Accessibility

- Outer `<nav aria-label="Site navigation">` provides the navigation landmark.
- `<summary>` is natively focusable, keyboard-operable (Enter / Space), and exposes `aria-expanded` automatically.
- `aria-current="page"` preserved on the active link.
- Color contrast inherits from existing tokens (already WCAG AA in the base theme).
- Chevron rotation respects `prefers-reduced-motion: reduce`.
- Hit target on `<summary>` is ≥ 24×24 CSS px at the chosen padding.

## Site changes

1. **`index.html`** — wrap the current `<nav class="toc">` links in the new `<nav> > <details class="toc site-nav">` structure. Add `aria-current="page"` on the Home link.
2. **`guide.html`** — (a) replace the top `<nav class="toc site-nav">` with the new disclosure structure; (b) remove the `<a href="index.html">← Home</a>` entry from the in-page `<nav class="toc" aria-label="Table of contents">` (the site nav already covers Home). The remaining in-page TOC links render text-only via the new CSS rule.
3. **`examples/index.html`** — apply the same change (confirmed: has `<nav class="toc site-nav">` at line 11).
4. **`caniuse-html-agent-docs.html`** — apply the same change (confirmed: has `<nav class="toc site-nav">` at line 11).

## Risks / open questions

- `<details>` styling has minor cross-browser quirks (Safari summary marker). Mitigated by `list-style: none` + `::-webkit-details-marker { display: none }`.
- If a consumer puts a `.site-nav` outside a `<nav>` landmark, they lose the landmark. The library docs should call out the wrapping `<nav aria-label="…">` requirement.

## Testing

- Manual: resize index.html and guide.html across 320px → 1280px, confirm pill row ↔ disclosure swap at 640px.
- Keyboard: Tab to summary, Enter to expand, Tab through links.
- Screen reader: VoiceOver / NVDA announce "Menu, collapsed/expanded button".
- Reduced-motion preference: confirm no chevron transition.
- Visual regression: walk every page with a non-site `.toc` (index, guide, examples/*, research/*, caniuse-html-agent-docs.html) and confirm in-page TOC renders as text links — no pill border — and the site nav still pills correctly.

## Out-of-scope follow-ups

- Add a "current page" indicator on `<summary>` (e.g. "Menu — Guide").
- Document the `.site-nav` pattern in `guide.html` Components section.
- Extend pattern to in-page TOCs as opt-in modifier.
