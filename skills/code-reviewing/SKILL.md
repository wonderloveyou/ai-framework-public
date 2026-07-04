---
name: code-reviewing
description: |
  Lightweight code review methodology with 11 review dimensions.
  For full multi-agent review with sub-agents and autofix → use `ce-code-review`.

  Use when: quick review, manual checklist, small diff, or when you don't need sub-agents.
---

# Code Review — Lightweight Mode

Quick standalone review. For heavy multi-agent review with sub-agents, autofix, and confidence gates → see `ce-code-review`.

## When to Use Which

| Situation | Use |
|-----------|-----|
| Small diff (<50 lines), quick look | `code-reviewing` (lightweight) |
| Large diff, complex change, needs security/perf review | `ce-code-review` (multi-agent) |
| Automated CI/CD pipeline | `ce-code-review mode:headless` |
| Manual checklist review | `code-reviewing` + 11 dimensions below |

## 11 Review Dimensions

### 1. Architectural Patterns
- Evaluate adherence to established patterns (MVC, MVVM, Clean Architecture)
- Assess design patterns usage (Factory, Strategy, Observer)
- Verify layer separation and dependency direction
- Check for anti-patterns (circular deps, god objects, tight coupling)

### 2. Separation of Concerns
- Single responsibility principle compliance
- Module boundaries and cohesion
- Business logic vs presentation logic separation
- Data layer abstraction

### 3. Code Readability & Maintainability
- Naming conventions, file structure
- Comment quality (focus on "why", not "what")
- DRY principle, complexity metrics
- No magic numbers → named constants

### 4. Error Handling & Logging
- Error propagation strategy
- Try-catch for fallible ops (API, DB, I/O)
- Structured logging, not console.log
- No empty catch blocks, no PII in logs
- Correlation IDs through call chain

### 5. Type Safety (typed languages)
- No `any` / loose typing
- Interface and type alias design
- Null/undefined handling
- Type assertions justified

### 6. Testing Coverage
- Unit tests for business logic, validations, error handling
- Integration tests when mocking >3 deps
- Edge cases and error scenarios

### 7. Dependencies
- New deps justified and vetted
- Security vulnerabilities checked
- Bundle size impact assessed

### 8. Security
- No hardcoded secrets
- Input validation and sanitization
- Auth/authz logic reviewed
- Injection, XSS, CSRF checked

### 9. Performance
- N+1 queries, algorithmic complexity
- Unnecessary re-renders / recomputations
- Memory leak risks
- Cache expensive computations

### 10. Cross-File Consistency
- Function signatures verified against definitions
- Import paths valid
- Types match across boundaries

### 11. Resource Management
- Heavy resources (DB pools, ML models, browser instances) as singletons
- Resource lifecycle: open → use → close
- No leaks (connections, handles, cursors)

## Dimension Prioritization

| Context | Prioritize |
|---------|------------|
| Auth/login | Security (8), Error Handling (4) |
| User input | Security (8), Type Safety (5) |
| Database queries | Security (8), Performance (9) |
| New feature | Architecture (1), Testing (6) |
| Refactoring | Cross-File (10), Testing (6) |
| ML/AI pipeline | Resource Mgmt (11), Performance (9) |

## Severity

| Level | Meaning |
|-------|---------|
| **critical** | Must fix — crash, vuln, data loss |
| **major** | Should fix — significant concern |
| **minor** | Nice to have — improvement |
