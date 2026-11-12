<div align="center">

<img width="100%" src="./assets/header.svg" alt="Михаил (DelixeamI): AI & LLM Systems Engineer" />

[![Telegram](https://img.shields.io/badge/Telegram-@Mihail__r63-26A5E4?style=flat-square&logo=telegram&logoColor=white)](https://t.me/Mihail_r63)
[![Email](https://img.shields.io/badge/Email-mikhailivanovr%40gmail.com-EA4335?style=flat-square&logo=gmail&logoColor=white)](mailto:mikhailivanovr@gmail.com)

**Самара, Россия · удалённо или гибрид**

</div>

---

## Профиль

AI / LLM Engineer. Строю LLM-сервисы, которым можно доверять чувствительные данные: модель отвечает за язык, а цифры, проверки и решения — за детерминированный код.

Единственный разработчик в медицинской лаборатории: в 2025 году по своей инициативе запустил направление AI-автоматизации и вывел в прод сервисы обработки документов и сопоставления каталогов. Специализируюсь на гибридном RAG, каскадном матчинге номенклатуры, автоматизации первичной документации и работе с отечественными LLM (GigaChat, YandexGPT) и локальными моделями (Ollama, Qwen) в закрытых контурах с соблюдением 152-ФЗ.

Инженерная база: автотесты и `mypy --strict` в открытых проектах, офлайн-оценка качества, системный бэкграунд в Linux, сетях и мониторинге.

---

## Технический стек

| Направление | Стек и технологии |
|---|---|
| **AI & LLM Engineering** | GigaChat API (OAuth, сертификаты Минцифры), YandexGPT, Ollama, Qwen 2.5/3, Structured Outputs (Pydantic v2 / JSON Schema), Tool Calling, LangGraph, Prompt Engineering |
| **RAG и поиск** | Гибридный поиск (dense + sparse), Reciprocal Rank Fusion (RRF), cross-encoder реранкинг (`bge-reranker-v2-m3`), эмбеддинги (`multilingual-e5`, `bge-m3`), BM25, PostgreSQL FTS |
| **Хранилища** | PostgreSQL (pgvector, HNSW, SQL-фильтрация), Redis (кэш, rate limiting, дедупликация), Qdrant |
| **Бэкенд и архитектура** | Python 3.12, FastAPI, asyncio (Semaphore, Token Bucket, DAG-пайплайны), Pydantic v2, REST API, интеграция с 1С |
| **Надёжность и оценка** | Fallback между провайдерами (GigaChat → YandexGPT, локальные модели), ретраи с full jitter, метрики Hit Rate@k / MRR / Recall, подбор порогов на отдельной выборке и проверка на отложенной |
| **Инфраструктура** | Docker, Docker Compose, Linux (Debian/Ubuntu), GitHub Actions, quality gates в CI, Zabbix, Grafana, 152-ФЗ |

---

## Коммерческий опыт — ООО «СкайЛаб», медицинская лаборатория (под NDA)

Два production-сервиса и MVP медицинского AI-продукта, от требований до поддержки.

* **Обработка первичных документов (счета, УПД, акты) → 1С — Production:**
  - Асинхронный конвейер на FastAPI, `pdfplumber`, OCR и GigaChat API со Structured Outputs для документов от поставщиков реагентов и расходников.
  - Сократил трудозатраты бухгалтерии на ~70%: ~78% входящего потока (и свыше 90% цифровых PDF) проходят по быстрому сценарию — около 45 секунд вместе с ревью оператора вместо 6–8 минут ручного разбора.
  - Точность на контрольной выборке из 200 документов: 98,2% по реквизитам и 94,5% по строкам таблиц.
  - Детерминированный валидатор сумм и НДС, сверка реквизитов через tool calling, экспорт в 1С; двухуровневая защита от дублей в Redis исключает повторную платную обработку и двойные проводки.

* **Сопоставление прайс-листов партнёров с мастер-каталогом — Production:**
  - Автоматическое сопоставление прайсов поставщиков и франчайзи (Excel/PDF) с номенклатурой лаборатории: онбординг прайса сократился с нескольких дней ручной работы до часа, нагрузка на методистов — в ~8 раз.
  - Retrieval-слой на PostgreSQL + pgvector (HNSW, multilingual-e5): Hit Rate@1 вырос с 0.68 до 0.89 и MRR с 0.74 до 0.91 относительно BM25.
  - LLM-скоринг на GigaChat Lite и трёхзонный триаж уверенности (GigaChat Pro с fallback на YandexGPT → оператор): Hit Rate@1 — 0.94, 82% позиций матчатся автоматически с precision 99,1%.
  - Асинхронный DAG на FastAPI и asyncio с семафорами и микро-батчингом: latency снижена в 4 раза, quality gate в CI блокирует деплой при падении точности.

* **Интерпретация результатов анализов для пациентов — MVP:**
  - Детерминированное ядро (извлечение геометрии PDF без OCR, контракты Pydantic v2 над YAML-базой знаний) само вычисляет отклонения и границы норм: модель не считает и не меняет клинические значения. LLM только формулирует пояснения по схеме «факты → модель → проверка кодом → повтор или шаблон».
  - Гибридный RAG-справочник по 24 клиническим рекомендациям Минздрава (pgvector + bge-m3 + BM25 + RRF): Recall@3 = 92% на 42 контрольных вопросах.
  - Мультипровайдерный LLM-слой: локальная Ollama (Qwen) в закрытом контуре, GigaChat API в расширенном режиме; наружу уходят только обезличенные данные. 362 автотеста, 0 сбоев на реальных бланках.

---

## Открытые проекты

### 1. [hybrid-rag: гибридный RAG по Трудовому кодексу с детерминированной валидацией и калиброванным отказом](https://github.com/DelixeamI/hybrid-rag)

[![CI](https://img.shields.io/github/actions/workflow/status/DelixeamI/hybrid-rag/ci.yml?branch=main&label=CI&logo=github)](https://github.com/DelixeamI/hybrid-rag/actions/workflows/ci.yml)
[![License](https://img.shields.io/badge/License-MIT-yellowgreen.svg)](https://github.com/DelixeamI/hybrid-rag/blob/main/LICENSE)
[![python](https://img.shields.io/badge/python-3.12-blue.svg)](https://github.com/DelixeamI/hybrid-rag/blob/main/pyproject.toml)

Сервис ответов на вопросы по Трудовому кодексу РФ. Основная инженерная задача — надёжный ретривал, калиброванный отказ на нерелевантных вопросах и проверка того, что ответ опирается на источник.

- **гибридный ретривер:** dense `multilingual-e5-small` в pgvector и BM25 на лексемах PostgreSQL (`russian`), объединённые через RRF (top-20);
- **cross-encoder реранкер:** `bge-reranker-v2-m3` пересортирует top-20 кандидатов гибрида и оставляет top-5: Recall@5 растёт с 84,9% до 90,8%, MRR — с 0.727 до 0.816 на 76 вопросах с ответом (у одного dense-поиска Recall@5 80,3%);
- **отказ по порогу:** если лучший скор реранкера ниже порога, система отказывает до вызова LLM. Порог подобран на DEV-половине вопросов; на отложенной TEST-половине — 8 из 8 корректных отказов и 0 ложных на 37 вопросах с ответом (на всём наборе 16/16 и 0/76);
- **детерминированный валидатор:** синтаксическая проверка grounding (числа с единицами, номера статей, цитаты есть в процитированном тексте) проходит у 71 из 76 данных ответов (93,4%); при провале — деградация до списка статей. Это проверка опоры на текст, а не юридической правильности;
- **кассетный офлайн-инференс:** `RecordedLLM` воспроизводит сохранённые ответы, CI работает без внешних API;
- **FastAPI-сервис:** эндпоинты `/ask` и `/health`, JSON-логи с латентностью по стадиям; ингест идемпотентен по отпечатку индекса (файл, модель эмбеддингов, чанкинг, версия парсера).

145 тестов, запуск без сети и API-ключей. Архитектурные альтернативы и компромиссы — в [DESIGN.md](https://github.com/DelixeamI/hybrid-rag/blob/main/DESIGN.md).

---

### 2. [entity-cascade: каскадный пайплайн сопоставления номенклатуры](https://github.com/DelixeamI/entity-cascade)

[![CI](https://img.shields.io/github/actions/workflow/status/DelixeamI/entity-cascade/ci.yml?branch=main&label=CI&logo=github)](https://github.com/DelixeamI/entity-cascade/actions/workflows/ci.yml)
[![License](https://img.shields.io/badge/License-MIT-green)](https://github.com/DelixeamI/entity-cascade/blob/main/LICENSE)
[![python](https://img.shields.io/badge/python-3.12-blue)](https://github.com/DelixeamI/entity-cascade/blob/main/pyproject.toml)

Сопоставление прайс-листов поставщиков с эталонным каталогом на публичных бенчмарках Abt-Buy и Amazon-Google. Ключевая задача — найти баланс между долей автоматизации и точностью без затрат на облачные токены.

- **трёхзонная маршрутизация:** автоподтверждение (`auto_match`) для пар с высоким скором и отрывом, серая зона для локальной LLM и эскалация оператору (`human_review`);
- **двухстадийный гибридный поиск:** эмбеддинги `bge-small-en-v1.5` и BM25 со слиянием RRF (top-20) плюс cross-encoder `ms-marco-MiniLM-L-6-v2`;
- **доменные признаки:** коды моделей, бренды и запас уверенности между кандидатами подняли долю автоматизации с 28,3% до 67,7% при точности 96,7%;
- **локальный LLM-арбитр:** `qwen2.5:14b` через Ollama с Pydantic-контрактом разрешает 45,4% серой зоны с точностью 84,8%; с арбитром система автоматизирует 82,3% при precision 96,3%;
- **калибровка порогов:** только на валидационной выборке, метрики — на тестовой; нижняя граница Уилсона (95%) доступна как опция и показана в таблице компромиссов для сравнения;
- **перенос домена:** проверка устойчивости калибровки на втором датасете (Amazon-Google) с фиксацией изменения метрик.

169 тестов, запуск без сети и API-ключей. Компромиссы — в [DESIGN.md](https://github.com/DelixeamI/entity-cascade/blob/main/DESIGN.md).

---

### 3. [llm-chat-api: отказоустойчивый асинхронный LLM-шлюз](https://github.com/DelixeamI/llm-chat-api)

[![CI](https://github.com/DelixeamI/llm-chat-api/actions/workflows/ci.yml/badge.svg)](https://github.com/DelixeamI/llm-chat-api/actions/workflows/ci.yml)
[![License](https://img.shields.io/badge/License-MIT-green)](https://github.com/DelixeamI/llm-chat-api/blob/main/LICENSE)
[![python](https://img.shields.io/badge/python-3.12-blue.svg)](https://github.com/DelixeamI/llm-chat-api/blob/main/pyproject.toml)
[![Checked with mypy](https://img.shields.io/badge/mypy-strict-blue.svg)](https://mypy-lang.org/)

Асинхронный API-шлюз на FastAPI для предсказуемой работы с LLM-провайдерами.

- **отказоустойчивость:** ограничение параллелизма через семафоры, таймауты на попытку, экспоненциальные повторы с full jitter только для временных сбоев (429/5xx), изоляция провайдеров через Protocol;
- **Structured Output с самоисправлением:** валидация Pydantic и контур автоматического исправления ответов модели (feedback recovery loop);
- **контроль контекста и биллинг:** предварительный расчёт токенов, учёт стоимости в `Decimal`, отчёты в USD и RUB;
- **кэширование и защита:** exact-match кэш в Redis (`temperature=0`), rate limiting на скользящем окне (Lua), идемпотентность через `Idempotency-Key`;
- **наблюдаемость:** JSON-логи с `X-Request-ID`, метрики Prometheus (`/metrics`), трассировка OpenTelemetry.

142 теста, покрытие 96%, strict-типизация mypy. Компромиссы — в [docs/trade-offs.md](https://github.com/DelixeamI/llm-chat-api/blob/main/docs/trade-offs.md).

---

### 4. [agentic-sql-analyst: мультиагентный Text-to-SQL аналитик](https://github.com/DelixeamI/agentic-sql-analyst)

[![CI](https://img.shields.io/github/actions/workflow/status/DelixeamI/agentic-sql-analyst/ci.yml?branch=main&label=CI&logo=github)](https://github.com/DelixeamI/agentic-sql-analyst/actions/workflows/ci.yml)
[![License](https://img.shields.io/badge/License-MIT-green)](https://github.com/DelixeamI/agentic-sql-analyst/blob/main/LICENSE)
[![python](https://img.shields.io/badge/python-3.12-blue.svg)](https://github.com/DelixeamI/agentic-sql-analyst/blob/main/pyproject.toml)

Аналитик на LangGraph: отвечает на вопросы к базе данных на естественном языке, сам пишет и исправляет SQL и строит отчёт с графиком. Ключевая задача — дать агенту доступ к данным так, чтобы он не мог навредить базе.

- **граф агентов на LangGraph:** планировщик, генератор SQL, AST-проверка, исполнитель, исправление, синтез ответа и отказ — как узлы машины состояний;
- **трёхуровневая песочница:** разбор запроса через `sqlglot` и проверка AST, отдельная роль PostgreSQL только на чтение, ограничения на выполнение;
- **цикл самоисправления:** ошибка выполнения возвращается генератору с контекстом, число попыток ограничено;
- **отчёты:** ответ с графиком в Vega-Lite; API `/query`, `/schema`, `/health`, `/examples`;
- **провайдеры:** демо-режим без API-ключа и любой OpenAI-совместимый endpoint, включая локальную Ollama;
- **данные:** синтетическая e-commerce база, генерируется детерминированными SQL-скриптами.

129 тестов, в том числе 40+ кейсов на AST-проверку и интеграционные тесты с PostgreSQL через testcontainers. Оценка точности на реальной модели — следующий шаг.

---

## Контакты

- **Telegram:** [@Mihail_r63](https://t.me/Mihail_r63)
- **Email:** [mikhailivanovr@gmail.com](mailto:mikhailivanovr@gmail.com)
- **Локация:** Самара, Россия · удалённая работа или гибрид
