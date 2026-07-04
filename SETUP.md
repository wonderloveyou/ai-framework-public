# AI Agent Framework — Quick Setup

This framework gives your AI agent (OpenCode, Codex, Claude Code, Hermes) a complete skill system, MCP server configuration, and knowledge management pipeline.

## Quick Start

```bash
git clone https://github.com/wonderloveyou/ai-framework-public.git
cd ai-framework-public
```

## Structure

```
├── AGENTS.md           # Master instruction for AI agents
├── SETUP.md            # This file
├── configs/
│   ├── mcp-servers.md  # All MCP servers with configs
│   └── platforms/
│       └── linux-setup.md  # VPS/bootstrap setup
├── docs/
│   ├── patterns/       # Reusable agent patterns
│   │   ├── autoresearch-agent.md
│   │   ├── researcher.md
│   │   └── brief-expander.md
│   └── runbooks/       # Operational guides
│       ├── hermes-integrations.md
│       ├── hermes-moa-setup.md
│       └── server-a-backup-restore.md
├── inventory/
│   └── mcp.md          # MCP profiles by risk level
└── skills/
    └── SKILL_INDEX.md  # Skill router — load this first
```

## For AI Agents

When starting work in this repo:

1. Read `AGENTS.md` — master rules for all agents
2. Read `skills/SKILL_INDEX.md` — find the right skill for your task
3. Load the full skill when a trigger matches

### Setup Checklist

- [ ] **Skills path**: Point your agent to `skills/` or copy `SKILL_INDEX.md` to your config
- [ ] **MCP servers**: Configure from `configs/mcp-servers.md` — pick your profile (research/coding/deploy)
- [ ] **API keys**: Set `{env:VAR}` variables for each MCP server you enable
- [ ] **Obsidian vault** (optional): Point `{OBSIDIAN_VAULT}` to your vault path
- [ ] **Autoresearch** (optional): Set up cron for weekly research via `docs/patterns/autoresearch-agent.md`

## Quick Integration Reference

| What | Config |
|------|--------|
| **Skills** | Point agent to `skills/` directory |
| **Code review** | `ce-code-review` (heavy) or `code-reviewing` (light) |
| **Planning** | `ce-plan` (universal) or user-spec → tech-spec pipeline |
| **Research** | `perplexity` + `firecrawl` MCP + `skill-research` |
| **Autoresearch** | Weekly cron → Perplexity → Obsidian vault pipeline |
| **VPS setup** | `configs/platforms/linux-setup.md` |
| **Hermes** | Desktop app (Mac/Windows) or Docker on VPS |

## For Human Maintainers

- Replace `{SERVER_X_IP}`, `{VAULT_PATH}`, `{OBSIDIAN_VAULT}`, `{USER_HOME}` in configs with your actual values
- Add your API keys to environment variables (never commit them)
- Run `ermes doctor --fix` if Hermes has issues
