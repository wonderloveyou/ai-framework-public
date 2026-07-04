# Framework Migration Plan (v3)

## Серверы (3 шт)

| # | IP | Провайдер | RAM | Роль | Что на нём |
|---|----|-----------|-----|------|-----------|
| 1 | {SERVER_X_IP} | hiplet (Финка) | 12GB (апгрейд) | **Server A — AI Tools** | AWG/Xray (уже), + Hermes, OpenCode, Codex, n8n |
| 2 | {SERVER_X_IP} | ? | 1.9GB | **Server B — Projects** | Telegram боты, FastAPI, MySQL |
| 3 | {SERVER_X_IP} | serv.host (Финка) | ? | **VPN + Monitoring** | AWG/Xray (уже), Uptime Kuma |

Сеть: между серверами уже есть WireGuard/Amnezia. {SERVER_X_IP} - {SERVER_X_IP} связаны через VPN.

## Целевая архитектура

```
{SERVER_X_IP} (12GB, Финка) — AI Tools
  +- Hermes Agent (24/7, Telegram Gateway)
  +- OpenCode CLI + MCP
  +- Codex CLI
  +- n8n (Docker)
  L- Некоторые проекты (если хватит места)

{SERVER_X_IP} (1.9GB) — Projects / Bots
  +- Telegram боты (smm4love, cifra, svxbot, dspwbot, wdrlvtgbot)
  +- skachivalka (FastAPI)
  +- MySQL
  L- guardian-ai

{SERVER_X_IP} (Финка) — VPN + Monitoring
  +- AWG + Xray (уже)
  L- Uptime Kuma (мониторинг всех серверов)
```

## MCP-профили (ключевое из ревью)

Не все MCP активны постоянно. Группировка по профилям:

| Профиль | MCP серверы | Когда активен | Риск |
|---------|------------|---------------|------|
| **research** | context7, perplexity, firecrawl, obsidian, memory | Always on | Низкий |
| **coding** | + filesystem, github, sequential-thinking | При работе с кодом | Средний |
| **deploy-readonly** | + ssh (docker ps, git pull) | При проверке деплоя | Средний |
| **deploy-write** | + ssh (restart, deploy), playwright | Явно, с approval | Высокий |
| **notebooklm** | + notebooklm-mcp | Фаза 5, отдельный аккаунт | Средний |

Как работает: в opencode.json все опасные MCP имеют `"enabled": false`. Агент активирует нужный профиль в начале сессии командой `/profile coding` или `/profile infra`.

---

## Фаза 0: Security Foundation (сейчас)
- [ ] Скан секретов в текущих конфигах (opencode.json, .env, codex config)
- [ ] Убрать все ключи из конфигов в env vars
- [ ] .gitignore: секреты, .env, .notebooklm/, ключи
- [ ] Inventory всех API ключей
- [ ] Создать приватное GitHub репо

## Фаза 1: Inventory + Framework (сейчас)
- [ ] AGENTS.md (платформо-нейтральный)
- [ ] SKILL_INDEX.md (1-2 строки на скилл, progressive disclosure)
- [ ] skills/_archive/ — неиспользуемые скиллы
- [ ] CONTEXT.md, ROADMAP.md
- [ ] Threat model: что может пойти не так, кто имеет доступ
- [ ] configs/platforms/linux-setup.md (отдельно, для VPS)
- [ ] configs/platforms/macos-setup.md (на будущее)

## Фаза 2: VPS — подготовка
- [ ] {SERVER_X_IP}: апгрейд RAM до 12GB
- [ ] Отдельные SSH ключи: personal-admin, deploy, github, emergency
- [ ] Docker + Compose, Node.js 20+, Python 3.11+
- [ ] Uptime Kuma на {SERVER_X_IP} (мониторинг с первого дня)
- [ ] Hardening: ufw (только 80/443 + VPN порты), fail2ban
- [ ] OpenRouter fallback: прямой DeepSeek API ключ

## Фаза 3: Server A — AI инструменты (на {SERVER_X_IP})
- [ ] API ключи: .env (chmod 600), НИКОГДА в git, отдельные ключи на инструмент
- [ ] Budget guardrails: max daily spend, алерты при перерасходе
- [ ] Hermes: Docker, Telegram (allowlist: только свой ID, rate limits, audit log)
- [ ] OpenCode: npm install -g, MCP-профили (research по умолчанию)
- [ ] Codex: перенос скиллов, config.toml
- [ ] n8n: Docker (после Hermes, ограниченные права)
- [ ] prod-ssh MCP: остаётся ТОЛЬКО на локальном Windows, не на сервере

