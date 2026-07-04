# Runbook: Hermes Provider Switch

Last updated: 2026-06-25

## Current Working Setup

Server: `{SERVER_A_IP}`

Hermes currently works with direct DeepSeek API through Hermes' OpenAI-compatible provider.

```yaml
llm:
  provider: openai
  model: deepseek-chat
  api_key_env: OPENAI_API_KEY
  base_url: https://api.deepseek.com/v1
```

Current fallback chain verified on 2026-06-25:

```yaml
fallback_providers:
  - provider: openrouter
    model: deepseek/deepseek-chat
```

The fallback key is stored as `OPENROUTER_API_KEY` in `/opt/hermes/.env`.

## SSH

Use the explicit key and `IdentitiesOnly=yes`:

```powershell
ssh -o IdentitiesOnly=yes -i {USER_HOME}\.ssh\id_ed25519 root@{SERVER_A_IP}
```

## Verify Current Status

```bash
docker exec hermes_agent env | sed -n 's/^OPENROUTER_API_KEY=.*/OPENROUTER_API_KEY=set/p'
docker exec hermes_agent hermes fallback list
docker exec hermes_agent hermes status
docker exec hermes_agent hermes -z 'Reply with OK only.' --cli -t ''
```

Expected model status:

- `Model: deepseek-chat`
- `Provider: openai`
- fallback chain includes `deepseek/deepseek-chat (via openrouter)`

Expected CLI test response:

```text
OK
```

## Switch To Direct DeepSeek

1. Back up config:

```bash
cp /root/.hermes/config.yaml /root/.hermes/config.yaml.bak-$(date +%Y%m%d-%H%M%S)
```

2. Set config:

```yaml
llm:
  provider: openai
  model: deepseek-chat
  api_key_env: OPENAI_API_KEY
  base_url: https://api.deepseek.com/v1

model:
  default: deepseek-chat
  provider: openai
  base_url: https://api.deepseek.com/v1
```

3. Clear cached session provider/model state:

```bash
mv /root/.hermes/sessions/sessions.json /root/.hermes/sessions/sessions.json.bak-$(date +%Y%m%d-%H%M%S) 2>/dev/null || true
```

4. Restart:

```bash
docker restart hermes_agent
```

5. Verify:

```bash
docker exec hermes_agent hermes -z 'Reply with OK only.' --cli -t ''
docker exec hermes_agent hermes status
```

## Switch To OpenRouter Fallback

Only do this if `OPENROUTER_API_KEY` exists in the Hermes environment file.

Use fully qualified OpenRouter model ids:

```yaml
llm:
  provider: openrouter
  model: deepseek/deepseek-chat
  api_key_env: OPENROUTER_API_KEY
```

Do not use short `deepseek-chat` with OpenRouter.

## Add OpenRouter As Fallback

If `OPENROUTER_API_KEY` exists elsewhere on the server, add it to `/opt/hermes/.env` without printing the value.

Example if it exists in `/root/.opencode-secrets`:

```bash
grep '^OPENROUTER_API_KEY=' /root/.opencode-secrets >> /opt/hermes/.env
```

Then add:

```yaml
fallback_providers:
  - provider: openrouter
    model: deepseek/deepseek-chat
```

Because `compose.yaml` uses `env_file`, recreate the container after changing `/opt/hermes/.env`:

```bash
cd /opt/hermes
/usr/local/bin/docker-compose down
/usr/local/bin/docker-compose up -d
```

## Known Pitfalls

- Do not set `llm.provider: deepseek`; Hermes does not treat it as a built-in provider.
- `docker-compose restart` does not reload changed `env_file`; use down/up when environment values change.
- Hermes sessions can cache provider/model state, so move `sessions.json` after provider changes.
- If Telegram asks for a home channel after session reset, run `/sethome` again.

