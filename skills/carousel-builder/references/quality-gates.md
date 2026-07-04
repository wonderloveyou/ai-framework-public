# Quality Gates

## Structure Gate

- One role per slide.
- No repeated narrative beats.
- Context appears before mechanism/result.
- Final slide is not a duplicate of the previous slide.
- CTA matches the carousel goal.

## Visual Gate

- One dominant visual object per slide.
- Headline readable in 2 seconds.
- Mobile preview remains readable.
- Phone-in-metro test: would the main idea be readable on a moving phone screen?
- Accent color has one meaning.
- No more than 3-4 support blocks unless using an intentional matrix.
- Text does not collide with topbar/footer/decorations.
- Text contrast is sufficient. Use WCAG contrast as a sanity check when colors are uncertain.
- Avoid `rgba(255,255,255,.25)` or similar low-opacity white for meaningful text on dark backgrounds.
- Empty space is intentional; if text-to-space ratio feels dense, split the slide.

## Copy Gate

- No filler intro phrases.
- No paragraphs unless intentional.
- Short headlines beat clever long headlines.
- Each bullet adds new information.
- Remove facts that are only there because they were in the source article.

## Brand Gate

- Colors match brand/profile roles.
- Typography matches profile or platform.
- Tone matches audience.
- Logos/assets are not distorted or overused.
- Images that carry meaning have alt/description text in the source spec when the publishing target supports it.

## Roast Questions

- What is this slide trying to make me believe?
- What can be deleted with no loss?
- Is this a compressed article section instead of a card?
- Would I understand it as a thumbnail?
- Is the final slide actually useful?
- Does any decoration compete with the headline or proof?

## AI Slop Gate (mandatory)

These patterns signal "AI-generated" and must be avoided. If a deck triggers any, rework.

| # | Tell | Fix |
|---|------|-----|
| 1 | Inter as default font | Swap: Geist, Outfit, Satoshi, Cabine Grotesk, or brand font |
| 2 | Purple-to-blue gradient hero | Use brand accent or one deliberate color. No AI-purple gradients. |
| 3 | Gray text on colored/tinted background | Darken text toward background hue, or check WCAG AA (4.5:1) |
| 4 | Cards nested inside cards | One card layer max. Use borders, negative space, or dividers for sub-groups |
| 5 | Pure `#000` or `#fff` | Always off-black (zinc-900/950) or off-white. Pure values kill depth. |
| 6 | Generic glassmorphism (`backdrop-blur` everywhere) | Glass only when motivated by the visual concept. 1-2 slides max per deck. |
| 7 | Tiny uppercase tracking eyebrow on every slide | Max 1 eyebrow per 3-4 slides. Otherwise headline alone is enough. |
| 8 | Centered hero on every slide | Alternate: split screen, left-aligned, asymmetric, full-bleed image |
| 9 | Emoji as visual crutch | Icons from library (Phosphor, Tabler), not emoji. Emoji only if brief is explicitly playful. |
| 10 | One identical entrance animation on every section | Staggered reveals are OK; uniform reflex on every slide is AI grammar |
| 11 | Warm beige/cream bg + brass/oxblood/ochre accent (premium-craft tell) | Rotate palette family: cold luxury, forest+amber, cobalt+cream, terracotta+slate |
| 12 | Bounce/elastic easing in animations | `ease-out` exponent curves only. No bounce, no elastic. |
| 13 | Pairing similar fonts (two geometric sans, two humanist serifs) | Pair on contrast axis: sans + serif, geometric + humanist, wide + narrow |

## Font Pairing Gate (Pomelnikov matrix — parsed from SVG)

Pair on contrast axis. Never two fonts from the same class. One display + one body. Source: Pomelnikov font-pair-table, 108 recommended pairs extracted from 1612-cell matrix.

### System-safe (no loading, always available)

| Headline | Body | Type |
|----------|------|------|
| Segoe UI Bold | Segoe UI Regular | Single-family, Windows-native |
| Arial Black | Georgia | Heavy sans → readable serif |
| Century Gothic | Garamond | Geometric sans → classic serif |
| Century Gothic | Georgia | Geometric sans → screen serif |
| Impact | Arial | Condensed punch → neutral body |

### Google Fonts — curated R-pairs (зелёные ячейки Помельникова)

| Headline | Body | Character |
|----------|------|-----------|
| Fira Sans | Garamond | Modern → classic |
| Fira Sans | Lora | Modern → literary |
| Fira Sans | Oswald | Sans → condensed sans |
| Arial Black | Merriweather | Heavy → readable serif |
| Arial Black | Noto Serif | Heavy → neutral serif |
| Century Gothic | Lora | Geometric → literary |
| Century Gothic | Oswald | Geometric → condensed |
| Century Gothic | Playfair Display | Geometric → elegant |
| Noto Sans | Oswald | Neutral → condensed |
| Noto Sans | Playfair Display | Neutral → elegant |
| Open Sans | Oswald | Workhorse → condensed |
| Open Sans | Playfair Display | Workhorse → elegant |
| Garamond | Merriweather | Classic → readable |
| Garamond | Noto Serif | Classic → neutral |
| Georgia | Noto Serif | Screen → neutral |
| Lora | Merriweather | Literary → readable |
| Lora | Open Sans | Literary → workhorse |
| Cuprum | Noto Sans | Narrow industrial → neutral |
| Exo 2 | Noto Sans | Tech → neutral |
| Arimo | Lora | Neutral sans → literary |
| Arimo | Playfair Display | Neutral sans → elegant |
| Lucida Console | Oswald | Mono → condensed |
| Arial Black | Cuprum | Heavy → narrow |
| Arial Black | Exo 2 | Heavy → tech |

### Pomelnikov rules

1. **Contrast first.** Sans + Serif. Never two sans or two serifs. If single-family, Bold + Regular with strong weight gap.
2. **Weight gap.** Headline 700+, body 400. The bigger the weight difference, the clearer the hierarchy.
3. **x-height match.** Fonts with similar x-height look paired intentionally, not randomly.
4. **Cyrillic mandatory.** Test with: «Съешь ещё этих мягких французских булок, да выпей чаю». If any glyph is missing — font is invalid.
5. **Max 2 weights per family.** Heading weight + body weight. No 5-weight soup.
6. **System fonts first.** For carousels and landing pages, prefer system-safe pairs. Zero load time. Google Fonts only when the design demands it.

## Browser Review

When working with HTML cards, open or render the deck before final handoff when feasible.

Check:

- topbar/footer overlap
- text clipping
- export UI hidden
- output size
- card order
- mobile-scale readability
