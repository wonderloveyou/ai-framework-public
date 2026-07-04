---
name: skill-reality-checker
description: Verify that referenced files, functions, APIs, and dependencies actually exist in the current project or codebase. Use before writing code that depends on existing structures.
---

# Reality Checker — Skill

## Goal
Before writing code, verify that referenced files, functions, modules, and APIs actually exist. Prevents hallucinations.

## Workflow
1. List all file paths mentioned in the plan/task
2. Check each path exists
3. List all function/class imports mentioned
4. Check each exists in the codebase
5. List all external APIs/dependencies
6. Check each is available (installed or documented)
7. Report mismatches

## Output
- ✅ FOUND: path/func/api exists
- ❌ MISSING: path/func/api does not exist
- ⚠️ AMBIGUOUS: similar name found, suggest correction
