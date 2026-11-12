<div align="center">

<img width="100%" src="./assets/header.svg" alt="Михаил (DelixeamI): AI & LLM Systems Engineer" />

[![Telegram](https://img.shields.io/badge/Telegram-@Mihail__r63-26A5E4?style=flat-square&logo=telegram&logoColor=white)](https://t.me/Mihail_r63)
[![Email](https://img.shields.io/badge/Email-mikhailivanovr%40gmail.com-EA4335?style=flat-square&logo=gmail&logoColor=white)](mailto:mikhailivanovr@gmail.com)
[![GitHub](https://img.shields.io/badge/GitHub-DelixeamI-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/DelixeamI)

<br/>

**Telegram: [@Mihail_r63](https://t.me/Mihail_r63) | Email: [mikhailivanovr@gmail.com](mailto:mikhailivanovr@gmail.com) | Самара, Россия**

</div>

---

## Профиль

AI / LLM Systems Engineer с практическим опытом проектирования детерминированных, экономически эффективных и отказоустойчивых LLM-сервисов в сфере B2B-автоматизации и Healthcare.

Специализируюсь на гибридном RAG, каскадном сопоставлении номенклатуры, автоматизации первичной документации и интеграции отечественных LLM (GigaChat, YandexGPT) и локальных моделей (Ollama, Qwen) в закрытых контурах с соблюдением 152-ФЗ. 

Инженерный фундамент подкреплен 560+ автотестами в открытых проектах, строгой типизацией (`mypy --strict`) и системным бэкграундом в Linux, сетях (VLAN) и мониторинге (Zabbix, Grafana).

---

## Технический стек

| Направление | Стек и технологии |
|---|---|
| **AI & LLM Engineering** | GigaChat API (OAuth, сертификаты Минцифры), YandexGPT, Ollama, Local Qwen 2.5/3, Structured Outputs (Pydantic v2 / JSON Schema), Tool Calling, Prompt Engineering |
| **RAG и поиск данных** | Гибридный поиск (Dense + Sparse), Reciprocal Rank Fusion (RRF), Cross-Encoder реранкинг (`bge-reranker-v2-m3`), плотные эмбеддинги (`multilingual-e5-base`, `bge-m3`), лексический поиск (BM25, PostgreSQL FTS) |
| **Векторные БД и хранилища** | PostgreSQL (pgvector с HNSW-индексами, SQL payload-фильтрация), Redis (exact-match кэш, rate limiting, дедупликация по SHA-256), Qdrant |
| **Бэкенд и архитектура** | Python 3.12, FastAPI, AsyncIO (asyncio.Semaphore, Token Bucket, DAG-пайплайны), Pydantic v2, RESTful APIs, интеграция с 1С (OpenAPI/HTTP-сервисы) |
| **Отказоустойчивость & Evals** | Multi-provider fallback (GigaChat → Yandex Cloud / YandexGPT, локальные модели), ретраи с full jitter, расчет метрик (Hit Rate@k, MRR, Context Recall, Faithfulness), калибровка порогов отказа (Wilson 95% bound) |
| **Инфраструктура & Безопасность** | Docker, Docker Compose, Linux (Debian/Ubuntu), GitHub Actions CI/CD, Quality Gates, Zabbix, Grafana, 802.1Q VLAN, регламенты 152-ФЗ, соглашения DPA (Zero Retention) |

---

## Коммерческий опыт (ООО «СкайЛаб», в рамках NDA)

Лидировал направление прикладной AI-автоматизации, разработав и внедрив 2 production-сервиса и MVP медицинского AI-продукта:

* **Автоматизация обработки первичных документов (счета, УПД, акты) — Production:**
  - Асинхронный конвейер на FastAPI, `pdfplumber`, GigaChat API (со Structured Outputs) и OCR с потоком ~1 200 документов в месяц от поставщиков реагентов.
  - Сократил трудозатраты бухгалтерии на ~70%: ~78% входящего потока (и свыше 90% цифровых PDF) обрабатываются за 45 секунд (включая 30 с финального ревью) вместо 6–8 минут ручного разбора. Точность извлечения — 98.2% по реквизитам и 94.5% по строкам таблиц.
  - Детерминированный валидатор сумм и НДС (допуск ±0.05 руб.), сверка реквизитов через Tool Calling (OpenAPI) и прямой экспорт в 1С; двухуровневая защита от дублей в Redis (SHA-256 хеш исключает повторную платную обработку, затраты ~3.5 ₽ на документ).

* **Каталожный матчинг прайс-листов с мастер-каталогом — Production:**
  - Автоматическое сопоставление партнерских прайсов (Excel/PDF, до 5 000 позиций) с номенклатурой лаборатории: сократил цикл онбординга с 2–3 дней до ~50 минут, нагрузка на методистов снизилась с 4 до 0.5 FTE (поток ~45 прайсов/мес).
  - Retrieval-слой на PostgreSQL + pgvector (HNSW, multilingual-e5) поднял Hit Rate@1 с 0.68 до 0.89 и MRR с 0.74 до 0.91 относительно BM25 на эталоне в 1 500 позиций.
  - Быстрый скоринг на GigaChat Lite с fallback на YandexGPT и 3-зонный триаж уверенности: автоматизировал 82% позиций с precision 99.1% (Hit Rate@1 вырос до 0.94).
  - Асинхронный DAG на FastAPI и asyncio с семафорами и микро-батчингом удержал себестоимость процессинга прайса на уровне ~45 ₽ при соблюдении 152-ФЗ.

* **SkyLab Med AI (Интерпретация лабораторных бланков для пациентов) — MVP:**
  - Детерминированное ядро (извлечение геометрии PDF без OCR, 100 Pydantic v2 контрактов) + LLM-слой подбора пояснений по схеме «сэндвич» (факты → модель → проверка кодом → шаблон) с гарантией 0% медицинских галлюцинаций.
  - Гибридный RAG-справочник по 24 клиническим рекомендациям Минздрава (pgvector + bge-m3 + BM25 + RRF, Recall@3 = 92% на 42 вопросах).
  - Единый интерфейс провайдеров (GigaChat с сертификатами Минцифры, Gemini, Ollama), строгая деперсонализация ПДн, 362 автотеста, 0 сбоев на 139 реальных бланках.

---

## Избранные инженерные проекты (Reference Implementations)

### 1. [hybrid-rag: Гибридный RAG по Трудовому кодексу с детерминированной валидацией и калибровкой отказа](https://github.com/DelixeamI/hybrid-rag)

[![CI](https://img.shields.io/github/actions/workflow/status/DelixeamI/hybrid-rag/ci.yml?branch=main&label=CI&logo=github)](https://github.com/DelixeamI/hybrid-rag/actions/workflows/ci.yml)
[![License](https://img.shields.io/badge/License-MIT-yellowgreen.svg)](https://github.com/DelixeamI/hybrid-rag/blob/main/LICENSE)
[![python](https://img.shields.io/badge/python-3.12-blue.svg)](https://github.com/DelixeamI/hybrid-rag/blob/main/pyproject.toml)
[![tests](https://img.shields.io/badge/tests-141-brightgreen.svg)](https://github.com/DelixeamI/hybrid-rag/tree/main/tests)

Сервис ответов на юридические вопросы по Трудовому кодексу РФ с детерминированным контролем фактологии и тестами, подтверждающими качество на каждом шаге. Основная инженерная задача в RAG — надежный ретривал, калиброванный порог отказа на нерелевантных вопросах и строгая проверка того, что каждый ответ подтвержден источником.

- **гибридный ретривер:** комбинация плотного поиска (dense `multilingual-e5-small` с pgvector) и разреженного BM25 на полнотекстовых лексемах PostgreSQL (`russian`), объединенных через Reciprocal Rank Fusion (RRF, top-20);
- **cross-encoder реранкер:** модель `bge-reranker-v2-m3` отбирает top-5 релевантных чанков, повышая Recall@5 с 80.3% до 90.8% и MRR с 0.696 до 0.816 на полном тексте кодекса;
- **калиброванный отказ от генерации:** порог уверенности реранкера ($0.0066$) отсекает нерелевантные запросы до вызова LLM, обеспечивая 100% корректных отказов на `no_answer` вопросах и 0% ложных отказов на ответимых;
- **детерминированный валидатор фактов и цитат:** синтаксическая проверка grounding (числа, сроки, статьи и дословные цитаты), подтверждающая 93.4% данных ответов (71 из 76), и безопасная деградация до списка статей при непрохождении проверки;
- **кассетный офлайн-инференс:** класс `RecordedLLM` воспроизводит сохраненные ответы без обращения к внешним API в CI/CD;
- **асинхронный FastAPI-сервис:** эндпоинты `/ask` и `/health`, структурированные JSON-логи с разбивкой латентности по стадиям и идемпотентный ингест по sha256 документа.

**141 тест**, работа без внешней сети и API-ключей. В [DESIGN.md](https://github.com/DelixeamI/hybrid-rag/blob/main/DESIGN.md) зафиксированы архитектурные альтернативы и измеренные компромиссы.

---

### 2. [entity-cascade: Каскадный пайплайн сопоставления номенклатуры товаров](https://github.com/DelixeamI/entity-cascade)

[![CI](https://img.shields.io/github/actions/workflow/status/DelixeamI/entity-cascade/ci.yml?branch=main&label=CI&logo=github)](https://github.com/DelixeamI/entity-cascade/actions/workflows/ci.yml)
[![License](https://img.shields.io/badge/License-MIT-green)](https://github.com/DelixeamI/entity-cascade/blob/main/LICENSE)
[![python](https://img.shields.io/badge/python-3.12-blue)](https://github.com/DelixeamI/entity-cascade/blob/main/pyproject.toml)
[![tests](https://img.shields.io/badge/tests-169_passed-brightgreen)](https://github.com/DelixeamI/entity-cascade/tree/main/tests)

Каскадный конвейер сопоставления прайс-листов поставщиков с эталонным каталогом продукции. Ключевая инженерная задача — поиск оптимальной границы между долей автоматизации и точностью системы при $0.00 расходов на облачные токены.

- **трехзонная маршрутизация:** автоматическое подтверждение (`auto_match`) для пар с высоким скором и отрывом (margin), серая зона (`gray band`) для разрешения локальной LLM и эскалация оператору (`human_review`) для сомнительных строк;
- **двухстадийный гибридный поиск:** плотные векторные эмбеддинги (`bge-small-en-v1.5`) и BM25 с ранговым слиянием RRF (top-20), дополненные cross-encoder моделью (`ms-marco-MiniLM-L-6-v2`);
- **доменные фичи и эвристики:** выделение кодов моделей и брендов с расчетом запаса уверенности между кандидатами, что повышает долю автоматизации с 28.3% до 67.7% при сохранении точности 96.7%;
- **локальный LLM-арбитр с JSON-схемой:** квантованная модель `qwen2.5:14b` через Ollama с Pydantic-контрактом, закрывающая 45.4% серой зоны с точностью 84.8% при $0.00 расходов на токены;
- **калибровка порогов:** настройка порогов срабатывания строго на валидационной выборке с оценкой нижней границы Вильсона (Wilson 95% bound) для защиты от переобучения;
- **контроль переноса домена:** оценка устойчивости калибровки при применении к новому датасету (`Amazon-Google`) с фиксацией изменения метрик.

**169 тестов**, запуск без сети и API-ключей. В [DESIGN.md](https://github.com/DelixeamI/entity-cascade/blob/main/DESIGN.md) задокументированы архитектурные компромиссы.

---

### 3. [llm-chat-api: Отказоустойчивый асинхронный LLM Gateway](https://github.com/DelixeamI/llm-chat-api)

[![CI](https://github.com/DelixeamI/llm-chat-api/actions/workflows/ci.yml/badge.svg)](https://github.com/DelixeamI/llm-chat-api/actions/workflows/ci.yml)
[![License](https://img.shields.io/badge/License-MIT-green)](https://github.com/DelixeamI/llm-chat-api/blob/main/LICENSE)
[![python](https://img.shields.io/badge/python-3.12-blue.svg)](https://github.com/DelixeamI/llm-chat-api/blob/main/pyproject.toml)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.115+-009688.svg?logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![tests](https://img.shields.io/badge/tests-142_passed-brightgreen.svg)](https://github.com/DelixeamI/llm-chat-api/tree/main/tests)
[![Checked with mypy](https://img.shields.io/badge/mypy-strict-blue.svg)](https://mypy-lang.org/)

Высокопроизводительный асинхронный API-шлюз и бэкенд на FastAPI для предсказуемой и надёжной работы с большими языковыми моделями (LLM).

- **многоуровневая отказоустойчивость:** ограничение параллелизма через семафоры, таймауты на попытку, экспоненциальные повторы с full jitter только для временных сбоев (429/5xx), изоляция провайдеров через Protocol;
- **гарантированный Structured Output:** валидация Pydantic с контуром автоматического исправления ошибок модели (feedback recovery loop);
- **контроль контекста и биллинг:** предварительный расчет токенов `fit()`, учет стоимости генераций в типе `Decimal`, отчеты в USD и RUB;
- **кэширование и защита:** exact-match кэш ответов в Redis (`temperature=0`, <10 мс latency), rate limiting на скользящем окне Lua и гарантия идемпотентности через `Idempotency-Key`;
- **наблюдаемость (Observability):** структурированные JSON-логи с `X-Request-ID`, метрики Prometheus (`/metrics`) и трассировка OpenTelemetry.

**142 теста**, покрытие 96%, strict-типизация mypy. В [docs/trade-offs.md](https://github.com/DelixeamI/llm-chat-api/blob/main/docs/trade-offs.md) описаны архитектурные компромиссы.

---

## Контакты

- **Telegram:** [@Mihail_r63](https://t.me/Mihail_r63)
- **Email:** [mikhailivanovr@gmail.com](mailto:mikhailivanovr@gmail.com)
- **Резюме на hh.ru:** [Резюме на HeadHunter (hh.ru)](https://hh.ru) *(прямая ссылка на опубликованное резюме)*
- **Локация:** Самара, Россия · Полная удаленная работа / Гибрид
