# RAG в продакшене: Полное руководство

> **Синтез 4 независимых ресёрчей, дополненный лучшими практиками из Parallel.AI Research и RAG Factura.**
> **Актуальность:** Июнь 2026. Все цифры из первичных источников.
> **Целевая аудитория:** AI/ML инженеры, строящие production RAG-системы.

---

## Executive Summary

**Главный тезис:** Большинство RAG-систем ломаются не на LLM. Они ломаются на плохом парсинге, плохом чанкинге, плохом retrieval и отсутствии оценки качества. В 80% случаев прирост качества приходит из retrieval-контура, а не из замены модели.

**Ключевые цифры и пороги продакшена:**

| Параметр | Значение | Обоснование |
|---|---|---|
| Размер чанка (старт) | 300–500 токенов, 10–20% overlap | Recursive character baseline |
| Parent-Child: child/parent | 100–200 / 500–1500 токенов | Точный поиск + полный контекст |
| Top-k retrieval | 5–20 (первичный), 3–5 (в LLM) | После cross-encoder reranking |
| Cosine similarity threshold | 0.7–0.8 | Отсечение шума |
| RRF k-параметр | 60 | Стандарт для слияния sparse + dense |
| Cross-encoder top-N | 50 кандидатов → top-5 в LLM | Баланс latency/cost/quality |
| Прирост nDCG@10 от гибридного поиска + reranker | +27% над dense-only | Бенчмарк |
| Снижение LLM calls через semantic caching | 30–50% | Similarity > 0.95 |
| Бинарная квантизация: сжатие / ускорение | 32x / 5–10x | 90–95% качества сохранено |

**Архитектурная трилемма:** RAG, Fine-tuning и Long Context Window решают разные задачи — RAG оптимизирует свежесть знаний при низкой стоимости запроса, Fine-tuning — поведение и формат модели, Long Context — глубокое рассуждение над целым корпусом. Выбор определяется доминирующим ограничением, а не модой.

**Best practice 2026:** RAG для фактов + Fine-tuning для стиля/формата + Long Context для небольших статичных блоков. Гибрид всегда выигрывает.

---

## 1. Архитектурный выбор: RAG vs Fine-tuning vs Long Context Window

### 1.1 Полная матрица сравнения

Это не конкурирующие технологии. Это разные уровни памяти.

| Критерий | RAG | Fine-tuning (LoRA/QLoRA) | Long Context Window |
|---|---|---|---|
| **Что хранит** | Знания вне модели (векторная БД) | Знания внутри весов | Документы прямо в промпте |
| **Setup Cost** | $0.10–2 / 1K запросов | $5–500+ трейнинг, дешёвый инференс | $0 (setup), $0.50–15+ за запрос |
| **Latency** | 200–800ms (retrieval + gen) | 50–200ms (без retrieval) | 2–30s (обработка миллионов токенов) |
| **Стоимость запроса (2M токенов)** | ~0.0024 EUR (стабильно) | ~0.0008 EUR (низкая) | ~0.60 EUR (без кэша) |
| **Accuracy** | 78–85% на Natural Questions | 82–90% на доменных задачах | 85–92% на RULER/NIAH |
| **Data Freshness** | Отличная (обновляй индекс) | Плохая (ретрейнинг) | Отличная (добавь в prompt) |
| **Privacy** | Данные в твоей векторной БД | Знание в весах (отлично) | Данные идут в API при каждом вызове |
| **Explainability / Цитируемость** | Высокая (источники) | Низкая (знание имплицитно) | Средняя |
| **Failure Mode** | Неверный контекст → уверенный неправильный ответ | Overfitting, catastrophic forgetting | Lost-in-the-Middle на очень длинных контекстах |
| **Update Cycle** | Минуты (переиндексация) | Часы–дни | Мгновенно |
| **Масштабируемость корпуса** | Миллионы документов | Ограничена объёмом данных для обучения | Ограничена окном (~200K токенов) |

### 1.2 Правило кроссовера и решающее дерево

**RAG — дефолт для:**
- Данных, меняющихся быстрее, чем можно дообучить (новости, продуктовая документация, тикеты)
- Баз знаний объёмом >10 МБ (~2M токенов)
- Per-tenant / per-customer данных (нельзя дообучать под каждого)
- Сценариев с обязательными цитатами и source attribution

**Fine-tuning — выбирай для:**
- Обучения модели **поведению**, а не фактам: стиль, формат, tone of voice, структурированный вывод
- Стабильных доменных задач с latency-sensitive требованиями
- On-prem деплоя с жёсткими требованиями к приватности

**Long Context Window — когда:**
- Корпус <1M токенов и меняется редко
- Запрос требует синтеза через весь корпус целиком (а не по отдельным чанкам)
- Есть возможность кэшировать prefix через много запросов (cache-hit rate 80–90%)
- Нужен быстрый MVP без индексации

**Правило кроссовера:** Для данных <10 МБ Long Context экономически целесообразен только при высоком cache-hit rate. Для любого объёма >10 МБ RAG — единственная архитектура с предсказуемой кривой затрат.

### 1.3 Гибридные архитектуры (реальный продакшен)

Изолированное применение одного подхода — редкость. Стандартные гибриды:

- **RAG + Fine-tuning:** Поисковый слой = динамические данные, дообученная модель = стиль и формат. Стандарт для клиентских AI-ассистентов.
- **RAG + Long Context:** Векторный ретривер фильтрует гигантскую базу до широкого пула, затем LLM с широким окном делает глубокий синтез (юридический/медицинский анализ).
- **Small Model + Router:** Быстрая дешёвая модель классифицирует запрос → роутит к нужному бэкенду. Простые факты → RAG, форматирование → Fine-tuned модель, комплексный анализ → Frontier Model с широким контекстом. Снижение OPEX в 3–5 раз.

---

## 2. Эволюция RAG: от Naive до Hierarchical

### 2.1 Четыре поколения

