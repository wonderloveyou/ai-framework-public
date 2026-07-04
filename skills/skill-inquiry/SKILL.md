---
name: skill-inquiry
description: Structured inquiry — extract knowledge from a person or decompose a topic through layered questioning. Combines Socratic method and interview techniques. Use when exploring a complex topic, debugging a decision, gathering requirements, interviewing a subject, or writing a spec.
---

# Inquiry — Skill

Combines two modes: **Socratic** (decompose a topic through questions) and **Interview** (extract knowledge from a person). Both start with the business goal.

---

## Mode 1: Socratic — Topic decomposition

### Goal
Decompose a complex topic or problem through layered questioning, revealing assumptions, gaps, and structure.

### Inputs
- Topic or problem statement
- Business goal: why are we exploring this? What decision will it inform?
- Optional: existing knowledge or documents
- Depth: surface / moderate / deep

### Outputs
- Question map: hierarchical tree of questions and answers
- Assumptions uncovered (marked explicitly)
- Gaps identified
- Synthesis: what was learned and how it affects the goal

### Workflow

**Phase 1: Surface — what do we know?**
Ask descriptive questions:
- What is this? Define it.
- What is it not? Define boundaries.
- Who is involved? Stakeholders.
- What is the current state?

**Phase 2: Structure — how is it organized?**
Ask analytical questions:
- What are the parts? Decompose.
- How do parts relate? Map connections.
- What depends on what? Dependencies.
- What is the sequence? Timeline/order.

**Phase 3: Challenge — why is it this way?**
Ask critical questions:
- Why this approach and not another?
- What assumptions are we making?
- What would disprove this?
- What is the weakest point?

**Phase 4: Synthesis — what now?**
Ask generative questions:
- What is the core insight?
- What should change?
- What is the next question to answer?
- What would we do if we had perfect information?

### Output template
```md
# Socratic Analysis: <Topic>

## Business Goal
<Why we are exploring this>

## Question Map
1. Q: ... → A: ...
   1.1 Q: ... → A: ...
   1.2 Q: ... → A: ...

## Assumptions
- [Confirmed/Unconfirmed] Assumption: ...

## Gaps
- ...

## Synthesis
- ...
```

---

## Mode 2: Interview — Knowledge extraction from a person

### Goal
Extract structured knowledge from a person through prepared questions, active listening, and organized synthesis.

### Inputs
- Topic or area to explore
- Interviewee role/context (who are we talking to?)
- Business goal: why are we interviewing this person? What will we do with the information?
- Interview mode: live chat / async / document review
- Optional: existing materials to ground the conversation

### Outputs
- Structured notes (question → answer format)
- Key insights (3-5 bullets, each with relevance to goal)
- Decisions made
- Open items
- Next steps

### Workflow

**Phase 1: Prepare**
1. Define the interview goal in one sentence
2. Identify 3-5 key areas to cover
3. Prepare 5-10 open-ended questions
4. For each question, define what a "good answer" looks like
5. Review existing materials to avoid asking known information
6. Note: what business decision does each question support?

**Phase 2: Conduct**
1. Start with context: restate the goal
2. Ask one question at a time
3. After each answer, optionally probe deeper:
   - "Can you give an example?"
   - "What makes you say that?"
   - "What would change if...?"
4. Capture verbatim key quotes
5. Confirm understanding: "So what you're saying is..."

**Phase 3: Synthesize**
1. Group answers by theme
2. Extract decisions and action items
3. Note areas of uncertainty or disagreement
4. Write summary of key insights, each linked to the business goal
5. List open questions for follow-up

### Output template
```md
# Interview: <Topic> with <Person>

## Business Goal
<Why this interview was conducted>

## Key Insights
- Insight 1 (relevance to goal)
- Insight 2 (relevance to goal)

## Notes
### Q1: ...
A: ...

### Q2: ...
A: ...

## Decisions
- ...

## Open Items
- ...

## Next Steps
- ...
```

---

## Hard rules (both modes)
- Do not answer without evidence or reasoning
- Mark assumptions explicitly as "Assumption: ..."
- If a question cannot be answered, keep it open as a gap — do not fabricate
- Prefer "What evidence supports this?" over "Is this true?"
- Distinguish between verbatim quotes and your interpretation
- If something is unclear, ask for clarification, don't guess
- Always connect findings back to the business goal

## Common mistakes
- Asking leading questions that bias the answer
- Stopping at surface answers without probing
- Not capturing uncertainty or disagreement
- Presenting assumptions as facts
- Losing the business context — exploring for the sake of exploring
