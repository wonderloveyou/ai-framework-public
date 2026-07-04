# Init Output Template — ce-init

Canonical output format for ce-init Phase 3 — Context Synthesis.

## Rules

- Fill every section that has data. Use `—` for empty sections (don't omit).
- Be specific: "Stack: TypeScript, Next.js 14, PostgreSQL, Prisma" not "Stack: web app".
- Under "Missing Context", list what you need from the user before proceeding.
- Keep the report visible — it's your working memory for the rest of the session.

## Template

```markdown
## Init: <project-name>

### Project Identity
- **What it does:** <1-line description>
- **Stack:** <languages, frameworks, key dependencies>
- **Project type:** <node/ts | python | n8n-workflow | docs-content | mixed | unknown>
- **Current branch:** `<branch>` | **Git status:** <clean | dirty: N files modified>

### Recent Activity
- **Last commits (5):**
  1. `<hash>` — <message>
  2. ...
- **Recent sessions (7 days):** <summary or "no prior sessions found">
- **Active workstream:** <feature name, bug fix, or "unclear — ask user">

### Knowledge Base
- **Obsidian notes:**
  - `<path>` — <one-line description>
- **MCP Memory:** <relevant entities and observations, or "empty">
- **Institutional knowledge:** <relevant learnings, or "none found">

### Project Structure
\```
<key directories only, 15 lines max>
\```

### Key Constraints
- **Rules from project config:** <extracted hard rules>
- **Architectural decisions:** <known constraints or ADRs>
- **Active risks:** <uncommitted changes, known bugs, merge conflicts>

### Missing Context
- <what you couldn't find but suspect exists>
- <questions to clarify with the user>
```

## Example (filled)

```markdown
## Init: alive-web

### Project Identity
- **What it does:** Interactive 3D globe visualization with WebGL, served as static site
- **Stack:** TypeScript, Three.js, Vite, GLSL shaders
- **Project type:** node/ts
- **Current branch:** `feat/globe-textures` | **Git status:** dirty: 3 files modified

### Recent Activity
- **Last commits (5):**
  1. `a1b2c3d` — fix: globe rotation on mobile
  2. `e4f5g6h` — feat: add country highlight on hover
- **Recent sessions (7 days):** 3 sessions — texture optimization, mobile perf, hover refactor
- **Active workstream:** globe texture atlas optimization

### Knowledge Base
- **Obsidian notes:**
  - `Jarvis/03_wiki/projects/aliveweb-vpn.md` — project overview and architecture
  - `Jarvis/05_logs/2026-06-07-texture-optimization.md` — last session
- **MCP Memory:** 2 entities — "alive-web", "threejs-texture-pipeline"
- **Institutional knowledge:** none found

### Project Structure
\```
src/
  globe/       — Three.js globe rendering
  shaders/     — GLSL vertex/fragment shaders
  ui/          — HTML/CSS overlay
  data/        — GeoJSON and texture data
public/        — static assets
\```

### Key Constraints
- **Rules:** caveman mode, no README unless asked, no comments unless unclear
- **Architecture:** Globe singleton, shader pipeline separated from JS logic
- **Active risks:** 3 uncommitted files, texture loading race condition known

### Missing Context
- Are there design specs for the new texture format?
- Should the hover detection be extracted to a separate module?
```