| Архитектура | Пайплайн | Pre-retrieval | Retrieval | Post-retrieval | Обратная связь |
|---|---|---|---|---|---|
| **Naive RAG** (2023) | Фиксированный, линейный | Нет | Один dense retriever | Нет | Нет |
| **Advanced RAG** (2023–24) | Линейный с оптимизацией | Query Rewriting, HyDE | Гибридный поиск | Re-ranking, фильтрация | Ограниченная |
| **Modular RAG** (2024–25) | Гибкий, перекомпонуемый | Настраиваемый модуль | Адаптивный выбор retriever | Настраиваемый модуль | Полная (циклы, CRAG) |
| **Hierarchical RAG** (2025–26) | Каскадный, многоуровневый | Суммаризации как индекс | Каскад: summary → chunks | Контекст родительского уровня | Многоуровневая |

#### Naive RAG

```
Question → Embedding → Vector Search → Top-K → LLM → Answer
```

Работает. Но быстро упирается в потолок: пропуск нужных документов, шум, нет multi-hop reasoning, падает на сложных вопросах.

#### Advanced RAG

```
Question → [Rewrite] → Hybrid Search → Top-50 → Reranker → Top-5 → LLM → Answer
```

Добавляются: Hybrid Search, Re-ranking, Query Rewriting, Metadata Filtering. Это сегодня базовый продакшен.

#### Modular RAG

Вся система разбивается на независимые блоки: Retriever, Reranker, Router, Memory, Agent, Evaluator. Можно менять любой компонент. Сегодня почти весь enterprise идёт сюда.

Router диспатчит запросы: фактологические → векторный поиск, аналитические → SQL-модуль для агрегации, результат слияния → Fusion → Re-ranking → LLM.

#### Hierarchical RAG (HRAG)

Многоуровневое извлечение: сначала найти раздел, потом документ, потом кусок документа. Знание организуется в древовидную структуру с суммаризациями на каждом уровне.

**RAPTOR** (Sarthi et al., 2024, 650+ цитирований): рекурсивно встраивает, кластеризует и суммаризует чанки, строя дерево с различными уровнями абстракции. Позволяет обрабатывать запросы от конкретных фактов до глобальных тем.

### 2.2 Сравнительная таблица: когда что

| Архитектура | Лучше всего для | Latency Impact | Accuracy Gain | Complexity | Апгрейди когда... |
|---|---|---|---|---|---|
| Naive RAG | Простые single-doc lookups | Baseline (0.5–2s) | Baseline | Низкая | Только для старта |
| Advanced RAG | Большинство prod сценариев | +100–200ms | +10–20% | Средняя | Точность Naive недостаточна |
| Modular/Agentic RAG | Сложные multi-step задачи | +0.5–1s | +15–30% | Высокая | Нужны тулы и routing |
| GraphRAG | Реляционные/comparative запросы | +1–3s (graph traversal) | +20%+ on multi-hop | Высокая | Юзеры спрашивают "сравни X и Y" |

Ключевой вывод: эволюция идёт от простоты к адаптивности. Naive достаточен для прототипа, Modular + Hierarchical нужны для продакшена.

---

## 3. Оптимизация компонентов

### 3.1 Чанкинг: самый недооценённый компонент RAG

> **Инвестиции в правильный чанкинг дают больший ROI, чем любой другой компонент.** Выбор стратегии чанкинга влияет на все последующие слои.

#### Стратегии чанкинга — полный обзор

| Стратегия | Как работает | Размер | Лучший кейс | Стоимость |
|---|---|---|---|---|
| **Fixed-size** | Разрезание по фиксированному числу токенов с overlap | 200–1000 | Быстрый прототип | Низкая |
| **Recursive Character** | Разрезание по разделителям (параграфы, предложения) рекурсивно | 300–500 | Старт по умолчанию | Низкая |
| **Semantic** | Группировка предложений по косинусному сходству эмбеддингов | Динамический, max 1024 | Когезивные темы (контракты, research papers) | Средняя |
| **Parent-Child** | Child (100–200) для поиска, Parent (500–1500) для контекста | Двухуровневый | Продакшен RAG (стандарт) | Средняя |
| **Late Chunking** | Эмбеддинги на уровне токенов → чанкинг после пулинга | Динамический | Минимизация потери контекста | Высокая |
| **Structure-aware** | По заголовкам/секциям | 800–1500 | Технические доки с разделами | Низкая |
| **IdeaBlock (Blockify)** | Question-answer пары с governance schema | 1 QA-пара | Корпоративные базы знаний | Высокая |

#### Fixed-size Chunking (baseline)

Стартовая точка: **512 токенов с 50–100 токенов overlap**. Для продакшна: 500–800 токенов с 100–200 overlap. Воспроизводимо, но ломает смысл на границах.

#### Semantic Chunking

Разбивает на уровне предложений, сравнивает cosine similarity соседних предложений и кластеризует их по breakpoint threshold. Алгоритм:

1. Разбить текст на предложения
2. Векторизовать каждое предложение
3. Вычислить косинусное сходство между векторами соседних предложений:
   ```
   Similarity(A,B) = (A·B) / (∥A∥ × ∥B∥)
   ```
4. Установить порог (95-й перцентиль расстояний между всеми последовательными окнами)
5. Когда сходство падает ниже порога → граница чанка

Работает лучше всего для плотного прозаичного текста (контракты, research papers, медицинские документы). Требует hard upper bound (max 1024 токенов), чтобы чанки не разрастались.

#### Parent-Child Chunking (стандарт продакшена)

Решает фундаментальный конфликт: для точного поиска нужны маленькие чанки, для генерации — широкий контекст.

1. **Parent chunks** (500–1500 токенов): хранятся в document store, отдаются LLM
2. **Child chunks** (100–200 токенов): индексируются в vector store для точного поиска
3. **Retrieval:** query ищет по child → при матче возвращается parent с полным контекстом

Результат: высокая точность поиска (маленькие чанки) + богатый контекст (большие чанки). Storage overhead ~2x, но precision на enterprise документах окупает.