## Фаза 4: Server B — Миграция проектов
- [ ] Бэкап {SERVER_X_IP}: mysqldump, конфиги, .env
- [ ] Trial миграция: один простой бот
- [ ] После успеха — остальные сервисы
- [ ] Cutover checklist: DNS, webhooks, nginx, SSL, rollback-план
- [ ] 7-14 дней стабилизации > решение по старому VPS

## Фаза 5: Связность
- [ ] Hermes Telegram: Rich-сообщения (HTML/MarkdownV2, custom emoji)
- [ ] VS Code Remote SSH с MacBook на оба сервера
- [ ] Obsidian Sync для vault (Git для framework)
- [ ] Server A: read-only Obsidian replica
- [ ] RAG: Qdrant/Chroma опционально
- [ ] NotebookLM: отдельный Google аккаунт, P2 (не часть базы)

## Фаза 6: Полировка
- [ ] Claude Code (отдельная задача)
- [ ] IaC/runbooks: bootstrap, backup, restore
- [ ] Disaster recovery: RTO 2ч, RPO 24ч
- [ ] Grafana + алерты (позже)

---

## Бюджет (примерный)
- {SERVER_X_IP}: €?/мес (после апгрейда до 12GB)
- {SERVER_X_IP}: €?/мес
- {SERVER_X_IP}: €?/мес
- OpenRouter: зависит от объёма
- Obsidian Sync: $4/мес (если)
- Итого: надо посчитать точно

## NotebookLM
- `notebooklm-mcp-server` + `notebooklm-py`
- HTTP/RPC (не браузерный бот)
- Отдельный Google аккаунт
- Фаза 5, не критичный компонент

## Ключевые решения
- Server A: {SERVER_X_IP} (12GB), Server B: {SERVER_X_IP}, Monitoring: {SERVER_X_IP}
- MCP-профили: research/coding/deploy-readonly/deploy-write
- prod-ssh: только локально на Windows
- Отдельные SSH ключи
- Uptime Kuma с первого дня
- Obsidian Sync для vault
- NotebookLM: Фаза 5, P2

## Авторисёрч (Karpathy-style) — RAG + бэкап + накопление знаний

### Как это работает
Агент не просто отвечает на запросы — он постоянно пополняет базу знаний:

```
Сессия > агент исследует тему > пишет в Obsidian
         +- 01_raw/      сырые источники, транскрипты
         +- 03_wiki/     очищенные знания, выводы
         L- 05_logs/     решения, противоречия

Следующая сессия > агент читает Obsidian > уже в контексте
```

### Цикл авторисёрча (запускается агентом или cron)

1. **Research** — найти новые источники по активным темам (Perplexity, Firecrawl, YouTube transcripts)
2. **Ingest** — сырые материалы > `01_raw/`
3. **Synthesize** — выделить ключевые выводы > `03_wiki/`
4. **Fact-check** — сверить с существующими заметками, найти противоречия > `05_logs/contradictions.md`
5. **Link** — проставить `[[wiki-ссылки]]` между связанными заметками
6. **Prune** — раз в месяц: устаревшее > `40_archive/`, дубликаты > смержить

### Где RAG в этой картине

Obsidian vault = текстовая база. `obsidian_search_notes` даёт полнотекстовый поиск.
Этого достаточно для 90% задач агента.

Семантический поиск (Qdrant/Chroma) — для специфических кейсов:
- "найди все заметки где обсуждалась производительность, даже если слово не упоминается"
- "какие контент-стратегии мы рассматривали за последний месяц"

Векторный индекс — производная от md-файлов. Не бэкапим отдельно — перестраиваем из vault.

### Бэкап RAG-системы

| Что | Как | RPO | Восстановление |
|-----|-----|-----|---------------|
| Vault (.md) | Obsidian Sync + Git | 1 день | Клонировать репо, открыть в Obsidian |
| Embeddings (Qdrant) | Не бэкапим | — | Перестроить из vault (скрипт) |
| NotebookLM notebooks | Экспорт в md > vault | 1 неделя | Ручной перезапуск |

### Регулярная проверка (cron на Server A)

Еженедельно:
- [ ] `obsidian_search_notes` по темам проектов — не устарели ли выводы?
- [ ] Проверить `contradictions.md` — есть ли неразрешённые?
- [ ] `INDEX.md` актуален?
- [ ] Свежие источники по активным проектам > в `01_raw/`

Ежемесячно:
- [ ] Аудит vault: мёртвые заметки > архив
- [ ] Переиндексация Qdrant (если используется)
- [ ] Экспорт NotebookLM > vault

---

Operational Telegram/user details belong in ignored handoff files or sanitized inventory docs, not in the migration plan. Current repo: `github.com/wonderloveyou/ai-framework`.

