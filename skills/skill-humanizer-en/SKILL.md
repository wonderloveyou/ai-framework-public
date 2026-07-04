---
name: skill-humanizer-en
description: Remove AI-generated writing patterns from English text. Based on Wikipedia's "Signs of AI writing" (33 patterns). Use when asked to humanize, de-AI, or make text sound natural. Detects: inflated significance, promotional language, AI vocabulary, em dashes, rule-of-three, chatbot artifacts, hedging, filler, and more.
license: MIT
---

# Humanizer EN — Remove AI Writing Patterns

## Goal
Identify and remove signs of AI-generated text to make writing sound more natural and human. Based on [Wikipedia: Signs of AI writing](https://en.wikipedia.org/wiki/Wikipedia:Signs_of_AI_writing), maintained by WikiProject AI Cleanup.

## Inputs
- Source text to humanize
- Optional: writing sample for voice calibration
- Optional: intended tone (formal, casual, technical)

## Outputs
- **Draft rewrite** — first pass with AI patterns removed
- **"Still AI" audit** — brief list of remaining tells
- **Final rewrite** — second pass addressing remaining tells
- Optional: change summary

---

## Workflow

### Step 1: Identify AI patterns
Scan the text for the 33 patterns below. Note every instance.

### Step 2: Draft rewrite
Replace AI-isms with natural alternatives. Preserve meaning. Cover everything the original covers — if original has 5 paragraphs, rewrite has 5 paragraphs.

### Step 3: "Still AI" audit
Ask: *"What makes this obviously AI generated?"* List remaining tells.

### Step 4: Final rewrite
Address remaining tells. **Hard constraint: zero em dashes (—) or en dashes (–) in the final output.** Replace with periods, commas, colons, or restructured sentences.

### Step 5 (Optional): Voice calibration
If user provides a writing sample, analyze it before rewriting:
- Sentence length patterns (short/punchy? long/flowing? mixed?)
- Word choice level (casual? academic? in between?)
- How they start paragraphs
- Punctuation habits
- Verbal tics and recurring phrases
Match their voice in the rewrite. When no sample provided, use natural, varied, opinionated voice.

---

## 33 AI Writing Patterns

### CONTENT PATTERNS

**1. Significance inflation** — Words: *stands/serves as, testament, pivotal, crucial, vital, marks a shift, evolving landscape, deeply rooted.* Replace with plain statements of fact.

**2. Notability name-dropping** — *Cited in NYT, BBC, FT... active social media presence.* Replace list with a specific sourced example.

**3. Superficial -ing analyses** — *symbolizing, reflecting, showcasing, underscoring, highlighting.* Remove or expand with actual sources.

**4. Promotional language** — *vibrant, nestled, breathtaking, stunning, must-visit, rich (figurative).* Replace with neutral description.

**5. Vague attributions** — *Experts believe, Industry reports, Observers have cited, Studies show.* Replace with specific named source or remove.

**6. Formulaic challenges sections** — *Despite its... faces several challenges... Despite these challenges, continues to thrive.* Replace with specific facts.

### LANGUAGE PATTERNS

**7. AI vocabulary** — High-frequency words: *actually, additionally, align with, crucial, delve, emphasizing, enduring, enhance, foster, garner, highlight, interplay, intricate, key (adj), landscape (abstract), pivotal, showcase, tapestry (abstract), testament, underscore, valuable, vibrant.* Replace with simpler words.

**8. Copula avoidance** — *serves as, stands as, features, boasts* → *is, has*. Use simple copulas.

**9. Negative parallelisms / tailing negations** — *"Not only...but...", "It's not just X, it's Y", "..., no guessing"*. State the point directly.

**10. Rule of three** — *"innovation, inspiration, and insights"*. Use natural number of items.

**11. Elegant variation (synonym cycling)** — Same concept in 3+ different words. Repeat the clearest term.

**12. False ranges** — *"from the Big Bang to dark matter"*. List topics directly.

**13. Passive voice / subjectless fragments** — *"No configuration file needed"*. Name the actor when it helps clarity.

### STYLE PATTERNS

**14. Em dashes and en dashes** — **HARD CUT.** Zero tolerance in final output. Replace with periods, commas, colons, parentheses, or restructure. Also catch spaced em dashes (` — `) and double hyphens (`--`).

**15. Boldface overuse** — *"**OKRs**, **KPIs**, **BMC**"*. Remove bold from mechanical emphasis.

**16. Inline-header lists** — *"**Performance:** Performance improved"*. Convert to prose.

**17. Title Case Headings** — *"Strategic Negotiations And Partnerships"* → *"Strategic negotiations and partnerships"*.

**18. Emojis** — *"🚀 Launch Phase: 💡 Key Insight:"*. Remove emojis.

**19. Curly quotes** — *"“...”"* → *"..."*. Use straight quotes.

**26. Hyphenated word pairs** — *cross-functional, data-driven, client-facing.* Drop hyphens when the compound follows the noun (*"the team is cross functional"*).

**27. Persuasive authority tropes** — *"At its core, what matters is...", "The real question is...", "In reality..."*. State the point directly.

**28. Signposting announcements** — *"Let's dive in", "Here's what you need to know", "Without further ado"*. Start with the content.

**29. Fragmented headers** — A heading followed by a one-line restatement before real content. Let the heading do the work.

**30. Diff-anchored writing** — *"This function was added to replace..."*. Describe what it does, not what changed.

**31. Manufactured punchlines / staccato drama** — *"It had no preference. No prior. No nostalgia."*. Use varied sentence lengths, concrete claims.

**32. Aphorism formulas** — *"X is the Y of Z", "Symmetry is the language of trust"*. Replace with the actual claim.

**33. Conversational rhetorical openers** — *"Honestly? It depends...", "Look, here's the thing..."*. Remove fake-candid setup.

### COMMUNICATION PATTERNS

**20. Chatbot artifacts** — *"I hope this helps!", "Let me know if...", "Certainly!", "Would you like me to...?", "Should I continue?"*. Remove entirely.

**21. Cutoff disclaimers / speculative gap-filling** — *"While details are limited...", "maintains a low profile", "likely grew up..."*. Say what's known or cut the sentence.

**22. Sycophantic tone** — *"Great question!", "You're absolutely right!", "That's an excellent point!"*. Respond directly without flattery.

### FILLER AND HEDGING

**23. Filler phrases** — *"In order to" → "To", "Due to the fact that" → "Because", "At this point in time" → "Now", "It is important to note that" → (remove)*.

**24. Excessive hedging** — *"could potentially possibly" → "may"*. Pick one hedge, not three.

**25. Generic positive conclusions** — *"The future looks bright", "Exciting times lie ahead"*. Replace with specific plans or facts.

---

## Detection Guidance

### What NOT to flag (false positives)
- **Perfect grammar** — polish ≠ AI
- **Casual/formal mixing** — signals a real person, not a chatbot
- **Bland but tell-free prose** — generic dryness without specific tells is just dry writing
- **Formal vocabulary** — AI overuses *specific* fancy words (§7), not all fancy words
- **Common transitions in isolation** — one *however* is not a tell
- **Curly quotes alone** — macOS/Word/Google Docs auto-curl by default
- **Em dashes alone** — many editors use them. Flag only when paired with other tells.
- **One short emphatic sentence** — humans do this. Flag only when several appear in a row.
- **Unsourced claims** — most of the web is unsourced

**Rule: look for clusters of tells, not isolated ones.** One em dash means nothing. Em dashes + rule-of-three + *vibrant tapestry* + a "Conclusion" section = AI.

### Signs of human writing (preserve these)
- Specific, unusual, hard-to-fabricate detail
- Mixed feelings and unresolved tension
- Dated, era-bound references (slang, memes)
- First-person editorial choices the writer can defend
- Variety in sentence length
- Genuine asides, parentheticals, or self-corrections
- Edits made before November 30, 2022

---

## Personality and Voice

Avoiding AI patterns is only half the job. Sterile, voiceless writing is just as obvious as AI slop.

**Apply voice only when content calls for it** — blog posts, essays, opinion, personal writing. For encyclopedic, technical, legal text, neutral and plain *is* the correct voice.

### Signs of soulless writing (even if technically "clean")
- Every sentence same length and structure
- No opinions, just neutral reporting
- No acknowledgment of uncertainty
- No first-person perspective when appropriate
- No humor, edge, or personality

### How to add voice
- **Have opinions.** Don't just report — react. *"I genuinely don't know how to feel about this"* beats neutrally listing pros and cons.
- **Vary rhythm.** Short punchy sentences. Then longer ones. Mix it up.
- **Let mess in.** Perfect structure feels algorithmic. Tangents, asides, half-formed thoughts are human.

---

## Hard Rules
- **Preserve meaning** — do not change facts, numbers, names, or the core message
- **Rewrite, don't delete** — cover everything the original covers
- **Zero em/en dashes in final output** (§14 — hard constraint)
- **Do not touch spelling or grammar** — that is proofreader's job
- **Do not over-humanize** — for technical, legal, or encyclopedic text, keep neutral tone
- **When in doubt, flag as comment** — don't silently change ambiguous cases
- **Match user's voice if sample provided** — preserve their quirks, don't sand them to generic

## Example

**Before (AI-sounding):**
> Great question! Here is an overview. I hope this helps!
>
> AI-assisted coding serves as an enduring testament to the transformative potential of LLMs, marking a pivotal moment in the evolution of software development. These groundbreaking tools — nestled at the intersection of research and practice — are reshaping how engineers ideate, iterate, and deliver, underscoring their vital role in modern workflows.
>
> At its core, the value proposition is clear: streamlining processes, enhancing collaboration, and fostering alignment. The tool serves as a catalyst. The assistant functions as a partner. The system stands as a foundation.
>
> Industry observers have noted that adoption has accelerated from hobbyist experiments to enterprise-wide rollouts. Additionally, the ability to generate documentation, tests, and refactors showcases how AI can contribute to better outcomes.
>
> While specific details are limited, it could potentially be argued that these tools might have some positive effect. In order to fully realize this potential, teams must align with best practices.
>
> In conclusion, the future looks bright. Exciting times lie ahead. Let me know if you'd like me to expand!

**After (humanized):**
> AI coding assistants can make you faster at the boring parts. Not everything. Definitely not architecture.
>
> They're great at boilerplate: config files, test scaffolding, repetitive refactors. They're also great at sounding right while being wrong. I've accepted suggestions that compiled, passed lint, and still missed the point because I stopped paying attention.
>
> People I talk to tend to land in two camps. Some use it like autocomplete for chores and review every line. Others disable it after it keeps suggesting patterns they don't want. Both feel reasonable.
>
> The productivity metrics are slippery. GitHub can say Copilot users "accept 30% of suggestions," but acceptance isn't correctness, and correctness isn't value. If you don't have tests, you're basically guessing.

**Changes made:** Stripped chatbot framing, significance inflation, promotional and -ing padding, rule-of-three, copula avoidance, em dashes, false ranges, cutoff and hedging disclaimers, filler, persuasive framing, generic upbeat conclusion — then rebuilt with varied rhythm and concrete detail.

## Reference
- [Wikipedia: Signs of AI writing](https://en.wikipedia.org/wiki/Wikipedia:Signs_of_AI_writing)
- [WikiProject AI Cleanup](https://en.wikipedia.org/wiki/Wikipedia:WikiProject_AI_Cleanup)
- Based on [blader/humanizer](https://github.com/blader/humanizer) v2.8.0 (33 patterns)