#### Late Chunking (Jina AI)

Ключевая идея: вместо нарезки перед эмбеддингом — **прогоняем весь документ через модель целиком**, получаем token-level embeddings, потом делаем mean pooling по чанкам.

- Каждый чанк «помнит» контекст всего документа (местоимения связаны с существительными)
- **10–15% лучше retrieval** на context-dependent корпусах
- **Требует моделей с token-level embeddings:** Jina Embeddings v3/v4, BGE-M3, Perplexity pplx-embed-context-v1
- **OpenAI API не поддерживает** (нет доступа к token-level outputs)

#### Blockify / IdeaBlock

Препроцессинговый слой между парсером и vector store: конвертирует chunks в **question-answer пары** с typed governance schema (clearance level, version state, source).

Цифры из бенчмарка (17 документов, 298 страниц):
- Cosine distance до best-matching block: **0.1585 для IdeaBlocks vs 0.3624 для naive chunks** → 2.29x лучше
- После semantic deduplication: 2,042 блока → 1,200 canonical, сжатие корпуса в **40x**
- Distilled dataset превзошёл undistilled на **13.55%** по векторной точности

Ключевой инсайт: 15 near-duplicate копий одного параграфа создают 15 конкурирующих векторов в одной области embedding space. Retrieval распределяет probability mass между ними, снижая сигнал. Схлопывание в один canonical block обостряет сигнал.

#### Рекомендации по чанкингу для разных типов контента

| Тип контента | Стратегия | Размер чанка |
|---|---|---|
| General Q&A, мануалы, legal | Parent-Child | Parent 500–1000 / Child 100–200 |
| Плотная проза (эссе, research papers) | Semantic Chunking | Max 1024 токенов |
| Технические доки с разделами | Structure-aware (по заголовкам) | 800–1500 токенов |
| Юридические/медицинские (точность — приоритет) | Semantic + Late Chunking | 300–500 токенов |
| Код | По функциям/классам | Структурные единицы |
| Разговоры/QA | Один dialog turn | Переменный |

**Стартовая стратегия:** Recursive Character Chunking (300–500 токенов, 10–20% overlap) → миграция на Parent-Child при росте объёмов.

### 3.2 Эмбеддинг-модели: топ 2026 года

Выбор эмбеддинг-модели определяет качество retrieval. MTEB (Massive Text Embedding Benchmark) — стандартный бенчмарк.

| Ранг | Модель | MTEB Score | Dims | Max Tokens | Цена | Ключевая особенность |
|---|---|---|---|---|---|---|
| 1 | **Qwen3-Embedding-8B** | 75.23 | 4096 | 32,768 | Self-host (Apache 2.0) | Лидер 2026 среди open-weight, 100+ языков |
| 2 | **NV-Embed-v2** (NVIDIA) | 72.31 | 4096 | 32,768 | Self-host (open-weight) | Сильный конкурент |
| 3 | **BGE-en-ICL** (BAAI) | 71.24 | 4096 | 32,768 | Self-host | In-context learning support |
| 4 | **GTE-Qwen2-7B-instruct** | 70.24 | 3584 | 32,768 | Self-host | Инструктивная модель |
| 5 | **Gemini Embedding 001** | 68.32 | 3072 | 8,192 | API $0.004/1K chars | Google, хорош для GCP |
| 6 | **Voyage-3.1-large** | 67.40 | 2048 | 32,768 | API $0.05/1M | Огромное окно, код и тех-доки |
| 7 | **Jina Embeddings v4** | 66.81 | 1024 | 32,768 | Free tier + paid | Late chunking, LoRA-адаптеры |
| 8 | **Cohere Embed v4** | 65.20 | 1024 | 512 | API $0.12/1M | Мультиязычная (>100 языков), binary quantization |
| 9 | **text-embedding-3-large** | 64.60 | 3072 | 8,191 | API $0.13/1M | Matryoshka (усечение до 256 dims) |
| 10 | **BGE-M3** (BAAI) | 63.00 | 1024 | 8,192 | Self-host | Dense + Sparse + ColBERT в одной модели |

#### Рекомендации по выбору

| Кейс | Рекомендация | Почему |
|---|---|---|
| Multilingual продакшен на своём GPU | Qwen3-Embedding-8B | Лидер MTEB, Apache 2.0, 32K контекст |
| Русский язык | Qwen3-Embedding-8B или BGE-M3 | Лучшая мультиязычная поддержка |
| Late chunking | Jina Embeddings v3/v4 или Perplexity pplx-embed-context-v1 | Только они дают token-level embeddings |
| Enterprise, самохостинг | BGE-M3 | Dense + Sparse + ColBERT, 8K контекст |
| Экономия памяти (Matryoshka) | text-embedding-3-large | Усечение с 3072 до 256 без деградации |
| Маленький сервер / edge | nomic-embed-text-v1.5 | 137M параметров, CPU-инференс |

> **Важно:** MTEB-рейтинги изменчивы. Проводи A/B тестирование на своих данных — общий балл маскирует сильные/слабые стороны на конкретных задачах.

#### Нормализация эмбеддингов (обязательно)

```python
embeddings = embeddings / np.linalg.norm(embeddings, axis=1, keepdims=True)
```

#### Бинарная квантизация

Техника, используемая Perplexity, Azure, HubSpot: float32 → 1 bit.
- **32x сжатие памяти**
- **5–10x ускорение поиска**
- **90–95% качества сохранено**
- 36M+ векторов → запрос за <30ms

```python
# float32 → binary
binary_embeds = np.where(embeds_array > 0, 1, 0).astype(np.uint8)
# Поиск по Hamming distance
```

**Важно:** Binary quantization делает векторный слой сверхэффективным, но это только одна часть retrieval-инфраструктуры. В реальном продакшене retrieval — это не просто векторный lookup; он включает auth, sync, query routing, permissions и reranking.

