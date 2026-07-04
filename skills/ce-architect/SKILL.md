---
name: ce-architect
description: "Architecture analysis before refactoring. Asks WHY before touching code — finds code smells, duplication, dead code, coupling, and technical debt. Produces a structured analysis with refactoring plan, risk assessment, and regression estimates. Use BEFORE any significant refactoring, code cleanup, or when user says 'analyze this code', 'architectural review', 'refactor plan', 'разбери архитектуру'."
argument-hint: "[file path, module name, or feature area to analyze]"
---

# ce-architect — Architecture Analysis & Refactoring Plan

Before changing structure, understand structure. This skill produces a **refactoring plan** — never executes it. Execution happens through ce-work or manual implementation.

The core rule: **"Рядом, а не вместо"** — for large refactorings, build new code alongside old, then switch over. Never delete the old until the new is proven.

## Contract

| Aspect | Value |
|--------|-------|
| **Input** | File path, module name, directory, or feature area to analyze |
| **Output** | Structured analysis: code smells, risks, duplication, dead code, refactoring plan with atomic steps |
| **Side effects** | Read-only. No code is modified. No files are written (except optional analysis artifact) |
| **Guard** | Do not refactor. Produce a plan. Let the user decide whether to execute it. |
| **Time budget** | 3-5 minutes for analysis; stop and report if scope is too large |

## Anti-Rationalization

| Excuse | Reality |
|--------|---------|
| "I can see the problem, let me just fix it" | Local fixes create global regressions. Analyze the blast radius first. |
| "This code is obviously dead" | "Obviously dead" code often has one caller you missed. Grep before deleting. |
| "I''ll refactor and add the feature at the same time" | Refactoring + features = untestable chaos. Separate them. Always. |
| "The duplication is minor, not worth extracting" | Minor duplication today = major drift tomorrow. Extract or document why not. |
| "I''ll skip the risk assessment, it''s a safe change" | Every structural change carries regression risk. Measure before dismissing. |
| "The old code is bad, I''ll rewrite it" | Rewrites lose bugfixes and edge cases accumulated over years. Refactor incrementally. |

## Execution

### Phase 0: Scope Definition (30 sec)

1. **Determine analysis target.** From $ARGUMENTS or user prompt — identify the file, module, or feature area.
2. **Map the blast radius.** Find every caller, importer, and dependent:
   ```
   grep for imports of the target module
   grep for function/class usage across the codebase
   ```
3. **Size the scope:**
   | Scope | Signal | Action |
   |-------|--------|--------|
   | Small | 1-3 files, <500 lines total | Full analysis |
   | Medium | 4-10 files, 500-2000 lines | Analyze entry points + key internals |
   | Large | 10+ files, >2000 lines | Narrow to one module or recommend ce-plan |
4. **Identify project conventions.** Read 2-3 neighboring files to understand local patterns, naming, structure.

### Phase 1: Code Smell Detection (1-2 min)

Search for each smell class. For each finding, note file:line, severity, and a one-line description.

#### 1a. Structural Smells

- **God objects / functions** — >200 lines, >5 responsibilities
- **Long parameter lists** — >4 parameters, especially same-type adjacency
- **Deep nesting** — >3 levels of if/for/try
- **Circular dependencies** — A imports B imports A
- **Feature envy** — function uses another module's data more than its own
- **Inappropriate intimacy** — modules accessing each other's internals

#### 1b. Naming Smells

- **Vague names** — `data`, `info`, `handle`, `process`, `manage`, `tmp`, `foo`
- **Misleading names** — name promises one thing, code does another
- **Inconsistent terminology** — same concept named differently across modules
- **Undocumented abbreviations** — `cnt`, `cfg`, `ctx` without project-wide convention

#### 1c. Logic Smells

- **Repeated conditionals** — same if/switch in multiple places
- **Magic numbers/strings** — unexplained literals
- **Shotgun surgery** — changing one rule requires editing 5+ files
- **Divergent change** — one module changed for unrelated reasons

### Phase 2: Duplication Analysis (30 sec)

1. **Find exact duplicates** — identical blocks >10 lines
2. **Find structural duplicates** — same logic with different variable names
3. **Find conceptual duplicates** — same problem solved differently in two places
4. **Classify each duplicate:**
   | Type | Action |
   |------|--------|
   | Accidental | Extract shared function/module |
   | Domain rule | Extract into domain service |
   | False positive | Note why they differ — they may diverge further |

### Phase 3: Dead Code Detection (30 sec)

1. **Find unused exports** — exported but never imported outside its own tests
2. **Find unreachable code** — after return/throw/exit, inside `if false`
3. **Find commented-out code** — blocks >5 lines of commented code
4. **Find zombie features** — feature-flagged code where flag is always off

