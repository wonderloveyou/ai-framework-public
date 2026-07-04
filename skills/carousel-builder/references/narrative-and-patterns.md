# Narrative And Card Patterns

## Roles

Assign roles before designing.

- **cover/hook**: reason to open.
- **context**: situation, system, audience, or stakes.
- **problem**: pain, failure mode, misconception, or bottleneck.
- **mechanism**: what changed or how it works.
- **proof/result**: metric, example, screenshot, quote, or before/after.
- **mistake -> fix**: common wrong move and correction.
- **checklist/action**: steps the reader can use.
- **takeaway/final**: non-duplicative principle or CTA.

## Default Structures

Educational:

```text
hook -> mistake/problem -> principle -> examples -> checklist -> CTA
```

Case study:

```text
hook -> context -> problem -> intervention -> result -> transferable lesson
```

Opinion/POV:

```text
contrarian hook -> why common view fails -> better model -> proof -> implication
```

Product/feature:

```text
pain -> new behavior -> how it works -> example -> result -> next action
```

## Storytelling Framework

These structures apply to the *narrative* across slides, not just individual slide composition.

### Hero's Journey for Carousels (7 beats)

Adapted from storytelling for blogs. Compact enough for 5-10 slides.

1. **Status Quo** — where the hero is before the story: «I was a sales manager, normal stable job»
2. **The Call / Problem** — something breaks: «after childbirth, my body fell apart»
3. **Resistance / Doubt** — the hero resists change: «I'm not qualified», «this is a bad time», «what if it fails?»
4. **The Turning Point** — an external push or realization forces action
5. **The Pit / Failure** — first attempt fails. Critical beat: without it, the story is flat. «The key employee quit.»
6. **The Sword / Solution** — finding the mechanism that actually works
7. **Victory + Transferable Principle** — result with numbers + one reusable lesson for the reader

**Anti-pattern:** flat arc «was bad → understood → did → became good». Engagement ~2.5%.
**With pits and doubts:** same story, engagement ~10%+ (3.5x lift, measured).

### Slide Headline Types (pick per slide role)

| Type | Use for | Example |
|------|---------|---------|
| **Direct** | Cover, problem slides | «How to package a case study people actually read» |
| **Resonant** | Personal/proof slides | «How I lost a client over one word» |
| **Spiky** | Hook, POV slides | «Auto-funnels are bullshit» |

**Headline test:** after reading the first 5 words, does the question «and then what?» arise? If yes, it works.

### Hero Matrix: Content Generator

When you need slide ideas but don't know where to start:

| | Professional | Personal |
|--|-------------|----------|
| **Strengths** | What you do better than peers | Humor, empathy, curiosity |
| **Weaknesses** | Where you still struggle | Introversion, self-doubt, impatience |

Cross-combine for unique slides:
- Prof weakness + personal strength = new angle in your niche
- Prof strength that grew from personal weakness = transformation story
- Personal weakness that grew from prof strength = showing humanity

### Reader Positioning (The "Bits" Rule)

**A reader buys when they are 1-2 story beats behind the hero.** Close enough to identify, far enough to need your solution. Before writing any carousel: what beat is the target reader on right now?

### Before/After as Transformation (not Design)

Before/after slides are not a visual pattern — they are narrative. The arc must include an intermediate failure, not just «was X → became Y».

```text
Before (concrete pain) -> Attempt (failed) -> Re-think (the insight) -> After (measured result with numbers)
```

## Pattern Catalog

### central-object-orbit-facts

Use for context/system passports.

```json
{
  "pattern": "central-object-orbit-facts",
  "fields": {
    "center": "product/system/person",
    "facts": ["3-4 short label+value blocks"],
    "accent": "one key constraint or metric"
  }
}
```

### split-comparison

Use for before/after, VS, LLM vs code, old/new process.

```json
{
  "pattern": "split-comparison",
  "fields": {
    "left_label": "before/LLM/problem",
    "left_points": ["1-3 points"],
    "right_label": "after/code/solution",
    "right_points": ["1-3 points"],
    "bridge": "arrow, VS, or conclusion"
  }
}
```

### big-metric-meaning

Use when one number is the point.

```json
{
  "pattern": "big-metric-meaning",
  "fields": {
    "metric": "70%",
    "meaning": "what this changes",
    "proof": "short source/context"
  }
}
```

### mistake-fix

Use for educational or expert content.

```json
{
  "pattern": "mistake-fix",
  "fields": {
    "mistake": "common wrong behavior",
    "why_bad": "short consequence",
    "fix": "specific replacement"
  }
}
```

### framed-example

Use for sales/marketing/operational examples.

```json
{
  "pattern": "framed-example",
  "fields": {
    "principle": "short rule",
    "example": "quote/code/UI/text sample",
    "annotation": "why it works"
  }
}
```

## Visual Brief Schema

Use for LLM-to-renderer handoff.

```json
{
  "platform": "instagram|linkedin|telegram|vk|article|custom",
  "format": { "width": 1080, "height": 1350, "aspect_ratio": "4:5" },
  "brand_profile": "velmi|custom|none",
  "deck": {
    "title": "string",
    "audience": "string",
    "tone": "expert|friendly|bold|meme|technical",
    "slide_count": 7
  },
  "slides": [
    {
      "index": 1,
      "role": "cover",
      "pattern": "central-object-orbit-facts|split-comparison|big-metric-meaning|mistake-fix|framed-example|custom",
      "headline": "3-8 words",
      "body": ["0-3 short bullets"],
      "visual_object": "big number|diagram|photo|icon|screenshot|comparison|central object",
      "composition": "where headline/object/support/CTA sit",
      "palette_roles": {
        "base": "#000000",
        "text": "#ffffff",
        "accent": "#ff6600",
        "support": "#eeeeee",
        "danger": "#ff2d2d"
      },
      "deletable_if_crowded": ["least important fact"]
    }
  ]
}
```

## Final Slide Rules

Good final slides summarize, give a principle, or ask for one concrete action.

Weak final slides repeat the previous result, relist every metric, add vague "follow for more", or introduce an unrelated idea.