### 3.3 Векторные базы данных

| БД | Тип | Open Source | Гибридный поиск | P95 Latency (1M vecs) | Throughput QPS | Лучший кейс |
|---|---|---|---|---|---|---|
| **FAISS** | Библиотека (C++) | Да | Нет | 10–20ms | 20,000–50,000 | MVP, локальные агенты, pet-project |
| **Qdrant** | Standalone/Cloud (Rust) | Да | Да (нативно) | 30–40ms | 8,000–15,000 | Продакшен с контролем, фильтрация |
| **Pinecone** | Managed Cloud | Нет | Платные тиры | 40–50ms | 5,000–10,000 | Zero-ops, быстрый старт |
| **Weaviate** | Standalone/Cloud | Да | Да (нативно) | 50–70ms | 3,000–8,000 | GraphQL API, гибрид из коробки |
| **Milvus** | Standalone/Cloud | Да | Да | Зависит от конфига | Очень высокий | Billion-scale enterprise |
| **pgvector** | Расширение PostgreSQL | Да | Да | Зависит от PG | Средний | Проекты, уже использующие PG |
| **Chroma** | Embedded/Cloud | Да | Частично | Средняя | Низкий | Прототип, Jupyter-ноутбуки |

#### Рекомендации по масштабу

| Масштаб | Выбор | Обоснование |
|---|---|---|
| Прототип / MVP | Chroma или FAISS | Мгновенный старт, нет инфраструктуры |
| Стартап / Small-medium production | Qdrant | Лучший баланс performance/features/self-hosted |
| Корпорация без DevOps | Pinecone | Полностью управляемый, auto-scaling |
| Graph-heavy / Knowledge graph | Weaviate | Гибридный поиск + GraphQL нативно |
| Billion-scale enterprise | Milvus | Распределённая архитектура, нет конкурентов |
| Всё в PostgreSQL | pgvector | ACID-транзакции, единая БД |

#### Квантизация для оптимизации индекса

- **SQ8 (Scalar Quantization):** ~4x сжатие, потеря recall 1–2%
- **Binary:** ~32x сжатие, нужен oversampling + rerank pass для восстановления recall

**Ключевой инсайт Qdrant:** применяет мета-фильтры во время обхода HNSW-графа (а не после векторного поиска, как другие БД), что обеспечивает плоскую задержку в 30–40ms даже при сложных запросах.

### 3.4 Гибридный поиск + Re-ranking (стандарт продакшена)

#### Почему dense-only недостаточно

Bi-encoders кодируют query и документы независимо — быстро, но теряют query-doc interaction. BM25 выигрывает на точных термах, кодах, аббревиатурах, числовых запросах. Комбинация даёт +1–9% recall над pure vector search.

#### Production Pipeline (2026)

```
1. Sparse retrieval (BM25) — keyword matching
2. Dense retrieval (bi-encoder) — semantic similarity
3. Reciprocal Rank Fusion (RRF) — слияние с k=60
4. Cross-encoder reranking — joint scoring top-50 кандидатов
5. Top-5 → LLM
```

#### Reciprocal Rank Fusion (RRF)

Не требует нормализации баллов и сложной калибровки весов:
```
Score(d) = Σ 1 / (k + rank(r,d))
```
Где k = 60 (константа сглаживания), r — поисковая система (Dense или Sparse), rank(r,d) — позиция документа d в выдаче системы r.

#### Результаты из бенчмарка

| Pipeline | nDCG@10 | Precision | Recall | Latency |
|---|---|---|---|---|
| Dense only | 0.56 | 0.50 | 0.84 | 94ms |
| Hybrid (BM25 + Dense, RRF) | 0.62 | 0.53 | 0.85 | 184ms |
| **Hybrid + Cross-Encoder** | **0.71** | **0.63** | **0.85** | **312ms** |

**+27% nDCG@10** над dense-only за прибавку 218ms latency. Context Precision улучшается с 0.61 до 0.79.

#### Cross-Encoder Re-ranking

Cross-encoders конкатенируют `[query, doc]` и обрабатывают совместно — улавливают «отвечает ли этот документ на именно этот вопрос». 10–100x медленнее bi-encoder, но применяется только на small candidate set (top-50).

**Популярные модели:**
- BGE-Reranker (BAAI)
- Jina Reranker
- Cohere Rerank

**Практика:** запускай cross-encoder на топ-50 из RRF, отдавай топ-3/5 в LLM. Часто даёт больше прироста, чем замена LLM.

---

## 4. Продвинутые агентские паттерны

Агентский RAG передаёт управление конвейером поиска ИИ-агенту. Вместо статического retrieve-then-generate агент самостоятельно оценивает ситуацию, выбирает инструменты, анализирует промежуточные результаты и итеративно продолжает поиск.

### 4.1 Query Rewriting

LLM переписывает query перед попаданием в vector DB: расширяет аббревиатуры, уточняет формулировки, исправляет typos, генерирует синонимы.

**Три техники:**
- **Декомпозиция:** сложный запрос → подзапросы. «Почему конвейер падает после обновления?» → «Какие ошибки при обновлении?» + «Какие изменения в API вызывают падение?»
- **Multi-query expansion:** 3–5 переформулировок → поиск по каждой → объединение результатов
- **Step-back prompting:** от конкретного к общему. «Температура плавления Ti-6Al-4V при 2 атм?» → «Какие свойства сплава Ti-6Al-4V при различных условиях?»

**Прирост:** +5–10% recall. Latency overhead: +50–100ms. Рекомендация: минимум декомпозиция для многосоставных запросов.

### 4.2 HyDE (Hypothetical Document Embeddings)

Контринтуитивная техника: вместо эмбеддинга вопроса — LLM генерирует гипотетический ответ, и поиск ведётся по его эмбеддингу.

1. Пользователь → запрос Q
2. LLM генерирует гипотетический ответ H (может содержать ошибки!)
3. H эмбеддится → v_H
4. Поиск по v_H в векторной БД
5. Найденные чанки → LLM → финальный ответ

