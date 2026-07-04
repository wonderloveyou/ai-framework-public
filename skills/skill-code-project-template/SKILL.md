---
name: skill-code-project-template
description: Шаблон CLAUDE.md для кодовых проектов с caveman mode, CE-скиллами и правилами для разработки. Копировать в корень нового кодового проекта.
---

# Code Project Template

## Goal
Быстро запустить кодовый проект с caveman-режимом, CE-скиллами и правилами для разработки. Копировать в `CLAUDE.md` нового проекта.

## Template
```md
# CLAUDE.md

@AGENTS.md

## Поведение
- Говори коротко и по делу (caveman mode). Без воды, без предисловий, без «конечно!»
- Используй фрагменты, опускай очевидное. Техническая точность — 100%, слов — минимум
- Не пиши «I'd be happy to help», «Great question!», «Let me explain»
- Пиши: «Bug in auth middleware. Token expiry uses `<` not `<=`. Fix:»

## Workflow
1. Plan first — inspect files, restate task in 2-5 bullets
2. Code — write tests first (TDD), then implementation
3. Verify — run lint/typecheck/test
4. Report — what changed + status

## Команды
- Dev: `[тут команда]`
- Lint: `[тут команда]`
- Test: `[тут команда]`

## CE-скиллы
- `/ce-plan` — спланировать фичу
- `/ce-work` — выполнить план
- `/ce-review` — ревью кода
- `/ce-compound` — задокументировать уроки

## Hard rules
- Не выдумывай факты, цены, имена, сроки
- Не меняй файлы не по задаче
- Если данных нет — `TODO: verify`, не выдумывай
```

## Usage
1. Copy template into project root as `CLAUDE.md`
2. Replace `[тут команда]` with actual dev commands
3. Create `AGENTS.md` if needed (or just use CLAUDE.md)
4. Start coding — caveman auto, CE commands available
