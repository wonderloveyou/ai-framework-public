---
name: skill-proofreader-en
description: English proofreader — typography, spelling, grammar, punctuation, formatting. Fixes quotes, dashes, commas, subject-verb agreement. Does NOT change content or style (that is editor). Use when asked to proofread, check grammar, fix typos.
---

# Proofreader EN — Skill

## Goal
Fix mechanics: typography, spelling, grammar, punctuation, formatting. Zero changes to content, structure, or style.

## Inputs
- Source text (English)

## Outputs
- Proofread text (only mechanical fixes)
- Change log: list of all changes with line references

## Workflow

### Pass 1: Typography
- Quotes: straight quotes (") → curly quotes (“ ”) with proper nesting: “outer ‘inner’ quotes”
- Apostrophes: straight (') → curly (’)
- Dashes: hyphen (-) for compounds → keep. En dash (–) for ranges (2010–2020). Em dash (—) for breaks — with spaces on both sides (Chicago style) or without (AP style) — follow project style
- Ellipsis: ... → … (single character)
- Copyright: (c) → ©, (r) → ®, (tm) → ™
- Trademark/brand names: preserve original capitalization
- Non-breaking spaces: before em dash in some styles, between number and unit (10 km)
- Numbers: group digits with comma (1,000,000), decimal point (3.14)

### Pass 2: Spelling
- Fix typos and misspellings
- Subject-verb agreement (the team is → not the team are in US English)
- Consistent spelling variant (US vs UK — follow project style: "color"/"colour", "organize"/"organise")
- Apostrophe placement: it's (it is) vs its (possessive), your vs you're, their vs there vs they're
- Compound words: check if hyphenated, open, or closed (e.g., "email" not "e-mail" in modern style)
- Proper nouns: preserve original capitalization
- Repeated words (the the, in in)

### Pass 3: Punctuation
- Oxford comma: consistent use (follow project style)
- Comma splices: fix run-on sentences
- Missing commas after introductory phrases ("However, ...", "In addition, ...")
- Colon vs semicolon: colon introduces list/explanation, semicolon connects related clauses
- Period in abbreviations: Mr., Dr., e.g., i.e. (US) vs Mr, Dr without period (UK)
- Quotes: periods and commas inside quotes in US English (He said "hello."), outside in UK English (He said 'hello').
- Hyphen in compound modifiers before noun ("well-known author") but not after ("the author is well known")

### Pass 4: Formatting
- Extra spaces (double spaces, trailing spaces)
- Line breaks (single between sections, double between paragraphs)
- List formatting (consistent bullet style, consistent punctuation)
- Inconsistent bold/italic
- Markdown syntax (broken links, unclosed tags)
- Title case in headings (Chicago style: capitalise major words)

## Hard rules
- Do NOT change words, phrasing, or sentence structure
- Do NOT rewrite or edit content
- Do NOT fix style or tone — that is the editor
- Do NOT add or remove text — only fix mechanics
- If a "typo" might be a deliberate choice (dialect, character voice), flag as a comment
- For US vs UK: follow project style; if none, default to US

## Examples

**Before:** "It's its own unique approach, theres alot to consider —and the team's working on it."

**After:** "It's its own unique approach. There's a lot to consider — and the team's working on it."

**Before:** "Heres a list of three things;apples, oranges, and bananas."

**After:** "Here's a list of three things: apples, oranges, and bananas."

**Before:** "The well known author who wrote the best selling book is well-known."

**After:** "The well-known author who wrote the best-selling book is well known."