**Почему работает:** структура гипотетического ответа ближе к структуре реальных документов в базе, чем короткий запрос. Фактическая точность гипотетического документа не требуется — HyDE работает за счёт формы и словаря.

- BEIR benchmark: **+6 pp average recall** над standard dense retrieval
- Лучше всего для: factoid questions с прямыми ответами
- Хуже для: personal, ambiguous или highly context-dependent queries

**Прирост:** +6 pp recall avg. Latency overhead: +100–200ms. Сложность: низкая.

### 4.3 Corrective RAG (CRAG)

CRAG добавляет lightweight evaluator между retrieval и generation:

1. Выполнить retrieval
2. Оценить релевантность документов (Correct / Ambiguous / Incorrect)
3. Correct → генерация; Ambiguous → доуточнение; Incorrect → веб-поиск

На PubHealth: **+36.6% accuracy** над стандартным RAG.

**Tiny-Critic вариант:** валидатор = малая языковая модель (SLM) с LoRA. Выполняет функцию детерминированного шлюза с ультранизкой задержкой, без вызовов «тяжёлых» LLM.

**Прирост:** +19–37%. Latency overhead: +0.5–1s. Сложность: средняя.

### 4.4 Self-RAG

Self-RAG **дообучает** LLM генерировать специальные reflection tokens:

- **[Retrieve]:** нужен ли retrieval вообще?
- **[IsRel]:** релевантен ли извлечённый документ?
- **[IsSup]:** поддержан ли ответ контекстом?
- **[IsUse]:** полезен ли ответ?

На этапе инференса — segment-wise beam search, который максимизирует полезность каждого сегмента с учётом токенов рефлексии.

**Кейс:** простые запросы («Какой ваш телефон?») → без retrieval (экономия ~200ms). Сложные («Как интегрировать API v3?») → полный цикл retrieval + верификация. Снижение средней latency на ~40%.

**Прирост:** +10–15% на fact verification. Latency overhead: +0.5–1s. Сложность: высокая (fine-tune). Применяй когда: ошибочные ответы несут юридические/медицинские риски.

### 4.5 GraphRAG (Microsoft Research)

Автоматически строит knowledge graph из корпуса:

1. LLM извлекает entities и relationships из каждого чанка
2. Алгоритм Leiden строит community hierarchy
3. Для каждого сообщества генерируется краткое резюме (Community Report)
4. При запросе — два режима:
   - **Local Search:** векторный поиск по узлам графа + fan-out на соседние сущности
   - **Global Search:** Map-Reduce поверх всех Community Reports
   - **DRIFT Search:** расширенный локальный поиск с контекстом графового сообщества

**+20%+ on multi-hop queries.** Лучше всего для глобальных вопросов («Какие основные темы в корпусе?», «Как связаны X и Y?»). Значительно дороже при индексации (LLM calls для построения графа). Не для простых factoid Q&A.

CocoIndex — open-source инкрементальный ETL движок для построения knowledge graphs, работает через MCP с Claude Code, Cursor и другими агентами.

**Прирост:** +20%+ multi-hop. Latency overhead: +1–3s. Сложность: высокая.

### 4.6 Сравнительная таблица агентских паттернов

| Паттерн | Когда применять | Latency | Accuracy Gain | Complexity | Ключевой риск |
|---|---|---|---|---|---|
| Query Rewriting | Всегда как baseline | +50–100ms | +5–10% | Низкая | Переформулирование может исказить intent |
| HyDE | Vague queries, терминологический mismatch | +100–200ms | +6pp recall avg | Низкая | Неверный гипотетический ответ уводит поиск |
| CRAG | Customer-facing agents, noisy corpora | +0.5–1s | +19–37% | Средняя | Увеличивает latency |
| Self-RAG | High-stakes (medical/legal) | +0.5–1s | +10–15% | Высокая | Требует fine-tuning |
| GraphRAG | Relational/comparative, multi-hop | +1–3s | +20%+ multi-hop | Высокая | Неэффективен для простых Q&A |

---

## 5. Инструменты и оценка качества

### 5.1 LlamaIndex vs LangChain/LangGraph

| Категория | LlamaIndex | LangChain / LangGraph |
|---|---|---|
| **Primary Strength** | Ingestion, indexing, retrieval, query engines | App composition, orchestration, agents, workflow |
| **RAG Support** | Excellent (purpose-built): parent-child, recursive, hierarchical | Good (via retrievers & chains) |
| **Document Parsing** | LlamaParse — нативный для complex PDFs, таблиц, иерархии | Требует внешний парсер |
| **Agentic Workflows** | Event-driven `@step` functions с Context API | LangGraph — stateful graphs с cyclic flows и human-in-the-loop |
| **Observability** | OpenTelemetry integration | LangSmith (core часть экосистемы) |
| **Best Fit** | Platform/data teams, retrieval layers | Product teams, complex LLM workflows |

**LangGraph — ключевые возможности:**
- **Stateful graphs:** каждый узел имеет доступ к общему состоянию
- **Checkpointing:** автоматическое сохранение состояния
- **Cyclic flows:** агенты возвращаются к предыдущим шагам (retry retrieval)
- **Human-in-the-loop:** приостановка для человеческой проверки

**Вывод:**
- **Чистый RAG** → LlamaIndex (лучшая retrieval архитектура, зрелые RAG модули)
- **Агентские сценарии** с branching/looping/approval gates → LangGraph
- **Можно комбинировать:** LlamaIndex как retrieval layer + LangGraph как оркестратор

### 5.2 RAGAS: стандарт оценки качества

RAGAS использует парадигму LLM-as-a-judge для изоляции ошибок ретривера от ошибок генератора.

#### Метрики генератора (LLM)

**Faithfulness (Фактологичность)** — детектор галлюцинаций:

1. LLM-судья извлекает все атомарные утверждения из ответа
2. Каждое утверждение проверяется: следует ли оно из извлечённого контекста?

