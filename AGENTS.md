# AGENTS.md — AI Framework Master Instruction

## What this is
AI agent framework — configuration, skills, and infrastructure for OpenCode, Codex, Hermes, n8n.
Cross-platform: Windows (primary), macOS (secondary), Linux VPS (target).

## Repository structure
```
framework/
  AGENTS.md              — this file (master instruction)
  CONTEXT.md             — project identity: what, who, constraints
  ROADMAP.md             — phases & priorities
  migration-plan.md      — detailed VPS migration plan
  configs/
    platforms/
      linux-setup.md     — Linux-specific paths/packages/systemd (open ONLY on Linux)
      macos-setup.md     — macOS-specific (open ONLY on macOS)
  skills/
    SKILL_INDEX.md       — skill router (load this, not individual SKILL.md files)
    _archive/            — archived/duplicate skills (DO NOT LOAD)
  scripts/               — install/deploy shell scripts
  docs/                  — documentation, runbooks, decisions
  raw-info/              — research sources (read-only reference)
  tasks/                 — task prompts for sub-agents
```

## Core rules (for ALL agents)

### NEVER
- Commit .env, tokens, API keys, or secrets to git
- Hardcode keys in config files — use {env:VAR_NAME} or environment variables
- Delete or modify raw-info/ — it is research archive
- Truncate/cut content inside SKILL.md files — only move or merge
- Modify Codex .system/ directory

### ALWAYS
- Load SKILL_INDEX.md first, not individual skills
- Load full SKILL.md only when task matches trigger condition
- Log decisions to docs/ or Obsidian vault
- Use platform-specific configs (configs/platforms/) on the right OS

### Skills loading
1. Read SKILL_INDEX.md (~2K tokens)
2. When task matches a trigger > load that SKILL.md
3. All skills intact in their directories
4. Archived skills in _archive/ — DO NOT load

## Platform detection
| OS | Config path | Shell |
|----|-------------|-------|
| Windows | %USERPROFILE%\.config\opencode\ | PowerShell / cmd |
| macOS | ~/.config/opencode/ | zsh / bash |
| Linux | ~/.config/opencode/ | bash |

## Servers (3 VPS)
| IP | Role | RAM | Access |
|----|------|-----|--------|
| {SERVER_A_IP} | AI Tools (Hermes, OpenCode, Codex, n8n) | 12GB | SSH + WireGuard |
| {SERVER_B_IP} | Projects (bots, sites, MySQL) | 1.9GB | SSH |
| {MONITOR_IP} | VPN + Monitoring | ? | WireGuard |

## Key integrations
- **Obsidian vault:** `{OBSIDIAN_VAULT_PATH}` (Windows)
- **OpenRouter:** primary model DeepSeek V4 Pro
- **Hermes Agent:** 24/7, Telegram Gateway, Docker on {SERVER_A_IP}
- **NotebookLM:** separate Google account, notebooklm-mcp-server + notebooklm-py
- **RAG:** Obsidian as data-layer, Qdrant/Chroma optional

