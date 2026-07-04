# Sub-Agent Pipeline Patterns

Паттерны для многошаговых AI-задач с передачей артефактов между агентами.

## Pipeline

```
brief-expander → researcher → ia-architect → design-generator → prototype-builder
                    ↕ (параллельно)
              test-companion
```

## Файлы

| Файл | Назначение | Где используется |
|------|-----------|-----------------|
| `brief-expander.md` | Уточнение ТЗ раундами вопросов | Hermes skill для обсуждения идей в Telegram |
| `researcher.md` | Продуктовый ресёрч — карта вопросов → поиск → синтез | **Phase 6 autoresearch loop** |
| `ia-architect.md` | Информационная архитектура (Figma) | Reference (требует Figma MCP) |
| `design-generator.md` | Макеты в Figma из IA + ДС | Reference (требует Figma MCP) |
| `prototype-builder.md` | Кликабельный прототип в Figma | Reference (требует Figma MCP) |
| `test-companion.md` | Параллельный агент: тест-стенд + аналитика | Паттерн для фоновых задач/мониторинга |

## Ключевые паттерны

### 1. Артефактный пайплайн
Агенты общаются не напрямую, а через файлы: `artifacts/brief.md → research.md → ia.md → screens.md → prototype.md`. Каждый следующий агент читает выход предыдущего.

### 2. Структура sub-agent промпта
```
- Логика работы (пошагово)
- Структура выходного файла
- Граничные ситуации (edge cases)
- Что не делать (anti-patterns)
```

### 3. Раундовое уточнение (brief-expander)
Приоритет вопросов: аудитория → ограничения → цели → fail criteria. После каждого раунда — резюме. Без преамбул, сразу вопросы.

### 4. Параллельный агент (test-companion)
Стартует рано, работает в фоне, читает артефакты по мере появления. Не блокирует основной пайплайн.

### 5. Методология ресёрча (researcher)
Карта вопросов → поиск → персоны/JTBD → сценарии → таблица фич с приоритетами. Цифры со ссылками, оценки — помечать.

## Применение в AI Framework

- **Phase 6 (Autoresearch):** адаптировать `researcher.md` для еженедельного cron-ресёрча в Obsidian
- **Phase 8.2 (Multi-agent):** шаблон для Hermes sub-agent промптов
- **Hermes skill:** `brief-expander.md` как навык для обсуждения идей через Telegram
