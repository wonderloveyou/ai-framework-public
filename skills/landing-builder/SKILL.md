---
name: landing-builder
description: Create or review landing pages, hero sections, feature grids, CTA blocks, and single-page HTML sites. Uses effective-html design tokens, dark mode, and self-contained HTML output.
---

# Landing Builder

## Quick Start

1. Define `audience + goal + sections`.
2. Pick design tokens and brand profile.
3. Build HTML shell -> sections -> QA with `references/quality-gates.md`.

## Resource Map

- `references/design-tokens.md` - CSS custom properties, dark mode recipe, font stack.
- `references/section-patterns.md` - hero, feature grid, CTA, FAQ, empty states, footer.
- `references/html-shell.md` - single-file HTML shell, meta tags, responsive breakpoints.
- `references/quality-gates.md` - structure, visual, copy, brand, and roast checks.
- `references/effective-html-patterns.md` - upstream patterns from plannotator/effective-html.

## HTML Shell Requirements

Every landing page must include:

1. **Design tokens** as CSS custom properties on `:root` (see `design-tokens.md`).
2. **Dark mode** via `html.dark` class + localStorage + apply-before-paint script in `<head>`.
3. **Theme toggle** button (top-right, fixed or in nav).
4. **Self-contained** single `.html` file — no external CSS, no build step, no framework.
5. **Responsive**: breakpoints at 960px (2-col -> 1-col) and 640px (mobile).
6. **SVG through CSS variables**: never hard-coded hex in SVG elements.

## Workflow

1. Define landing structure:
   - audience and conversion goal
   - sections: hero, features, social proof, pricing, FAQ, CTA, footer
   - brand/profile
2. Use reference files:
   - shell/setup: `html-shell.md` + `design-tokens.md`
   - section patterns: `section-patterns.md`
   - final review: `quality-gates.md`
3. Build in order:
   - HTML shell + tokens + dark mode
   - sections top-to-bottom
   - responsive QA
   - roast review
4. One job per section. If a section does two things, split it.
5. Mobile-first: check every section at 375px width before finalizing.
6. After a reusable lesson, update the smallest relevant reference file.

## Section Catalog

| Section | Use for |
|---------|---------|
| Hero | Eyebrow + fluid heading + subtitle + CTA + visual |
| Feature grid | 3-6 feature cards in auto-fill grid |
| How it works | Numbered steps, collapsible details, or tabbed explainer |
| Social proof | Logos, testimonials, metrics grid |
| Comparison | Before/after, VS table, feature matrix |
| Pricing | Tier cards with accent stripe for recommended |
| FAQ | Collapsible details with serif questions |
| CTA block | Full-width accent stripe with primary + ghost buttons |
| Footer | Links, copyright, theme toggle |

## Card Variants (from effective-html)

When building cards inside sections, pick from the 6-variant system:

| Variant | When |
|---------|------|
| Flat | Dense lists on tinted backgrounds |
| Outlined | Default content cards on ivory |
| Elevated | Hero cards, featured items, pricing tiers |
| Accent stripe | Pinned/priority items, recommended tier |
| Inset | Nested cards inside white panels |
| Horizontal | Compact row lists, sidebars |

## Agent Roles

- **Structure agent**: audience -> goal -> section sequence -> copy outline.
- **Visual brief agent**: composition, palette roles, typography per section.
- **Renderer agent**: HTML/CSS single-file output.
- **Brand enforcer**: profile compliance and visual consistency.
- **Roast reviewer**: clutter, duplication, weak hero/CTA, unreadable text.

## Core Rule

```text
audience -> goal -> sections -> visual brief -> render -> QA
```

Do not start from decoration. Every section must serve the conversion goal.
