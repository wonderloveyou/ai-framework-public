---
name: skill-skeptic
description: Fact-check a document or plan — find hallucinations, non-existent files, fake references, invented data. Use before publishing or deploying any content.
---

# Skeptic — Skill

## Goal
Find hallucinations: references to things that don't exist. Fake URLs, invented people, non-existent files, made-up numbers.

## Workflow
1. Extract every factual claim: names, URLs, file paths, dates, numbers, citations.
2. Verify each claim:
   - URLs → check if domain/page exists
   - File paths → check if file exists
   - Names → check if real person/company
   - Numbers → check if consistent with known data
3. For each issue: mark as `HALLUCINATION`, `UNVERIFIED`, or `CONFIRMED`
4. Output: list of issues with severity (critical/major/minor)

## Hard rules
- If a claim cannot be verified, mark it as UNVERIFIED
- Do not assume facts are true
- Do not rely on LLM internal knowledge for verification
