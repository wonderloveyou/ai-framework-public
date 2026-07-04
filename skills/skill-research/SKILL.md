---
name: skill-research
description: Structured research — gather, organize, and verify information from web and files. Use when asked to research, investigate, find information, explore a topic, or gather data. For RU or EN queries.
---

# Research — Skill

## Goal
Gather, organize, and present information in a structured, verifiable format. Every research task starts with a **business goal**: why is this information needed and what will it be used for?

## Inputs
- Research question or topic
- Business goal: why is this needed? What decision will it inform?
- Scope: domains, sources, time range, depth (quick overview / deep dive)
- Optional: starting URLs, file paths, existing materials

## Outputs
- Structured markdown with sections: Summary, Key Findings, Sources, Gaps
- Each finding attributed to a source URL or file with retrieval date
- Confidence rating per finding: confirmed / likely / unverified / conflicting
- Business relevance note: how this finding affects the decision

## Workflow

### Phase 0: Define the business goal
Before searching, clarify:
1. Why do we need this information?
2. What decision or action will it inform?
3. What would "good enough" look like? (exhaustive vs quick scan)
4. Who is the audience for the research output?

### Phase 1: Decompose the question
1. Restate the research goal in 1-2 sentences
2. Break it into 3-7 sub-questions
3. For each sub-question, define what a good answer looks like (specific data point, trend, comparison, etc.)
4. Identify which sub-questions are critical vs nice-to-have
5. Confirm scope with user before proceeding

### Phase 2: Identify & prioritize sources
Priority order:
1. Official documentation / primary sources
2. Authoritative databases and reference works
3. Expert analysis and peer-reviewed material
4. Trusted media and industry reports
5. Community forums and user reports (lowest confidence)

For web research:
- Use search with specific queries per sub-question
- Scrape relevant pages for full content
- Note the publication/update date of each source

For file research:
- Read TOC/summary first, then specific sections
- Extract verbatim relevant passages with file path

### Phase 3: Extract facts
- One fact per bullet
- Include source URL/file path and retrieval date
- Quote verbatim where possible — do not paraphrase interpretations
- For numerical data, include the exact figure and context
- Note if the source is primary or secondary

### Phase 4: Cross-reference & verify
- If 2+ sources agree → mark as confirmed
- If sources contradict → present both claims, note the conflict
- If only one source claims X → mark as unverified
- If information is outdated or missing → mark as gap
- Check: is the source authoritative for this specific claim?
- Check: is the source's date appropriate for this claim?

### Phase 5: Synthesize
- **Summary**: 3-5 bullets of the most important findings, each with business relevance
- **Key Findings**: detailed section with sources per finding
- **Gaps**: what wasn't found and why it matters
- **Conflicts**: where sources disagree
- **Recommendation**: what to do next, what decisions can be made now

## Output template
```md
# Research: <Topic>

## Business Goal
<Why this research was done>

## Summary
- Finding 1 (confirmed) — relevance to goal
- Finding 2 (unverified) — what's missing

## Key Findings
### Finding 1: <Title>
Source: <URL/file> | Date: <retrieval date> | Confidence: confirmed
<Detail>

### Finding 2: <Title>
...

## Gaps
- <What is missing and why it matters>

## Conflicts
- <If sources disagree>

## Recommendations
- <Next steps based on findings>
```

## Semi-prompts (for recurring research patterns)

### Quick competitive scan
```
Goal: understand how [competitor] positions [feature/product].
Questions:
1. What is their headline promise?
2. What specific claims do they make (numbers, facts)?
3. What is their pricing model?
4. Who are their reference customers?
5. What channels do they use to reach audience?
Output: 1-page summary with sources.
```

### Technology / tool evaluation
```
Goal: decide whether to adopt [tool/library/service].
Questions:
1. What problem does it solve?
2. What are the alternatives and how do they compare?
3. What are the known limitations or pitfalls?
4. What is the total cost (licensing, infrastructure, learning curve)?
5. Who is using it successfully? In what context?
Output: comparison table + recommendation.
```

### Market / trend overview
```
Goal: understand current state and direction of [market/trend].
Questions:
1. What is the current market size and growth rate?
2. Who are the key players and their market share?
3. What are the major trends and drivers?
4. What are the risks and uncertainties?
5. What is the 12-24 month outlook?
Output: structured overview with confidence ratings.
```

## Hard rules
- No invented facts — if unsure, mark as `TODO: verify`
- No opinions or interpretations — only what sources say
- Always cite sources with URL/file path and retrieval date
- Distinguish between confirmed, likely, unverified, and conflicting
- If sources are outdated, note the date gap
- For each finding, state relevance to the business goal
- If the research reveals the question was wrong, flag it

## Common mistakes
- Mixing facts from different sources without attribution
- Presenting outdated information as current
- Summarizing too early — extract first, then condense
- Ignoring conflicting sources to present a cleaner picture
- Not stating what is unknown
- Research without connecting back to the business goal
