# Runbook: OpenCode Provider Verification

Last updated: 2026-06-25

Server A: `{SERVER_A_IP}`

## Important Paths

| Path | Purpose |
|---|---|
| `/root/.config/opencode/opencode.json` | OpenCode config |
| `/root/.opencode-secrets` | OpenCode API keys, sourced before CLI checks |

## Current Model Setup

Default model in `/root/.config/opencode/opencode.json`:

```json
"model": "deepseek/deepseek-chat"
```

OpenRouter key is available in `/root/.opencode-secrets`.

OpenCode auto-fallback was not found in the installed config/schema during the 2026-06-25 check. OpenRouter is available as an explicit alternate model:

```bash
source /root/.opencode-secrets
cd /home/ubuntu/framework
opencode run -m openrouter/deepseek/deepseek-chat 'Reply with OK only.' </dev/null
```

## Smoke Tests

Default DeepSeek:

```bash
source /root/.opencode-secrets
cd /home/ubuntu/framework
opencode run --format json 'Reply with OK only.' </dev/null
```

Explicit direct DeepSeek:

```bash
source /root/.opencode-secrets
cd /home/ubuntu/framework
opencode run --format json -m deepseek/deepseek-chat 'Reply with OK only.' </dev/null
```

Explicit OpenRouter fallback:

```bash
source /root/.opencode-secrets
cd /home/ubuntu/framework
opencode run --format json -m openrouter/deepseek/deepseek-chat 'Reply with OK only.' </dev/null
```

Verified on 2026-06-25:

- OpenCode version: `1.17.10`
- Default `deepseek/deepseek-chat`: returns `OK`
- Explicit `deepseek/deepseek-chat`: returns `OK`
- Explicit `openrouter/deepseek/deepseek-chat`: returns `OK`

## Gotchas

- Close stdin with `</dev/null` for non-interactive SSH smoke tests.
- `opencode models` validates config before listing models.
- `model` must be a string such as `deepseek/deepseek-chat`; object-style model config is invalid for the installed OpenCode version.
- `prod-ssh` MCP is present but disabled on Server A by policy.