```
Faithfulness = Утверждения, подтверждённые контекстом / Общее количество утверждений
```

Модель штрафуется за любые неподтверждённые утверждения, даже если они правдивы в реальном мире (нарушение grounding).

**Answer Relevancy (Релевантность ответа):**

1. На основе ответа генерируются гипотетические вопросы
2. Вычисляется косинусное сходство между эмбеддингами сгенерированных вопросов и оригинальным вопросом

Штрафует модель за «воду» и отклонение от темы.

#### Метрики ретривера

**Context Recall (Полнота контекста):** эталонный ответ разбивается на утверждения. Ретривер оценивается по тому, присутствует ли каждое утверждение в найденных чанках. Верхняя граница корректности ответа — нельзя восстановить факты, никогда не попавшие в контекст.

**Context Precision (Точность контекста):**

```
Context Precision@K = Σ(k=1→K) Precision@k × v_k / Общее число релевантных элементов
```
Где v_k ∈ {0,1} — бинарный индикатор релевантности на ранге k.

#### Production Target Thresholds

| Метрика | Danger Zone | Acceptable | Good | Excellent |
|---|---|---|---|---|
| **Faithfulness** | <0.5 | 0.5–0.7 | 0.7–0.9 | >0.9 |
| **Answer Relevancy** | <0.6 | 0.6–0.75 | 0.75–0.9 | >0.9 |
| **Context Precision** | <0.5 | 0.5–0.7 | 0.7–0.85 | >0.85 |
| **Context Recall** | <0.5 | 0.5–0.7 | 0.7–0.85 | >0.85 |

#### Диагностика по метрикам

- Низкий **Faithfulness** при высоком **Context Precision** → проблема в LLM/prompt, укрепляй grounding
- Низкий **Context Precision** → проблема в retrieval стратегии или чанкинге
- Низкий **Context Recall** → RAG пропускает нужные документы

> **Интегрируй RAGAS в CI/CD:** падение context relevancy даже на 2% должно триггерить rollback.

---

## 6. Продакшен-чеклист (10 этапов)

### Этап 0: Предпроектная подготовка
- [ ] Data map: все источники (docs, tickets, product DB, wikis) и их владельцы
- [ ] Access model: tenants, roles, redaction rules согласованы с security/compliance
- [ ] Success metrics: relevance, citation accuracy, latency targets, SLAs
- [ ] Budget guardrails: token/embedding cost ceilings, fallbacks, kill-switches

### Этап 1: Ingestion & Normalization
- [ ] Source inventory: PDFs, HTML, databases, APIs, code задокументированы
- [ ] Normalization pipeline: cleaning, parsing, metadata enrichment, classification
- [ ] PII/PHI detection и redaction до эмбеддинга
- [ ] Versioning strategy (content hashes, etags) для выбытия stale docs
- [ ] **PDF стратегия:** 1 документ + 1–2 вопроса → парсер + full context. Много документов / много вопросов → агентский RAG (в 3x дешевле на запрос)

### Этап 2: Chunking & Embeddings
- [ ] Content-specific chunking strategy задокументирована
- [ ] Metadata schema: tenant, ACL, tags, timestamps, lineage
- [ ] Старт: 300–500 токенов + 10–20% overlap для baseline
- [ ] **Нормализуй эмбеддинги** перед хранением: `embeddings / norm`
- [ ] Similarity thresholds настроены по offline evals
- [ ] Poisoning safeguards: content hashing, anomaly detection

### Этап 3: Retrieval & Reranking
- [ ] Hybrid retrieval (vector + BM25) с feature flags
- [ ] RRF fusion с `k=60`
- [ ] Cross-encoder reranker выбран (BGE-reranker / Cohere Rerank) с latency budget
- [ ] Top-N калиброван: 50 для reranker, 5 в LLM
- [ ] Metadata filtering **до** vector search (снижает шум и latency)
- [ ] Tenant isolation: RLS, metadata filters, runtime assertions

### Этап 4: Generation & Safety
- [ ] System prompt с explicit grounding: «Отвечай только на основе контекста. Если ответа нет — скажи об этом»
- [ ] Контроль источников: ответы цитируют retrieved chunks
- [ ] Sanitization layer: проверка на adversarial patterns
- [ ] Guardrails: entity recognition, semantic consistency checks
- [ ] Fallback: контролируемый отказ всегда дешевле и безопаснее уверенной галлюцинации

### Этап 5: Evaluation & Monitoring
- [ ] RAGAS pipeline: Faithfulness > 0.85, Answer Relevancy > 0.85, Context Precision > 0.8
- [ ] Offline eval set: labeled query-document pairs для Recall@K
- [ ] Semantic caching: кешируй ответы на запросы с similarity > 0.95 → снижение LLM calls на 30–50%
- [ ] RAGAS интегрирован в CI/CD: падение метрик → rollback
- [ ] Мониторинг: embedding errors, retrieval latency, ratio retrieved docs / answer tokens

---

## 7. Траблшутинг: 7 критических проблем и их решения

### Проблема 1: Low Recall (нужное не находится)

**Симптом:** модель «не знает» ответа, который точно есть в документах. Context Recall < 0.7.

**Root cause:** recall — upper bound correctness. Нет промпт-инжиниринга, который восстановит факты, никогда не попавшие в контекст.

**Причины:** слишком агрессивный чанкинг, низкий top_k, domain-blind embeddings, нет query rewriting.

**Решения (по порядку приоритета):**
1. Увеличь `top_k` до 10–50, потом фильтруй через reranker
2. Применяй Query Expansion + Multi-Query Retrieval
3. Внедри Hybrid Search (BM25 + dense) — особенно помогает для exact-term technical запросов
4. Смени embedding модель на Qwen3-Embedding-8B или BGE-M3
5. Попробуй Parent-Child chunking: маленькие чанки для поиска, большие для контекста
6. Применяй HyDE для коротких запросов

### Проблема 2: Low Precision (слишком много мусора)

