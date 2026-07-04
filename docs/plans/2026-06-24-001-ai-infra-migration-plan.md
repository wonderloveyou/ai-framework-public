# План миграции AI-фреймворка на VPS

Дата: 2026-06-24
Статус: draft

## Цель

Собрать переносимую, безопасную и постоянно доступную AI-разработческую инфраструктуру: агентный сервер, проектный сервер, Obsidian-память, бэкапы, приватный GitHub-репозиторий с инструкциями и конфигами без секретов.

## Не делаем сейчас

- Не ставим Claude Code.
- Не открываем NotebookLM bridge наружу.
- Не переносим секреты через git.
- Не даем агентам root-доступ по умолчанию.
- Не режем скиллы до инвентаризации и измерения пользы.

## Целевая архитектура

```text
Windows / MacBook / Phone
        |
        | SSH / VS Code Remote / Telegram
        v
Server 1: ai-control-plane
  - Codex
  - OpenCode
  - Hermes Agent
  - n8n
  - MCP servers
  - Obsidian/Jarvis sync copy
  - optional Qdrant/pgvector
        |
        | SSH deploy user / Git / API
        v
Server 2: projects-runtime
  - project repos
  - Docker Compose apps
  - databases
  - reverse proxy
  - deploy scripts
```

## Рекомендуемые серверы

Старт:

- Server 1: Ubuntu 24.04 LTS, 2 vCPU, 8 GB RAM, 80+ GB SSD.
- Server 2: Ubuntu 24.04 LTS, 2 vCPU, 4-8 GB RAM, 80+ GB SSD.

Если n8n, browser automation, Qdrant и несколько агентов будут активно работать одновременно, Server 1 лучше сразу брать 16 GB RAM.

## Фаза 0. Оформить фреймворк как repo

1. Инициализировать git в `W:\framework`.
2. Добавить `.gitignore` для секретов, auth-файлов, логов, SQLite, временных файлов, архивов.
3. Разложить документацию:
   - `docs/notes/` — заметки исследования;
   - `docs/plans/` — планы;
   - `docs/runbooks/` — пошаговые инструкции;
   - `templates/` — шаблоны `AGENTS.md`, `CLAUDE.md`, `opencode.json`, `config.toml`;
   - `inventory/` — список локальных инструментов, MCP, скиллов, проектов.
4. Сделать первый приватный GitHub repo только после проверки секретов.

Проверка:

- `git status` не показывает секретные файлы.
- `rg -n "sk-|api[_-]?key|token|auth|password|secret" .` не находит реальных ключей.

## Фаза 1. Инвентаризация локальной среды

Собрать без копирования секретов:

- версии `node`, `npm`, `python`, `uv`, `git`, `codex`, `opencode`;
- текущие MCP из `.codex/config.toml` и OpenCode config;
- список скиллов: active/system/plugin/custom/archive-кандидаты;
- список проектов и где они живут;
- список env-переменных по именам, без значений;
- список локальных баз/логов, которые надо бэкапить или исключить.

Результаты:

- `inventory/local-environment.md`
- `inventory/skills.md`
- `inventory/projects.md`
- `inventory/secrets-map.md`

## Фаза 2. Безопасная серверная база

На обоих VPS:

1. Создать обычного пользователя, запретить root login по SSH.
2. Настроить SSH keys, `~/.ssh/config`, отдельные ключи под каждый сервер.
3. Включить firewall: открыть только SSH, HTTP/HTTPS где нужно.
4. Поставить базовый стек: `git`, `curl`, `ripgrep`, `jq`, `tmux`, `node`, `python`, `uv`, `docker`, `docker compose`.
5. Включить unattended security updates.
6. Настроить Tailscale или WireGuard для приватного доступа к n8n, Qdrant, NotebookLM bridge, служебным UI.

Решение:

- Публично наружу: только сайты/приложения через reverse proxy.
- Служебное: только через VPN/SSH tunnel.

## Фаза 3. Перенос агентной среды на Server 1

Порядок:

1. Codex CLI.
2. OpenCode.
3. MCP базового набора: filesystem, memory, context7, playwright/chrome-devtools по необходимости.
4. Hermes Agent.
5. n8n.
6. GitHub auth.

Секреты:

- хранить в `.env` или secret manager на сервере;
- права `chmod 600`;
- не класть в Obsidian raw, GitHub, логи сессий;
- для Telegram bot token, OpenRouter, DeepSeek, Replicate, GitHub PAT сделать отдельную таблицу в `inventory/secrets-map.md` без значений.

Hermes:

- запускать как отдельный пользователь/сервис;
- Telegram gateway включать только с allowlist твоего user ID;
- approval mode оставить manual;
- destructive tools и SSH включать только осознанно;
- для coding worker сначала использовать встроенные навыки/простую делегацию, не ставить плагины поверх плагинов до проверки базы.

## Фаза 4. Проектный сервер

На Server 2:

1. Создать пользователя `deploy`.
2. Разделить проекты по `/srv/projects/<project>`.
3. Каждый проект держать в git.
4. Для приложений использовать Docker Compose.
5. Настроить reverse proxy: Caddy или Traefik.
6. Агентный сервер ходит на project server только по SSH deploy-key.

