---
name: carousel-builder
description: Create or review social carousels, article covers, HTML card decks, visual briefs, batch image systems, and platform exports.
---

# Carousel Builder

## Quick Start

1. Pick `platform + format + brand/profile`.
2. Map slides as `role -> visual object -> short copy`.
3. Render or review, then run `references/quality-gates.md`.

## Resource Map

- `config.json` - available profiles, examples, engines, and reference files.
- `references/pipeline.md` - end-to-end flow, reference intake, batch/CSV automation.
- `references/platform-formats.md` - platform sizes, slide counts, safe zones.
- `references/narrative-and-patterns.md` - roles, card patterns, JSON brief schema.
- `references/html-export.md` - artboards, templates, export, HTML/CSS guardrails.
- `references/effective-html-patterns.md` - design tokens, dark mode, scroll-snap deck, card variants, slide content patterns (from plannotator/effective-html).
- `references/quality-gates.md` - final checks, roast pass, contrast/mobile/readability.
- `profiles/velmi.md` - Velmi AI design profile.
- `profiles/fonts.md` - Personal font preferences by use case (landings, memes, reels).
- `examples/bitrix-ai-agent.md` - reference case from the successful Bitrix carousel.

## HTML Shell Requirements

Every carousel deck must include:

1. **Design tokens** as CSS custom properties on `:root` (see `effective-html-patterns.md`).
2. **Dark mode** via `html.dark` class + localStorage + apply-before-paint script in `<head>`.
3. **Scroll-snap** for native swipe: `scroll-snap-type: y mandatory` on body, `scroll-snap-align: start` on each slide.
4. **Keyboard navigation**: ArrowRight/Down/Space forward, ArrowLeft/Up backward.
5. **Slide counter**: IntersectionObserver updating `N / total` fixed in bottom-right.
6. **SVG through CSS variables**: never hard-coded hex in SVG elements.

## Workflow

1. Identify target:
   - platform and aspect ratio
   - slide count
   - source: topic, article, rough draft, CSV, existing HTML
   - brand/profile
2. Use the smallest needed reference set:
   - structure task: `narrative-and-patterns.md`
   - HTML/export task: `html-export.md` + `effective-html-patterns.md`
   - platform task: `platform-formats.md`
   - bulk automation: `pipeline.md`
   - final review: `quality-gates.md`
   - Velmi work: `profiles/velmi.md`
3. Build in order:
   - structure
   - visual brief
   - render/export
   - brand/style QA
   - roast review
4. Keep one job per slide. Split or simplify if a slide carries hook, proof, process, and conclusion at once.
5. Treat overflow as a strategy issue first. Delete, rewrite, or restructure before shrinking fonts.
6. Keep card count, visible labels, buttons, filenames, ZIP/export logic, and platform variants synchronized.
7. After a reusable lesson, update the smallest relevant file: global reference, profile, or example.

## Slide Content Patterns

From effective-html (see `effective-html-patterns.md` for CSS):

| Pattern | Use for |
|---------|---------|
| Ship list | Achievements, features, deliverables (dot + title + desc + ref) |
| Progress bars | Status updates, roadmap progress (track + fill + label + note) |
| Metrics grid | Big numbers with deltas (2-column, 52px serif values) |
| Decision card | Choices, options, polls (bordered card + chip options) |
| Next/On deck | Upcoming items (serif list with clay dashes) |
| Inverted slide | Section breaks, emphasis (slate bg, ivory text) |

## Card Variants (from effective-html)

When building card-based slides, pick from the 6-variant system:

| Variant | When |
|---------|------|
| Flat | Dense lists on tinted backgrounds |
| Outlined | Default content cards on ivory |
| Elevated | Hero cards, featured items |
| Accent stripe | Pinned/priority items |
| Inset | Nested cards inside white panels |
| Horizontal | Compact row lists, sidebars |

## Agent Roles

- **Structure agent**: source material -> slide roles and concise copy.
- **Visual brief agent**: composition, palette roles, typography, visual object.
- **Renderer agent**: HTML/CSS, image prompts, or template/export scripts.
- **Brand enforcer**: profile compliance and visual consistency.
- **Roast reviewer**: clutter, duplication, weak hook/final, unreadable text.

## Core Rule

```text
audience -> platform -> promise -> slide roles -> visual object -> copy -> render -> QA
```

Do not start from decoration.

