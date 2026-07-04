# AI Framework

Personal AI agent framework for development, automation, knowledge work, and VPS-based always-on tooling.

This repository stores documentation, plans, runbooks, inventories, and skill-routing metadata for a private AI infrastructure built around OpenCode, Codex, Hermes Agent, n8n, Obsidian, and MCP tools.

## Current Status

Core Server A is running:

- Hermes Agent with Telegram gateway
- Direct DeepSeek API through OpenAI-compatible provider
- OpenCode with MCP tooling
- Codex installed and logged in through device auth; `doctor`, MCP list, and `exec` smoke test pass
- Hermes has OpenRouter fallback configured
- OpenCode default DeepSeek and explicit OpenRouter model smoke tests pass
- n8n
- Uptime Kuma
- rsshub
- PostgreSQL container

The current operational plan is in [ROADMAP.md](ROADMAP.md).

## Repository Map

| Path | Purpose |
|---|---|
| [AGENTS.md](AGENTS.md) | Master instructions for AI agents |
| [CONTEXT.md](CONTEXT.md) | Project identity, goals, constraints |
| [ROADMAP.md](ROADMAP.md) | Current phases, status, immediate next actions |
| [migration-plan.md](migration-plan.md) | Detailed migration architecture and long-form plan |
| [skills/SKILL_INDEX.md](skills/SKILL_INDEX.md) | Lightweight skill router for progressive disclosure |
| [inventory/](inventory/) | Server, MCP, and secret-name inventory without secret values |
| [docs/runbooks/](docs/runbooks/) | Operational procedures |
| [docs/notes/](docs/notes/) | Status notes and decisions |
| [configs/platforms/](configs/platforms/) | Platform-specific setup notes |
| [tasks/](tasks/) | Task prompts for focused work |
| [raw-info/](raw-info/) | Read-only research archive |

## Infrastructure

| Role | Notes |
|---|---|
| AI Tools | Hermes, OpenCode, Codex, n8n, Uptime Kuma, rsshub, PostgreSQL |
| Projects | Bots, FastAPI apps, MySQL, project runtime |
| VPN + Monitoring | WireGuard/Amnezia, monitoring role |

See [inventory/servers.md](inventory/servers.md) for the current sanitized inventory.

## Safety Rules

- Never commit `.env`, API keys, tokens, auth JSON, cookies, browser profiles, or private SSH keys.
- Keep secret files on servers with `chmod 600`.
- Store secret names and locations only in [inventory/secrets-map.md](inventory/secrets-map.md).
- Do not edit `raw-info/`; it is a research archive.
- Do not truncate `SKILL.md` files. Move or archive whole skills only.
- Load [skills/SKILL_INDEX.md](skills/SKILL_INDEX.md) before opening full skill files.

## Common Runbooks

- [Hermes provider switch](docs/runbooks/hermes-provider-switch.md)
- [Server A backup and restore](docs/runbooks/server-a-backup-restore.md)

## Immediate Next Actions

1. Create or verify `SKILL_INDEX.md` in actual OpenCode and Codex skill directories.
2. Verify `SUPABASE_ACCESS_TOKEN` exists on Server A if Supabase MCP is needed.
3. Continue Server B projects inventory.
4. Commit/push current documentation changes when ready.

## Non-Goals

- This is not a production SaaS.
- This is not a multi-user platform.
- This does not replace IDE copilots; it coordinates personal agent workflows and infrastructure.
