# CONTEXT.md — Framework Identity

## What
AI agent framework — набор инструментов, скиллов, конфигураций и документации для персональной AI-разработки. Объединяет OpenCode, Codex, Hermes, n8n под единой методологией: spec-driven development + compound engineering + second brain.

## Who
Соло-разработчик. Стек: веб (Next.js, React, Python/FastAPI), телеграм-боты, контент-маркетинг, B2B-автоматизация. Работает с Windows 10, MacBook, iPhone. Целевая среда — Ubuntu VPS.

## Current stack
- **AI coding:** OpenCode (DeepSeek V4 Pro), Codex
- **Orchestration:** Hermes Agent (24/7, Telegram)
- **Automation:** n8n
- **Knowledge:** Obsidian (Jarvis vault)
- **Models:** OpenRouter (DeepSeek, MiniMax, GLM, Gemini, Qwen), Replicate (images)
- **Hosting:** 3 VPS (hiplet, serv.host)

## Constraints
- Бюджет ограничен (фриланс/свои проекты)
- VPS: 12GB max на AI-сервере
- Контекстное окно AI-агентов: минимизировать через SKILL_INDEX.md
- Безопасность: никаких ключей в git, отдельные SSH-ключи, MCP-профили
- Кроссплатформенность: Windows сейчас, macOS/Linux в будущем

## Non-goals
- Не продакшен SaaS (это личный инструментарий)
- Не замена GitHub Copilot/Cursor как IDE-плагин
- Не multi-user система
