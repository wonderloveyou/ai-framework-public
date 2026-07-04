# MCP Inventory

Last updated: 2026-06-25

## Policy

MCP servers should be grouped by exposure level. Dangerous write/deploy tools should be enabled explicitly for a task, not kept as default always-on tools.

Decision on 2026-06-25: production SSH MCP stays on local Windows only. It should not be enabled on Server A because the AI tools server already has broad local context and does not need direct production SSH delegation.

## Desired Profiles

| Profile | MCP servers | Use |
|---|---|---|
| `research` | `context7`, `perplexity`, `firecrawl`, `memory` | Default research and documentation work |
| `coding` | `filesystem`, `github`, `sequential-thinking` plus research tools | Code changes and repo work |
| `deploy-readonly` | read-only SSH checks, Docker status, logs | Deployment inspection |
| `deploy-write` | SSH restart/deploy, browser checks | Explicit approval only |
| `notebooklm` | NotebookLM bridge tools | Future phase, separate Google account |

## Server A OpenCode MCP

Configured in `/root/.config/opencode/opencode.json`:

- `filesystem`
- `sequential-thinking`
- `context7`
- `firecrawl`
- `memory`
- `github`
- `perplexity`
- `supabase`
- `prod-ssh` - present but disabled on 2026-06-25

Supabase MCP should use `Bearer {env:SUPABASE_ACCESS_TOKEN}`, not a hardcoded bearer value.

## Server A OpenCode Models

OpenCode config: `/root/.config/opencode/opencode.json`.

Current default model:

```text
deepseek/deepseek-chat
```

Verified alternate OpenRouter model:

```bash
source /root/.opencode-secrets
cd /home/ubuntu/framework
opencode run -m openrouter/deepseek/deepseek-chat 'Reply with OK only.' </dev/null
```

Result verified on 2026-06-25: returns `OK`.

Current note: no active OpenCode auto-fallback config was found. Use OpenRouter explicitly with `-m openrouter/deepseek/deepseek-chat` when direct DeepSeek is unavailable.

## Server A Codex MCP

Configured in `/root/.codex/config.toml`.

Status: installed, but needs verification after interactive `codex login`.

## Local Windows Codex MCP

Known active tools from project context:

- `playwright`
- `filesystem`
- `sequential-thinking`
- `memory`
- `context7`
- `openai-docs`
- `git`
- `chrome-devtools`

Status: local working environment, not yet converted into a repo-managed template. Production SSH MCP belongs here when explicitly needed.