Решение:

- агенты не работают напрямую от root;
- каждый проект имеет backup/runbook;
- destructive commands требуют подтверждения.

## Фаза 5. Obsidian и заметки

Базовая структура Jarvis:

```text
Jarvis/
  00_system/
    AGENT.md
    INDEX.md
    MEMORY_POLICY.md
  01_raw/
    web/
    youtube/
    telegram/
    chats/
    docs/
  03_wiki/
    concepts/
    people/
    projects/
    tools/
    playbooks/
  04_outputs/
    posts/
    briefs/
    plans/
    research/
  05_logs/
    decisions.md
    changelog.md
    contradictions.md
```

Sync:

- Obsidian Sync подходит как основной sync для личных заметок: есть end-to-end encryption, version history, selective sync и file recovery.
- Git подходит для технической документации и framework repo.
- Для agent-readable копии vault на Server 1 лучше использовать Obsidian Sync headless или отдельную реплику через Git/rsync, не прямую запись в единственный живой vault без бэкапа.

Правило:

- `03_wiki` — очищенные знания;
- `01_raw` — сырье;
- `05_logs/decisions.md` — решения;
- большие архивы не засовывать в индекс, для них нужен RAG/NotebookLM.

## Фаза 6. RAG-память

MVP:

- оставить Obsidian + filesystem search + memory MCP;
- писать решения и handoff в Markdown;
- не строить Qdrant, пока нет большого корпуса.

Следующий уровень:

- Qdrant или pgvector на Server 1;
- ingestion `.md` из Obsidian и больших архивов;
- parent-child chunking;
- metadata: `type`, `project`, `source`, `updated`, `status`;
- отдельный retriever tool для Codex/OpenCode/Hermes.

Не начинать с Pinecone, если цель — контроль и приватность. Qdrant локально в Docker проще и логичнее.

## Фаза 7. NotebookLM

NotebookLM использовать как дополнительный исследовательский слой.

Безопасный старт:

- отдельный Google-аккаунт без почты, платежей и личного Drive;
- переносить туда только выбранные notebooks/источники;
- не хранить там API-ключи, личные документы и клиентские приватные данные;
- bridge/MCP только локально или через VPN/SSH tunnel;
- не публиковать HTTP bridge в интернет.

Причина:

- официальная NotebookLM Enterprise API существует, но это Google Cloud/Enterprise/Pre-GA история;
- consumer NotebookLM bridges в основном используют неофициальные API/cookies/browser automation.

## Фаза 8. Скиллы и контекст

Проблема:

- сейчас найдено 78 `SKILL.md`, и список описаний грузится в контекст.

План:

1. Сделать inventory всех скиллов.
2. Разделить:
   - core engineering;
   - n8n;
   - writing/marketing;
   - infra;
   - archive/rare.
3. Проверить дубли и слишком широкие descriptions.
4. Не удалять сразу. Сначала сделать отчет, какие skills реально нужны в повседневном coding loop.
5. После этого можно оптимизировать descriptions и архивировать лишнее.

## Фаза 9. Бэкапы

Минимум:

- GitHub private repo для framework/docs/templates.
- Obsidian Sync для vault.
- Ежедневный server backup: `/home/<user>/.codex`, `~/.hermes`, n8n data, selected configs, без node_modules/cache.
- Weekly encrypted archive в отдельное место.

Нужны restore drills:

- восстановить repo на чистую машину;
- поднять Codex/OpenCode config без секретов;
- восстановить Obsidian vault;
- восстановить n8n workflows;
- проверить, что агент не потерял runbooks.

## Фаза 10. Приватный GitHub

Перед пушем:

1. Добавить `.gitignore`.
2. Прогнать поиск секретов.
3. Убрать/замаскировать примеры ключей.
4. Инициализировать repo.
5. Создать private repo.
6. Первый commit: docs, планы, шаблоны.

Не пушить:

- `.codex/auth.json`
- `.codex/sessions/`
- `.codex/logs*.sqlite`
- `.hermes/auth.json`
- `.hermes/config.yaml` с реальными ключами
- `.env`
- cookies/browser profiles
- Obsidian personal/private notes
- n8n credentials database

## Первый рабочий порядок действий

1. Довести этот framework repo до безопасного состояния.
2. Сделать inventory локальной среды.
3. Создать приватный GitHub repo.
4. Выбрать VPS и поставить базовую Ubuntu.
5. Настроить SSH/VPN/firewall/users.
6. Перенести Codex/OpenCode без секретов в git.
7. Секреты завести вручную на сервере.
8. Поставить Hermes и Telegram gateway.
9. Поднять n8n.
10. Настроить Obsidian sync/replica.
11. Только после этого думать про NotebookLM bridge и Qdrant.

## Открытые вопросы

- Какой VPS-провайдер выбираем и какие тарифы доступны?
- Где сейчас лежат реальные проекты, которые надо переносить первыми?
- Нужен ли Tailscale или предпочтительнее чистый SSH tunnel/WireGuard?
- Obsidian vault переносим целиком или создаем отдельный `Jarvis` vault для агентной работы?
- n8n сейчас локальный или уже где-то развернут?

