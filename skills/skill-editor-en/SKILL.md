---
name: skill-editor-en
description: English text editor — structural and stylistic editing. Removes AI-isms, promotional fluff, weasel words, passive overuse. Does NOT fix spelling or typography (that is proofreader). Use when asked to edit, rewrite, or improve English text.
---

# Editor EN — Skill

## Goal
Improve English text at the content level: structure, clarity, logic, tone, persuasiveness. Remove AI-writing patterns, promotional language, hedge words. Preserve the author's voice.

## Inputs
- Source text
- Target audience (who reads this?)
- Goal (what should the reader do/feel/know?)
- Optional: STYLE.md, SOUL.md, brand voice references

## Outputs
- Edited text (content-level changes only)
- Change log: what changed and why (per section)
- Remaining issues (if any)

## Workflow

### Pass 1: Purpose check
Read the text and answer:
1. What is the one thing the reader should take away?
2. Does every paragraph serve that purpose?
3. What can be cut without losing the message?

### Pass 2: Structure
- Is there a clear heading hierarchy?
- Does each section have one claim?
- Is the sequence logical?
- Is the lead strong enough?
- Is the ending clear (CTA, conclusion, next step)?

### Pass 3: AI-isms & promotional language
Remove or replace:
- "In today's [noun]" openings ("In today's digital landscape")
- "In the ever-evolving world of..."
- "It is important/crucial/essential to note that..."
- "Delve", "showcase", "underscore", "testament", "tapestry"
- "Leverage" → "use", "utilize" → "use"
- "Robust", "seamless", "cutting-edge", "next-gen"
- "Robust" → "reliable", "seamless" → remove or specify
- "A comprehensive overview" → remove or be specific
- "A wealth of" → "many"

### Pass 4: English AI-writing patterns
- Remove significance inflation ("pivotal", "watershed", "groundbreaking", "game-changer")
- Remove promotional language ("vibrant", "nestled", "thriving")
- Remove vague attributions ("Experts believe", "Studies show" — without citation)
- Remove chatbot artifacts ("Certainly!", "I hope this helps!", "Feel free to reach out")
- Remove formulaic transitions ("Moreover", "Furthermore", "In conclusion")
- Remove generic conclusions ("The future looks bright", "Only time will tell")
- Remove synonym cycling (same concept in 3 different words)
- Remove copula avoidance ("serves as", "features", "boasts" → "is", "has")
- Remove em dash overuse (— with spaces is a ChatGPT tell)
- Remove self-labeling significance ("That last move is the contrarian one")
- Remove false concession ("While X has limitations, it's still remarkable")
- Remove rhetorical question openers ("What if there were a better way?")
- Remove infomercial hooks ("The catch?", "The kicker?", "Here's the thing")

### Pass 5: Weasel words & hedge stack
- "It is worth noting", "Interestingly", "Surprisingly", "Notably"
- "May", "might", "could potentially", "can possibly"
- Stacked hedges: "could potentially create" → pick one
- "It's not X, it's Y" constructions → direct positive statement
- False ranges ("from the Big Bang to dark matter") → list actual topics
- Parenthetical hedging ("tools (like X and Y)") → name directly or cut

### Pass 6: Voice & tone
- Does the text sound like the author or like a generic LLM?
- Replace uniform sentences with varied rhythm
- Remove over-polishing — keep natural disfluency
- Check: would a human actually say this?
- Compare with STYLE.md / SOUL.md if available

## Hard rules
- Preserve the author's voice — don't sand it to generic
- Do not change facts, numbers, names, brand terms
- Do not add content — edit what's there
- If unsure, flag as comment, don't change silently
- Do not touch spelling or grammar — that is proofreader's job
- Do not touch typography (quotes, dashes, spacing) — that is proofreader's job

## Examples

### Good edit
**Before:** "In today's ever-evolving digital landscape, Acme's innovative platform serves as a unified hub that leverages cutting-edge AI to seamlessly optimize workflows, empowering teams to reach their full potential."

**After:** "Acme's platform cuts workflow time by 40%. Teams set up in 10 minutes. No training needed."

**Why:** removed all promotional fluff, replaced with specific claims.

### Bad edit (over-editing)
**Before:** "We built a simple tool that helps small businesses send invoices."

**After:** "Our revolutionary paradigm-shifting platform leverages state-of-the-art neural architectures to reimagine the invoicing experience."

**Why:** direct language replaced with buzzword salad. Made it worse.
