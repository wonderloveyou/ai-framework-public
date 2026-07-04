---
name: ce-init
description: "Mandatory project initialization and deep context recovery. Runs BEFORE any code work Ч gathers project context, session history, institutional knowledge, Obsidian notes, git state, and MCP memory. Produces a structured context report. Use at session start, after context switch, or when user says 'init', 'bootstrap', 'ориентируйс€', 'введи в контекст', 'покажи полную картину', 'catch me up'."
argument-hint: "[topic or task description]"
---

# ce-init Ч Project Init & Deep Context Recovery

Your FIRST responsibility is understanding the project. Not coding. Not reviewing. UNDERSTANDING.

This is a **mandatory pre-work protocol**. It runs before planning, reviewing, debugging, or implementing. The goal is to build a complete mental model of the project so every subsequent action is grounded in reality, not assumptions.

## Contract

| Aspect | Value |
|--------|-------|
| **Input** | Current working directory, user's task description (optional) |
| **Output** | Structured context report (markdown Ч Phase 3 template) |
| **Side effects** | Read-only. No files modified, no code written, no commits |
| **Guard** | Skip if message is purely conversational ("hello", "how are you") |
| **Time budget** | 2-4 minutes for Phase 0-3; abort and report missing context if stuck |

## Anti-Rationalization

Common excuses the agent will use to skip this protocol Ч and why each is wrong:

| Excuse | Reality |
|--------|---------|
| "I already know this project" | Memory from last session is stale. Git status, recent commits, and Obsidian notes may have changed. Verify. |
| "This is a simple fix, I don't need context" | Simple fixes break things when context is missing. 2 minutes of init saves hours of debugging. |
| "I'll check git/obsidian later" | Later = never. The first action sets the trajectory. Context gathered late is context gathered wrong. |
| "Obsidian/MCP memory is optional" | It's your long-term memory. Without it, you repeat mistakes and forget decisions. |
| "The user seems to be in a hurry" | A broken fix wastes more time than a 2-minute init. Speed comes from accuracy. |
| "I read CLAUDE.md, that's enough" | CLAUDE.md is 10% of the picture. Git state, sessions, Obsidian, memory fill the other 90%. |
| "I'll produce the report after I start coding" | The report IS your mental model. Code written without it is code written blind. |

## Guardrails

- **Never skip phases.** Each phase feeds the next. Skipping = blind spots.
- **Verify, don't assume.** If a source doesn't exist, note its absence Ч don't hallucinate it.
- **Timebox.** Phase 0-3 should take 2-4 minutes. If stuck, note what's missing and move on.
- **No code changes during init.** This is read-only context gathering.
- **Produce a visible summary.** The user must see what you found before you act on it.

## Execution

### Mode Detection

Pick the right depth based on task signals. Default to **standard** if unsure.

| Mode | Time | When | What's loaded |
|------|------|------|---------------|
| **quick** | 30-60s | Specific file:line reference, simple fix, user says "быстрый init" | Git status + branch + CLAUDE.md + target file |
| **standard** | 2-3 min | General code work, feature, bug, review, PR | All of quick + README + package.json + project memory + Obsidian + MCP memory + sessions |
| **deep** | 3-5 min | Refactoring, architecture, new project, post-break resume, user says "глубокий init" | All of standard + institutional knowledge + ce-learnings-researcher + full architecture scan |

**Auto-detection signals:**

| Signal | > Mode |
|--------|--------|
| `file:line` reference with error message, no architecture/design keywords | quick |
| "fix this", "debug", "review", "implement", "add feature" | standard |
| "refactor", "architecture", "redesign", "plan", "restructure", "new project" | deep |
| User explicitly: "quick init", "быстрый init" | quick |
| User explicitly: "deep init", "глубокий init", "полный init" | deep |
| After session break >24h, after /compact | deep |
| Task mentions >10 files or cross-module changes | deep |

### Phase 0: Environment Identification (15 sec)

Determine WHERE we are working.

1. **Identify the project root:**
   - Note the current working directory from session metadata
   - Walk upward to find `.git/`, `package.json`, `CLAUDE.md`, or `AGENTS.md`

2. **Determine the project name.** From directory name, `package.json#name`, or `CLAUDE.md` header.

3. **Quick git pulse.** Use the bash tool to run these commands:
   ```
   git status --short
   git log --oneline -5
   git branch --show-current
   ```
   If not a git repo, note it and skip git-dependent steps.

4. **Identify project type** (pick one): `node/ts`, `python`, `rust`, `n8n-workflow`, `docs-content`, `mixed`, `unknown`.

5. **MCP availability check** (quick, do not fail):
   - Try `obsidian_search_notes query="test" limit=1` Ч note if Obsidian MCP is reachable
   - Try `memory_search_nodes query="test"` Ч note if Memory MCP is reachable
   - If either is unreachable: note in the context report, continue without them. Never block init on MCP failure.

### Phase 1: Project Surface Scan (mode-dependent)

Read project-defining files. What you read depends on mode:

**Quick mode** Ч read only:
1. **`CLAUDE.md`** or **`AGENTS.md`** (MANDATORY)
2. **Target file** (if task references a specific file)

**Standard mode** Ч read all:
1. **`CLAUDE.md`** or **`AGENTS.md`** (MANDATORY)
2. **`README.md`**
3. **`package.json`** / `pyproject.toml` / `Cargo.toml`
4. **`CONTEXT.md`** or **`ARCHITECTURE.md`** Ч if they exist
5. **`MEMORY.md`** Ч Claude Code auto-memory
6. **Project-specific memory files** Ч `project_*.md` in Claude projects directory
7. **Directory structure** Ч top-level listing

