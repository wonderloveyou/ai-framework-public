---
name: skill-write-manual
description: Create or audit operational manuals, mini-skills, SOPs, CLAUDE.md/AGENTS.md rules, roadmaps, and repeatable agent workflows. Use when asked to write instructions, docs, manuals, or rules.
---

# Write Manual — Skill

## Goal
Create an operational document that lets a human or AI agent perform a task reliably, without ambiguity.

## Inputs
- Task description (what needs to be documented)
- Target format (CLAUDE.md / SKILL.md / MANUAL / ROADMAP / AGENTS.md)
- Optional: existing project docs (CONTEXT.md, SOUL.md, etc.)
- Optional: examples of similar documents

## Outputs
- Markdown file following the chosen format's conventions
- Verification checklist at the end

## Workflow

1. **Define the actor and task**
   - Who will follow this? (AI agent, human, both)
   - What exact outcome should they produce?
   - What are the hard boundaries (don't do X)?

2. **Define required inputs**
   - What does the actor need before starting?
   - Where do they find it?

3. **Define expected output**
   - Format (Markdown / JSON / code)
   - Structure (sections, headings, tables)
   - Quality bar (what "done" looks like)

4. **Write the procedure**
   - Numbered steps, each starting with an action verb
   - Include decision points: "If X, do Y. If not Z, do W"
   - Include error handling: "If command fails, report the error and likely cause"

5. **Add hard rules**
   - Non-negotiable constraints
   - Write as negatives: "Do not invent facts"
   - Each rule must be verifiable

6. **Add examples**
   - Good example (input → correct output)
   - Bad example (input → wrong output + why it's wrong)
   - Edge case if applicable

7. **Add verification checklist**
   - Checkboxes the actor can run through

## Hard rules
- No vague advice ("be professional", "write clean code")
- No motivational filler
- No invented facts — mark missing as `TODO: verify`
- No giant universal manuals — split by audience/task
- Each workflow step must be specific enough to test

## Output structure (template)
```md
---
name: <skill-name>
description: <when to use>
---

# <Skill Name>

## Goal
## Inputs
## Outputs
## Workflow
## Hard rules
## Examples
## Common mistakes
## Checklist
```
