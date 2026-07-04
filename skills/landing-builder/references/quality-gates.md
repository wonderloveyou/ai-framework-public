# Quality Gates

## Structure Gate

- One goal per landing. Every section serves that goal.
- Hero communicates value in 3 seconds or less.
- Sections flow: problem -> solution -> proof -> action.
- CTA appears at least twice: after hero and at bottom.
- No section repeats the previous section's message.

## Visual Gate

- Dark mode works on every element. Toggle and verify.
- Theme toggle is visible but not dominant.
- Headlines readable at 375px width.
- One dominant visual per section.
- Accent color has one meaning across the page.
- Text contrast meets WCAG AA (4.5:1 for body, 3:1 for large text).
- No `rgba(255,255,255,.25)` for meaningful text on dark backgrounds.
- Empty space is intentional, not accidental.
- Mobile: no horizontal scroll, no overlapping elements.

## Copy Gate

- No filler intro phrases ("In today's world...", "We all know that...").
- Headlines are claims, not descriptions.
- Subtitle explains the headline, doesn't repeat it.
- CTA is an action verb, not "Submit" or "Click here".
- No paragraphs over 3 lines without a visual break.
- Each section adds new information.

## Brand Gate

- Colors match brand/profile or effective-html palette.
- Typography matches audience tone.
- No more than 2 font families in use (serif headings + sans body).
- SVG assets are not distorted.

## Performance Gate

- Single file, no external dependencies.
- No webfont loading (system fonts only).
- SVG inline, no external images unless essential.
- Total file under 200KB.

## Roast Questions

- What is this page trying to make me do?
- Can the hero be understood in 3 seconds?
- Does the CTA feel inevitable or forced?
- What section can be deleted with no loss?
- Would this convince someone who just landed here from search?
- Is there a dark mode flash? (Must be prevented by apply-before-paint script.)

## AI Slop Gate (from carousel-builder)

| # | Tell | Fix |
|---|------|-----|
| 1 | Inter as default font | System font stack only |
| 2 | Purple-to-blue gradient hero | Brand accent or one deliberate color |
| 3 | Gray text on colored background | WCAG AA check |
| 4 | Cards nested inside cards | One card layer max |
| 5 | Pure `#000` or `#fff` | Off-black (zinc-900) or off-white |
| 6 | Generic glassmorphism everywhere | Glass only when motivated |
| 7 | Tiny uppercase eyebrow on every section | Max 1 eyebrow per 3 sections |
| 8 | Centered layout on every section | Alternate: split, left-aligned, asymmetric |
| 9 | Emoji as visual crutch | SVG icons or none |
| 10 | Bounce/elastic animations | ease-out exponent curves only |
