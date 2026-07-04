---
name: ce-refactor
description: "Atomic refactoring executor. Takes a plan from ce-architect (or a list of code smells) and executes it step by step — one change, one test, one commit. Uses Strangler pattern: build new code alongside old, migrate callers one by one, delete old only when proven. Never big-bang. Use when user says 'refactor', 'execute refactor plan', 'порефактори', 'выполни план рефакторинга', or after running ce-architect."
argument-hint: "[plan file path from ce-architect, or smell list, or module to refactor]"
---

# ce-refactor — Atomic Refactoring Executor

This skill EXECUTES a refactoring plan. It does not analyze architecture (ce-architect does that). It does not review code (ce-code-review does that). It executes — one atomic step, one test run, one checkpoint at a time.

Core rule: **«Рядом, а не вместо»** (Strangler Fig pattern). New code lives beside old code. Callers migrate one by one. Old code is deleted ONLY after the new is proven.

## Contract

| Aspect | Value |
|--------|-------|
| **Input** | Refactoring plan from ce-architect, or list of code smells, or module path to refactor |
| **Output** | Executed refactoring — each step verified by tests, each checkpoint committed |
| **Side effects** | Modifies project files, runs tests, creates commits |
| **Guard** | Do NOT execute without a YAML `refactor_plan` from ce-architect. Reject free-form markdown plans. Never big-bang. Stop on first test failure. Stop at any `stop_condition`. |

## Anti-Rationalization

| Excuse | Reality |
|--------|---------|
| "I'll do all the changes at once, faster" | Big-bang refactors break everything. One change at a time = one revert at a time. |
| "The tests pass, I can skip the intermediate run" | Tests pass now ≠ tests pass after next change. Run after EVERY step. No exceptions. |
| "This step is too small to commit" | Every step is a checkpoint. Crash recovery depends on granular commits. |
| "I'll delete the old code first, then write the new" | Reverse order = no fallback. New code first. Old code last. Always. |
| "I know the pattern, I'll skip reading callers" | Every caller is a potential regression. Grep for EVERY call site before touching anything. |
| "It's a pure refactor, behavior doesn't change" | Pure refactors break things. Tests are your only proof. Run them. |

## Execution

### Phase 0: Plan Intake (30 sec)

1. **If a plan file is provided** ($ARGUMENTS path → read it):
   - Look for `refactor_plan:` YAML block (ce-architect machine-readable format)
   - If YAML found: parse steps, invariants, stop_conditions, test_command, lint_command
   - If NO YAML block: reject. Tell user: «Plan lacks machine-readable `refactor_plan` block. Re-run ce-architect to generate structured output.»
   - Never parse free-form markdown steps. Only YAML or explicit user confirmation of each step.

2. **If a smell list or module is provided** (no ce-architect plan):
   - Run ce-architect first to produce a plan
   - Wait for user to confirm the plan before executing

3. **Workspace check:**
   ```
   git status --short  — working tree must be clean or user-confirmed
   git branch --show-current  — note the branch
   ```
   If dirty workspace, ask: "Commit, stash, or proceed with uncommitted changes?"

### Phase 1: Safety Net (30 sec)

1. **Run existing tests** using `refactor_plan.test_command` to establish baseline.

2. **Identify test coverage gaps** for the target area:
   - Do tests exist for the functions being refactored?
   - If not: write minimal characterization tests that capture CURRENT behavior

### Phase 2: Atomic Execution Loop

For each step in the plan's `refactor_plan.steps` array, execute this cycle. Use `step.id`, `step.title`, `step.change_type`, `step.files`, `step.verification`, `step.rollback`, `step.risk` from the YAML:

```
┌─────────────────────────────────────────┐
│  FOR EACH STEP IN PLAN:                 │
│                                         │
│  1. ANNOUNCE: "Step N/M: <description>" │
│                                         │
│  2. VERIFY: Existing tests pass         │
│                                         │
│  3. BUILD: New code BESIDE old          │
│     (same file, new function;           │
│      or new file next to old)           │
│                                         │
│  4. MIGRATE: One caller at a time       │
│     → run tests after EACH caller       │
│                                         │
│  5. TEST: Full test suite               │
│     ✅ PASS → continue                  │
│     ❌ FAIL → REVERT, diagnose, retry   │
│                                         │
│  6. CLEAN: Delete old code ONLY after   │
│     all callers migrated + tests pass   │
│                                         │
│  7. COMMIT: git commit -m "step N: ..." │
│                                         │
│  8. REPORT: Done / Failed / Skipped     │
└─────────────────────────────────────────┘
```

### Phase 2b: Strangler Pattern (detailed)

```
OLD CODE:  function processPayment() { ... }
           called by: checkout.ts, invoice.ts, api.ts

STEP A:    Create processPaymentV2() BESIDE old function
           → processPayment() still exists, still works

STEP B:    Migrate checkout.ts → processPaymentV2()
           → run tests → pass → commit

STEP C:    Migrate invoice.ts → processPaymentV2()
           → run tests → pass → commit

STEP D:    Migrate api.ts → processPaymentV2()
           → run tests → pass → commit

STEP E:    Delete processPayment() (all callers migrated)
           → run tests → pass → rename V2 → processPayment
           → commit
```

Never delete old code before ALL callers use the new code.

### Phase 2c: Failure Handling

If tests fail after a step:

1. **Read the test output.** Don't guess. Understand the failure.
2. **Revert the change.** `git checkout -- <file>` or undo the edit.
3. **Diagnose.** Was the step wrong? Did a caller break? Is the test flaky?
4. **Retry with fix.** Same step, corrected.
5. **Two failures in a row on the same step** → stop. Report the blockage. Don't loop.
6. **If any `stop_condition` triggers** → stop immediately, report which condition fired.

### Phase 3: Final Verification

1. **Full test suite** — must pass clean
2. **Lint/typecheck** — using `refactor_plan.lint_command`, must pass clean
3. **Dead code check** — did we leave any unreferenced old code?
4. **Invariants check** — verify each `refactor_plan.invariants` item still holds
5. **Git log** — `git log --oneline -N` showing one commit per step

### Phase 4: Summary

```markdown
## Refactoring Complete: <target>

| Step | Description | Status | Commits |
|------|-------------|--------|---------|
| 1/N | Rename foo → bar | ✅ | abc1234 |
| 2/N | Extract validate() | ✅ | def5678 |
| ... | ... | ... | ... |

### What changed
- <summary of structural changes>

### What was preserved
- All existing behavior (verified by N tests)
- All existing tests pass
- Git history is atomic (one commit per step, easy revert)

### Next
- Run ce-code-review on the diff
- Run ce-compound to document learnings
```

## Integration Points

- **Before:** Must have plan from `ce-architect`, or run ce-architect inline
- **During:** Use `ce-sessions` if stuck (check if this area was recently changed)
- **After:** Run `ce-code-review` on the diff; run `ce-compound` to capture patterns
- **Record:** Log architecture decisions via `skill-decisions-log`

## Hard Rules

1. **One step = one change = one test run = one commit.** Never batch.
2. **«Рядом, а не вместо» — always.** New code first. Old code last.
3. **Tests must pass BEFORE and AFTER every step.** No skipped test runs.
4. **Stop on failure.** Don't push through. Revert, diagnose, retry.
5. **Never refactor and add features at the same time.** Separate sessions.
6. **Commit after every step.** Atomic commits = atomic reverts.
7. **Scope discipline.** If the plan has 12 steps, execute exactly 12 steps. Don't "while we're here" expand scope.