### Phase 4: Coupling & Cohesion (30 sec)

1. **Coupling check:**
   - Count imports per module — >10 is a coupling smell
   - Find import cycles (A→B→A)
   - Find hidden coupling (shared mutable state, global singletons)

2. **Cohesion check:**
   - Do all functions in a module operate on the same data?
   - Are there functions that don't belong? (low cohesion)
   - Is the module about one thing? (single responsibility)

### Phase 5: Risk Assessment (30 sec)

For each proposed change, estimate:

| Risk Level | Signal | Mitigation |
|-----------|--------|------------|
| **Low** | Rename, extract function, add test | Safe to automate |
| **Medium** | Reorder params, change types, split module | Needs test coverage first |
| **High** | Change data flow, remove abstraction, merge modules | Build alongside old, switch over |
| **Critical** | Change persistence, auth, API contract | Requires spec review + staged rollout |

### Phase 6: Refactoring Plan

Produce a structured plan in TWO formats: human-readable markdown (for the user) AND machine-readable YAML (for ce-refactor). Both are mandatory.

#### 6a. Human-readable summary (markdown)

```markdown
## Architecture Analysis: <target>

### Scope
- **Files analyzed:** N | **Lines:** ~N | **Callers:** N | **Risk level:** low/medium/high/critical

### Code Smells Found
| # | File:Line | Smell | Severity |
|---|-----------|-------|----------|
| 1 | auth.ts:42 | Long parameter list | medium |
| 2 | ... | ... | ... |

### Duplication
- `auth.ts:42-58` duplicates `legacy-auth.ts:12-28` → extract `validateCredentials()`

### Dead Code
- `utils.ts:foo()` — exported, never imported outside tests

### What NOT to change
- `auth.ts:validateSession()` — complex but stable, touched by hotfix last week
- `legacy-auth.ts` — scheduled for deprecation next quarter
```

#### 6b. Machine-readable plan (YAML — required for ce-refactor)

```yaml
refactor_plan:
  version: 1
  target: "<module or file>"
  goal: "<one-line purpose>"
  overall_risk: low|medium|high|critical
  
  invariants:
    - "Public API signatures must not change"
    - "<other constraints>"
  
  scope:
    include: ["src/auth.ts", "src/login.ts"]
    exclude: ["src/legacy-auth.ts"]
  
  steps:
    - id: R001
      title: "Rename usr → user"
      change_type: rename
      risk: low
      files:
        - path: "src/auth.ts"
          action: modify
        - path: "src/login.ts"
          action: modify
      verification:
        command: "grep -r 'usr' src/ | wc -l | grep 0"
        expected: pass
      rollback: "git revert R001"
    
    - id: R002
      title: "Extract validateCredentials()"
      change_type: extract
      risk: medium
      files:
        - path: "src/auth.ts"
          action: modify
        - path: "src/legacy-auth.ts"
          action: modify
          lines: "12-28"
      new_files:
        - path: "src/validate-credentials.ts"
          action: create
      verification:
        command: "npm test -- validateCredentials"
        expected: pass
      rollback: "git revert R002"
  
  stop_conditions:
    - "Any test failure without clear cause"
    - "Need to edit file outside defined scope"
    - "Two consecutive failures on same step"
  
  test_command: "npm test"
  lint_command: "npm run lint"
```

### Phase 7: Decision Gate

Before the user acts on the plan:

- [ ] Every smell has a severity and a file:line
- [ ] Every duplicate is classified (accidental/domain/false positive)
- [ ] Every dead code candidate has been verified (grepped for callers)
- [ ] High-risk steps have explicit mitigation strategy
- [ ] "What NOT to change" section is filled — things that look wrong but are intentionally so
- [ ] Plan steps are atomic and reversible

## Anti-Patterns (in Analysis)

- **"This whole module needs a rewrite"** — Rewrites discard accumulated knowledge. Refactor incrementally.
- **"I see 20 smells, let me list them all"** — Prioritize. Top 5 smells by impact. The rest are noise.
- **"The naming convention is different from what I'd use"** — Project conventions override personal taste. Match existing style.
- **"This abstraction is unnecessary"** — Chesterton's Fence: understand why it exists before removing it.
- **"I'll skip the caller search, it's probably fine"** — Every deletion candidate must be verified. Grep is cheap. Regressions are expensive.

## Integration Points

- **Before:** Run `ce-init` to understand project context
- **During:** Use `ce-sessions` to check if this area was recently modified
- **After:** Execute steps via `ce-work`; review each step with `ce-code-review`
- **Record:** Log architecture decisions via `skill-decisions-log`

## Output

Always produce the Phase 6 structured report. The report IS the deliverable — no code changes, no commits. The user decides which steps to execute.
