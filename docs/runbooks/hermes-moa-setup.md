# Hermes Mixture of Agents (MoA) Setup

**Date:** 2026-06-28
**Status:** ready to deploy

## What it is

MoA — virtual model provider. Reference-модели (советники, без инструментов) анализируют запрос → Aggregator (исполнитель, с инструментами) выдаёт финальный ответ. Панель бьёт одиночку: Opus 4.8 + GPT-5.5 = 0.8202 vs Opus solo = 0.7607 на HermesBench.

## Prerequisites

- Hermes Agent updated: `hermes update` (or Dashboard → Manage → Update Hermes)
- OPENROUTER_API_KEY in `/opt/hermes/.env` (already present on Server A)
- DEEPSEEK_API_KEY in `/opt/hermes/.env` (already present — used as OPENAI_API_KEY with base_url)

## Recommended Preset: DeepSeek V4 Pro + Gemini 2.5 Flash

| Role | Model | Provider | Cost | Why |
|------|-------|----------|------|-----|
| Aggregator | `deepseek-v4-pro` | OpenRouter | ~$0.20/$0.70 | Main model, tools, final answer |
| Reference | `gemini-2.5-flash` | OpenRouter | $0.30/$2.50 | Different perspective, cheap |

Reference работает без инструментов (короткий контекст, только user/assistant текст) — дёшево. Aggregator получает анализ reference и выдаёт финальный ответ с инструментами.

### Why Gemini 2.5 Flash as reference

- $0.30/M input, $2.50/M output — дешёво для reference-роли
- 1M контекст
- Другая архитектура (Google) — реально другая перспектива
- Быстрый (0.45s latency)

## Step 1: Update Hermes on Server A

```bash
ssh -o IdentitiesOnly=yes -i {USER_HOME}\.ssh\id_ed25519 root@{SERVER_A_IP}
hermes update
docker restart hermes_agent
```

## Step 2: Add MoA config to /root/.hermes/config.yaml

```yaml
# /root/.hermes/config.yaml — add at the end

moa:
  default_preset: deepseek-gemini
  presets:
    deepseek-gemini:
      reference_models:
        - provider: openrouter
          model: google/gemini-2.5-flash
      aggregator:
        provider: openrouter
        model: deepseek/deepseek-v4-pro
      reference_temperature: 0.6
      aggregator_temperature: 0.3
      max_tokens: 4096
      enabled: true
```

Note: using OpenRouter for both so MoA resolves credentials from OPENROUTER_API_KEY in `/opt/hermes/.env`.

### Alternative: direct DeepSeek API as aggregator (cheaper)

```yaml
moa:
  default_preset: deepseek-gemini
  presets:
    deepseek-gemini:
      reference_models:
        - provider: openrouter
          model: google/gemini-2.5-flash
      aggregator:
        provider: openai
        model: deepseek-chat
        base_url: https://api.deepseek.com/v1
        api_key_env: OPENAI_API_KEY
      reference_temperature: 0.6
      aggregator_temperature: 0.3
      max_tokens: 4096
      enabled: true
```

**Recommendation:** use direct DeepSeek API for aggregator — same model, cheaper than through OpenRouter. Gemini reference через OpenRouter отдельно.

## Step 3: Restart and verify

```bash
# Backup old config
cp /root/.hermes/config.yaml /root/.hermes/config.yaml.bak-moa-$(date +%Y%m%d)

# Restart
docker restart hermes_agent

# Wait 10s, then verify
sleep 10
docker exec hermes_agent hermes moa list
docker exec hermes_agent hermes status
```

## Step 4: Test MoA

```bash
# In Telegram: /model default --provider moa
# Then send a complex question to test

# Or via CLI:
docker exec hermes_agent hermes -z "Explain the trade-offs between B-tree and LSM-tree indexes in 3 sentences." --cli -t ''
```

## Step 5: Verify quality

```bash
# One-shot MoA (doesn't change model):
# In Telegram: /moa explain the CAP theorem and why it matters for distributed databases

# Or set permanently:
# /model default --provider moa
```

## Daily workflow

```bash
# Switch to MoA for complex tasks
/model default --provider moa

# Switch back to plain DeepSeek for simple chats
/model deepseek-v4-pro --provider openrouter

# One-shot MoA (use once, model stays the same)
/moa design a database schema for a blog with comments and tags
```

## Troubleshooting

| Problem | Fix |
|---------|-----|
| MoA not showing in /model | `hermes update` + restart |
| Credential failure on reference | MoA continues with aggregator; check OPENROUTER_API_KEY |
| High token usage | Reference calls are short (no tools, trimmed context); monitor via OpenRouter dashboard |
| MoA preset not found | `hermes moa list` to verify, check config.yaml indentation |

## Related

- Official docs: https://hermes-agent.nousresearch.com/docs/user-guide/features/mixture-of-agents
- ROADMAP.md Phase 3 (AI Tools Migration)
- ROADMAP.md Phase 8.2 (Multi-Agent Orchestration)

