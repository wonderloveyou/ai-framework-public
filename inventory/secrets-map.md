# Secrets Map

Last updated: 2026-06-25

This file tracks secret names and storage locations only. Do not write secret values here.

## Rules

- Never commit `.env`, auth JSON files, API keys, tokens, cookies, browser profiles, or private SSH keys.
- Use environment variables or `{env:VAR_NAME}` references in configs.
- Keep server secret files `chmod 600`.
- Rotate any secret that accidentally appears in git, chat logs, or screenshots.

## Server A

| Name | Purpose | Storage | Notes |
|---|---|---|---|
| `TELEGRAM_BOT_TOKEN` | Hermes Telegram gateway | `/opt/hermes/.env` | Value not stored in git |
| `TELEGRAM_ALLOWED_USERS` | Telegram allowlist | `/opt/hermes/.env` | User id only, no token |
| `GATEWAY_ALLOW_ALL_USERS` | Gateway access mode | `/opt/hermes/.env` | Review security posture later |
| `OPENAI_API_KEY` | Hermes OpenAI-compatible DeepSeek key | `/opt/hermes/.env` | Used with DeepSeek base URL |
| `OPENAI_BASE_URL` | DeepSeek OpenAI-compatible endpoint | `/opt/hermes/.env` | Expected: `https://api.deepseek.com/v1` |
| `DEEPSEEK_API_KEY` | Direct DeepSeek API key | `/opt/hermes/.env`, `/root/.opencode-secrets` | Same provider family; do not commit |
| `OPENROUTER_API_KEY` | OpenRouter fallback/alternate provider | `/root/.opencode-secrets`, `/opt/hermes/.env` | Present on Server A as of 2026-06-25; Hermes uses it for fallback, OpenCode can use it with `-m openrouter/...` |
| `GITHUB_TOKEN` / PAT | GitHub MCP and repo access | `/root/.opencode-secrets` | Scope should be minimal |
| `FIRECRAWL_API_KEY` | Firecrawl MCP | `/root/.opencode-secrets` | Optional research tool |
| `PERPLEXITY_API_KEY` | Perplexity MCP | `/root/.opencode-secrets` | Optional research tool |
| `SUPABASE_*` | Supabase MCP | `/root/.opencode-secrets` | Confirm actual usage before enabling broadly |
| `SUPABASE_ACCESS_TOKEN` | Supabase MCP bearer token | `/root/.opencode-secrets` | Missing on Server A as of 2026-06-25; required only if Supabase MCP is used |

## Local Windows

| Secret | Storage | Notes |
|---|---|---|
| Codex auth | `~/.codex/` | Do not copy to git |
| OpenCode auth/config secrets | `%USERPROFILE%\.config\opencode\` | Config may reference env vars |
| SSH private keys | `~/.ssh/` | Use explicit key and `IdentitiesOnly=yes` for Server A |

## To Do

- Confirm exact names in `/root/.opencode-secrets` without printing values.
- Confirm which keys are duplicated across Hermes/OpenCode/Codex and whether separation is needed.
- Add rotation dates and scopes once values are audited manually.
