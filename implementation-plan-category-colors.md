# Category Colors — Implementation Plan

## Goal

Concentrate all color inside prompt cards. Everything outside cards (hero heading, nav, footer links, search input) becomes neutral. Each category gets its own color that drives the tag, input underlines, glow animation, and any other accent inside that card.

---

## Current Color Touchpoints

**Outside cards (→ make neutral):**
- Hero h1: "Prompt." span is `var(--accent)` cyan
- Hero radial glow background
- Search input placeholder color
- Text selection highlight (`::selection`)
- Footer link hover color
- CSS `--accent` used globally

**Inside cards (→ per-category color):**
- Category badge/tag text color
- Template input underline (focus + filled states)
- Copy button color
- Card border glow animation (3-layer conic gradients using `var(--accent)`)
- Card inner glow inset layer
- Hover lift + glow activation

---

## Categories & Prompt Count

| Category        | Prompts |
|-----------------|---------|
| Planning        | 6       |
| Governance      | 2       |
| Design System   | 4       |
| Content         | 2       |
| Performance     | 1       |
| Troubleshooting | 2       |

---

## Phases

### Phase 1 — Color Palette Proposal
**Checkpoint: review before any code changes.**

Propose a color for each category. Requirements:
- Distinct from each other at a glance
- WCAG AA contrast on both light and dark backgrounds
- Works for text, borders, and translucent glows
- Feels cohesive as a set — not random

Deliverable: a visual swatch artifact showing each category with its proposed color in light and dark mode, applied to a sample tag and glow preview.

---

### Phase 2 — Token Architecture
**Checkpoint: review token names and structure.**

Define per-category CSS custom properties. Likely structure:

```
--cat-planning:          hsl(...)
--cat-planning-muted:    hsla(...)
--cat-planning-bg:       hsla(...)
--cat-planning-border:   hsla(...)
```

Repeat for each category. These mirror the existing `--accent` / `--accent-muted` / `--accent-bg` / `--accent-border` pattern but scoped per category.

Deliverable: updated `:root` and `.dark` token blocks.

---

### Phase 3 — Neutralize Exterior
**Checkpoint: verify nothing outside cards uses color.**

Changes:
- Hero h1 "Prompt." span → `var(--foreground)` or `var(--muted-strong)`
- Hero radial glow → remove or make neutral gray
- Search placeholder → neutral muted color
- `::selection` → neutral highlight
- Footer link hover → `var(--foreground)`
- Global `--accent` becomes a fallback only, no longer drives visible UI outside cards

Deliverable: updated component + screenshot for review.

---

### Phase 4 — Wire Category Colors into Cards
**Checkpoint: verify cards reflect their category color.**

Each `PromptCard` receives its category color and applies it to:
- Tag/badge text and background tint
- Template input underline (`borderBottom` on focus/filled)
- Copy button icon color
- Any accent text inside the card

Implementation approach: pass category color tokens as inline CSS variables on the card wrapper (e.g. `style={{ '--card-accent': 'var(--cat-planning)' }}`), then reference `var(--card-accent)` throughout the card internals.

Deliverable: updated component + screenshot for review.

---

### Phase 5 — Category-Colored Glow Animation
**Checkpoint: final visual review.**

Update the 3-layer conic gradient glow to use `var(--card-accent)` instead of the global `var(--accent)`. This means:
- `.card-glow-wrap` border gradients use the per-card color
- `.card-glow-inset` inner glow uses the per-card color
- Glow breathe/spin animations remain unchanged (they animate angles, not colors)

Deliverable: updated component with hover/focus glow in category colors. Final review before Netlify deploy.

---

### Phase 6 — Sync & Deploy
- Update `index.html` with final component
- Update `globals.css` with new tokens
- Verify both light and dark mode
- Ship to Netlify
