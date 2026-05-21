# Mobile Site Nav — Design Spec

**Date:** 2026-05-21
**Status:** Approved (pending user review of this doc)
**Scope:** Readworthy CSS library + site pages (`index.html`, `guide.html`, and any `examples/` page using site nav)

## Problem

The Readworthy site (`index.html`, `guide.html`) uses `<nav class="toc">` rendered as a horizontal flex row of pill links. On narrow viewports the pills wrap onto multiple lines, consuming vertical space above the hero. There is no proper collapsed mobile navigation.

Readworthy is itself a CSS library; the fix should be added to `readworthy.css` as a reusable pattern so library consumers benefit, then applied on the site.

## Goals

- Provide a collapsible mobile navigation pattern that lives in `readworthy.css`.
- Apply the pattern on the Readworthy site’s cross-page navigation only.
- Stay JS-free, semantic, accessible by default (WCAG AA).
- Preserve the existing desktop appearance (horizontal pill row).

## Non-goals

- In-page tables of contents (`<nav class="toc">` inside articles) are out of scope and keep their current wrapping-pill behavior.
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

### Rules

```css
/* Mobile-first: collapsed disclosure */
.site-nav {
  display: block;
  margin-block: 2rem;
}

.site-nav > summary {
  /* Reuse existing pill styling */
  border: 1px solid var(--rw-border);
  border-radius: 999px;
  color: var(--rw-muted);
  cursor: pointer;
  display: inline-flex;
  align-items: center;
  gap: .4rem;
  font-family: var(--rw-mono);
  font-size: .78rem;
  list-style: none;            /* kill default disclosure triangle */
  padding: .35rem .75rem;
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

.site-nav > summary:hover {
  border-color: var(--rw-accent);
  color: var(--rw-fg);
}

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
2. **`guide.html`** — replace the top `<nav class="toc site-nav">` with the new structure. Keep the inline `style="margin-bottom: 0;"` removal handled via CSS instead, or leave inline if simpler. The in-page `<nav class="toc" aria-label="Table of contents">` below the hero is unchanged.
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

## Out-of-scope follow-ups

- Add a "current page" indicator on `<summary>` (e.g. "Menu — Guide").
- Document the `.site-nav` pattern in `guide.html` Components section.
- Extend pattern to in-page TOCs as opt-in modifier.