**Симптом:** ответы противоречивы или нерелевантны. Context Precision < 0.6.

**Решения:**
1. Добавь cross-encoder reranking на top-50 → top-5 в LLM
2. Установи similarity threshold (0.7–0.75) — документы ниже не попадают в контекст
3. Применяй metadata filtering до vector search
4. Semantic Chunking для тематически чистых чанков
5. MMR (Maximal Marginal Relevance) для дедупликации похожих результатов

### Проблема 3: Context Poisoning (контекстное отравление)

**Симптом:** модель даёт неожиданно неверные или вредоносные ответы. Атакующий добавляет документы с «instruction override» в knowledge base.

**Механизм:** poisoned content → попадает в context window → LLM следует вредоносным инструкциям.

**Решения (Defense-in-Depth):**
1. **Content filtering при ingestion:** regex на attack patterns, classification model для suspicious content
2. **Source validation:** allowlist approved sources с криптографическими подписями
3. **Embedding analysis:** мониторинг документов с аномально высокими magnitudes
4. **Guardrails:** PromptGuard от Meta для мониторинга retrieved context на suspicious patterns
5. **ACL на уровне retrieval:** никогда не давай LLM решать, какие документы «безопасны»
6. **Attention-Variance Filter (AV Filter):** исследовательский подход — poisoned passages оставляют аномалии в attention weights LLM

### Проблема 4: Lost in the Middle

**Симптом:** ретривер отработал идеально, релевантный чанк в середине контекста, но LLM его игнорирует.

**Решения:**
1. **Reorder documents:** поместить наиболее релевантные чанки в начало и конец
2. **Parent-Child chunking:** вместо 10 мелких чанков — 2–3 родительских с полным контекстом
3. **Context compression:** убрать дублирование и нерелевантные фрагменты
4. **Уменьшить количество:** top-3 вместо top-10 с качественным re-ranking

### Проблема 5: Высокая Latency и Cost

**Симптом:** p90 latency > 3–5s, счёт за токены взрывает бюджет.

**Решения:**
1. **Binary Quantization:** 32x сжатие памяти, 5–10x ускорение
2. **Semantic Caching:** кешируй эмбеддинги частых запросов, similarity > 0.95 = return cached. Снижение LLM calls на 30–50%
3. **Tiered Retrieval:** дешёвая модель для простых запросов, дорогая для сложных
4. **Tune HNSW параметры:** `M`, `ef_construction`, `ef_search` под latency target
5. **REFRAG:** compress-sense-expand фреймворк: 30.85x быстрее TTFT, 16x больший context window

### Проблема 6: Stale Data / Version Conflicts

**Симптом:** модель смешивает current и deprecated версии, отвечает по устаревшим документам.

**Решения:**
1. **Content hashing:** при ingestion считай hash каждого чанка, обновляй только изменившиеся
2. **Metadata versioning:** каждый чанк несёт `version_state` (Current/Deprecated/Draft), `updated_at`
3. **Metadata filter при retrieval:** `version_state == "Current"` как обязательный pre-filter
4. **Semantic deduplication:** косинус > 0.8 → оставляй canonical version
5. **CocoIndex:** инкрементальный ETL, точечно пересчитывает изменённые части без полной переиндексации

### Проблема 7: Hallucinations Despite RAG

**Симптом:** контекст найден, но модель фантазирует.

**Решения:**
1. **Цитирование источников:** ответы ссылаются на retrieved chunks
2. **Faithfulness evaluation:** мониторинг через RAGAS
3. **Self-RAG:** модель проверяет свои ответы через reflection tokens
4. **Сильный системный промпт:** «Отвечай ТОЛЬКО на основе контекста. Если информации нет — скажи: я не знаю»
5. **CRAG:** при низкой уверенности → fallback на веб-поиск

---

## 8. Frontier Research 2026

### 8.1 REFRAG (Meta AI)

RL-based контекстная компрессия перед LLM. Вместо того чтобы сваливать все чанки — RL-политика (2-слойный MLP) оценивает сжатые эмбеддинги и принимает решение select/not-select для каждого чанка.

Результаты:
- **30.85x быстрее** time-to-first-token
- Контекстные окна в **16 раз больше**
- В **2–4x меньше** обрабатываемых токенов
- Обгоняет LLaMA на 16 RAG-бенчмарках

### 8.2 Hy-Memory (Tencent)

6-уровневый memory framework для долгосрочных агентов. Двойная архитектура:
- **System 1 (синхронная):** работает «днём», фиксирует атомарные факты
- **System 2 (асинхронная):** работает «ночью», выявляет латентные намерения, разрешает противоречия

Цифры: на **70% меньше** воспоминаний, плотность информации **выше на 45%**, потребление токенов **снижено на 35%**.

### 8.3 Blockify / IdeaBlock

Препроцессинговый слой между парсером и vector store. 7-ступенчатый пайплайн:
1. Scoping (иерархия индекса)
2. Ingestion (DOCX, PDF, PPT, PNG/JPG, Markdown, HTML)
3. Chunking & extraction (LLM конвертирует в QA-пары)
4. Semantic deduplication (80–85% similarity, 3–5 итераций)
5. Auto-tagging (clearance level, version state, product line)
6. Human validation (2–3K IdeaBlocks → 5–10 SMEs × 1–2 часа/квартал)
7. Export (Azure AI Search, Pinecone, Milvus, Vertex Matching Engine)

Цифры: корпус **сжат в 40x**, retrieval precision **улучшен в 2.29x**, точность **выросла на 13.55%**.

### 8.4 CocoIndex

Open-source инкрементальный ETL движок (Rust core, Python API, Apache 2.0). Вместо ночных пересборок — точечная переиндексация изменившихся чанков. Поддерживает Postgres, LanceDB, Neo4j, Kafka, S3. Работает через MCP с Claude Code, Cursor.

### 8.5 Трёхстадийный Hybrid (SOTA 2026)