**Deep mode** Ч all of standard plus:
8. **`ROADMAP.md`** or **`TODO.md`**
9. **`SOUL.md`** + **`STYLE.md`** Ч if present
10. **Full architecture** Ч read every file in `src/` or equivalent structure

If a file exists but is too large (>500 lines), read the first 100 lines and note the rest was skipped.

### Phase 2: Deep Context Recovery (mode-dependent)

**Quick mode:** Skip Phase 2 entirely. Go to Phase 3.

**Standard mode:** Recover context from external stores. If MCP is unavailable, skip that source Ч do not block.

#### 2a. Obsidian Vault (standard + deep, skip if MCP unavailable)

Vault root: `{OBSIDIAN_VAULT}`

Search for project-related notes using the `obsidian_search_notes` tool:
```
obsidian_search_notes query="<project-name>" limit=5
obsidian_search_notes query="<repo-folder-name>" limit=5
```

Read the top 2-3 matching notes using `obsidian_read_note` for full context.

#### 2b. MCP Memory

Check the knowledge graph for persistent project memory using the `memory_search_nodes` tool:
```
memory_search_nodes query="<project-name>"
```
If relevant entities found, use `memory_open_nodes` to read their observations.

#### 2c. Session History

Use the `ce-sessions` skill to find recent work on this project:
- Search for sessions in the last 7 days
- Extract: what was worked on, what broke, what decisions were made

#### 2d. Institutional Knowledge

If `docs/solutions/` exists in the project, search for relevant learnings:
- Check for `docs/solutions/patterns/critical-patterns.md` (if exists, read it)
- Search for patterns related to the current task area

If the `ce-learnings-researcher` agent is available, dispatch it with the work context.

**Deep mode only:** Additional sources beyond standard.

#### 2e. Institutional Knowledge (deep only)

### Phase 3: Context Synthesis (30 sec)

Produce a **structured context report**. This is the output the user sees AND the foundation for all subsequent work.

**Do NOT proceed to any code work until this report is produced.**

Use this exact structure:

```markdown
## Init: <project-name>

### Project Identity
- **What it does:** <1-line description>
- **Stack:** <languages, frameworks, key dependencies>
- **Type:** <node/ts | python | n8n | docs | other>
- **Current branch:** <branch> | **Status:** <clean/dirty N files>

### Recent Activity
- **Last commits:** <summary of recent git activity, last 5 commits>
- **Recent sessions:** <what was worked on in prior sessions, if found>
- **Active workstream:** <what feature/bug/fix is in progress>

### Knowledge Base
- **Obsidian notes:** <list of relevant vault notes found, with paths>
- **Memory nodes:** <relevant knowledge graph entries>
- **Institutional knowledge:** <relevant learnings from docs/solutions/>

### Project Structure
\```
<top-level directory tree, key folders only, max 15 lines>
\```

### Key Constraints
- **Rules from CLAUDE.md:** <extracted hard rules>
- **Architectural decisions:** <known ADRs or constraints>
- **Active risks:** <uncommitted changes, known bugs, merge conflicts, etc.>

### Missing Context
- <anything you couldn't find but suspect exists>
- <questions to ask the user before proceeding>
```

### Phase 4: Task Integration

Now, and ONLY now, integrate the user's request with the recovered context.

1. **Restate the user's task** in 2-5 bullets, mapped to the project context.
2. **Challenge the approach** Ч is this the right thing to do given the context? Are there simpler alternatives?
3. **Identify risks** Ч what could break? What regressions are possible?
4. **Propose a plan** Ч 3-7 concrete steps, referencing specific files and patterns found.
5. **Ask for confirmation** if the task is ambiguous or high-risk.

### Phase 5: Decision Gate

Before writing a single line of code:

- [ ] I understand what the project does
- [ ] I know what was recently changed and why
- [ ] I've searched for existing implementations of this
- [ ] I've checked for relevant past decisions/learnings
- [ ] I've identified potential regressions
- [ ] I've considered simpler alternatives
- [ ] I have a concrete plan

If any box is unchecked, go back and fill the gap. Do NOT start coding.

## Anti-Patterns

- **"I'll figure it out as I go"** Ч No. Context first, then action.
- **Reading only CLAUDE.md and starting to code** Ч Missing 80% of context.
- **Assuming the project is in a clean state** Ч Always check git status.
- **Ignoring Obsidian/memory because it's "optional"** Ч It's not. It's your long-term memory.
- **Producing a report but not reading it** Ч The report IS your mental model. Reference it.

## Integration Points

This skill is the entry point for ALL work. It should be invoked:

- **Automatically** Ч by the rule in CLAUDE.md: "при любой задаче с кодом/проектом сначала загрузи ce-init"
- **Manually** Ч by the user saying: "init", "bootstrap", "ориентируйс€", "catch me up", "введи в контекст"
- **On context switch** Ч when the user changes topic or project directory
- **After /compact** Ч to rebuild context that may have been compressed away

## Output

Always produce the Phase 3 structured context report. Then proceed to Phase 4-5.

If the user's message is purely conversational ("hello", "how are you"), skip init and respond normally. But if the conversation turns to any project work, run init first.

## Windows Notes

- All paths use Windows format (backslashes or forward slashes, both accepted by the bash tool)
- Git commands run in PowerShell via the bash tool Ч stderr is captured automatically, no `2>/dev/null` needed
- Tilde paths in CLAUDE.md are resolved by the shell; use absolute paths when in doubt
- The `bash` tool wraps PowerShell 5.1 Ч use `; if ($?) { ... }` for sequential commands
