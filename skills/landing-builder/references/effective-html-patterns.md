# Effective-HTML Patterns

Upstream reference: [plannotator/effective-html](https://github.com/plannotator/effective-html) (Apache 2.0).

This file documents which effective-html examples are most relevant for landing page building.

## Key Reference Files

| File | Relevance |
|------|-----------|
| `index.html` | Hero pattern, card grid, TOC pills, SVG thumbnails |
| `05-design-system.html` | Color swatches, type scale, radius/shadow tokens, button components |
| `06-component-variants.html` | 6 card variant treatments with live controls |
| `14-research-feature-explainer.html` | Sticky sidebar, collapsible details, tabbed code, FAQ, callout boxes |
| `02-exploration-visual-designs.html` | Empty state variants, dark/light artboard toggle |

## Hero Pattern (from index.html)

- Eyebrow with `::before` decorative line
- Fluid heading: `clamp(38px, 5.4vw, 62px)` on serif
- Two-column grid: text + visual (collapses at 880px)
- TOC as pill navigation: `border-radius: 999px` links with counter badges

## Card Grid (from index.html)

- `grid-template-columns: repeat(auto-fill, minmax(316px, 1fr))`
- Each card: SVG thumbnail (132px) → body (title + description) → footer
- Hover: `translateY(-3px)`, box-shadow, border darkens
- SVG thumbnails with class-based coloring

## Card Variant Matrix (from 06-component-variants.html)

Six treatments:
- **Flat**: no border, no shadow — for dense lists
- **Outlined**: `1.5px solid var(--gray-300)` — default content cards
- **Elevated**: shadow — hero cards, featured items
- **Accent stripe**: `::before` pseudo with 4px color bar — priority items
- **Inset**: oat background — nested inside white panels
- **Horizontal**: flex row — compact lists, sidebars

## Feature Explainer (from 14-research-feature-explainer.html)

- Sticky sidebar TOC (200px) + main content
- TL;DR box with left accent border
- Collapsible `<details>` with custom markers
- Tabbed code samples
- Callout boxes: flex row with icon + text
- FAQ: definition list with serif questions