BM25 + dense vectors + **SPLADE (learned sparse model)** — стабильно превосходит стандартные двусторонние гибридные модели на бенчмарках 2026.

---

## Приложение A: Obsidian в RAG-архитектуре

### A.1 Obsidian как data layer для RAG

Obsidian vault = источник Markdown-документов (и графа ссылок). Идеально ложится в роль «данного слоя» в RAG-архитектуре.

**Общее устройство стека:**
- Obsidian vault → файловый watcher → парсер `.md`
- Chunker + embeddings → vector store (Chroma/Qdrant/pgvector)
- Retrieval API (FastAPI/Flask) → LLM/агент

### A.2 Обязательные требования к структуре vault

- Чёткая структура папок: `areas/`, `projects/`, `references/`, `daily/` — как у PARA или Zettelkasten
- Консистентные заголовки (`#`, `##`, `###`) — естественные parent chunks
- Максимум atomic notes: одна заметка = одна сущность/идея
- Мета-инфа в YAML frontmatter: `tags`, `aliases`, `status`, `source`, `created`, `updated`

### A.3 Parent-Child чанкинг для Obsidian

- **Parent** = заметка или секция (`#` / `##`) — отдаётся LLM в контекст
- **Child** = абзац/подзаголовок (100–200 токенов) — индексируется в vector store

При retrieval: поиск по children → сбор соответствующих parents → LLM.

### A.4 Использование ссылок и тегов

- **Wiki-links (`[[note]]`):** при ingestion добавлять в metadata `linked_notes`, `linked_from`. При retrieval — расширение контекста на 1–2 hop по графу (GraphRAG-лайт)
- **Tags (`#tag`):** metadata фильтры (`topic: ai`, `status: draft`)

### A.5 Два уровня интеграции

**Уровень 1: Плагины (Copilot / Smart Connections)**
- Поднимают локальный LLM endpoint (Ollama / LLM Studio)
- Строят embeddings заметок, делают семантический поиск
- Минимальный код, фокус на организации знаний

**Уровень 2: Свой RAG API поверх vault**
- Backend (FastAPI) + Postgres с pgvector или Qdrant
- Ingestion скрипт: читает `.md` → парсит frontmatter → chunking → embeddings → DB
- API эндпоинты: `/embed` (reindex), `/query` (RAG + ответ + ссылки на заметки)
- Агент (LangGraph/LlamaIndex) ходит к API как к retriever tool

### A.6 Ключ кэша

```
hash(content) + embedding_model_name + chunking_strategy
```
Иначе при смене стратегии чанкинга — битый кэш.

---

## Приложение B: Второй мозг + RAG

### B.1 Структура vault под RAG (PARA)

- `00_inbox/` — сырые заметки, драфты
- `10_projects/` — активные проекты (каждый проект = папка)
- `20_areas/` — долгие области (AI-агенты, маркетинг, музыка)
- `30_resources/` — референсы: статьи, книги, туториалы (по темам)
- `40_archive/` — завершённое

Для RAG: папка → тип контента (metadata `type=project|area|resource`). Можно фильтровать retrieval (игнорировать inbox/archive).

### B.2 Типы заметок

1. **MoC / Index-заметки** — список ссылок на ключевые заметки по теме. Естественные «опорные parents», хорошие крупные чанки
2. **Source** — одна заметка = один источник. Поля: `type: source`, `source_url`, `author`, `year`
3. **Idea (Atomic note)** — одна заметка = одна мысль. Чистые, короткие, однозначные chunks
4. **Project** — туду/план/архитектура. RAG обычно не нужен, кроме специфичных QA

### B.3 Frontmatter под RAG

```yaml
---
type: idea
topics: [ai, rag, obsidian]
status: active
source: "[[Paper: Late Chunking JinaAI]]"
created: 2026-06-13
updated: 2026-06-13
importance: 3
---
```

Для RAG: готовые metadata filters (type/topics/status), признаки важности для reranking/boost.

### B.4 Правила ведения vault под RAG

1. Не плодить папки >2 уровней — 4–6 крупных «зон», детали через ссылки и теги
2. Делать MoC-страницы для крупных тем/проектов
3. Один md = один источник или одна идея
4. Всегда заполнять frontmatter у важных заметок
5. В конце заметки минимум 1–2 связи на другие заметки
6. Не хранить простыни без заголовков — делить по смыслу так, чтобы естественно мапилось на чанки

### B.5 Конкретный сетап

1. Obsidian Vault с описанной структурой (PARA + MoC + atomic notes)
2. Ingestion сервис: читает `.md` → парсит frontmatter + заголовки → Parent/Child chunking → Qdrant/pgvector
3. Agent (LangGraph/LlamaIndex) с tool `obsidian_retrieve`
4. Obsidian-сторона: hotkey «Спросить у второго мозга» → REST → ответ вставляется в заметку

В результате: человеческий слой (Obsidian — думать/писать/линковать) + машинный слой (агент + RAG поверх vault, которому важна структура и метаданные).

---

## Приложение C: Итоговая рекомендуемая архитектура (80% кейсов)

```
Documents
    ↓
Parser (Docling / LlamaParse / Unstructured)
    ↓
Semantic Chunking + Parent-Child Index
    ↓
Embedding: Qwen3-Embedding-8B или BGE-M3
    ↓
Qdrant (production) / Chroma (prototype)
    ↓
Hybrid Search (BM25 + Dense, RRF k=60)
    ↓
Top-50 → Cross-Encoder Reranker → Top-5
    ↓
LLM (GPT-4o / Claude / Kimi-K2)
    ↓
RAGAS Evaluation (Faithfulness > 0.85, Context Precision > 0.8)
```

Это современный production baseline. Дальнейший прирост — через Query Rewriting, CRAG, Self-RAG и GraphRAG для сложных multi-hop задач.

---

**Обновлено:** 2026-06-13
**Источники:** 4 независимых LLM-ресёрча + Parallel.AI Research + RAG Factura
