# Mikhail

**AI & LLM Systems Engineer · Production LLM Backend · Healthcare & Multimodal AI**  
Russia · Remote · mihail_jackson@list.ru · [Telegram](https://t.me/your_handle)  
[GitHub Profile](https://github.com/DelixeamI) · [AI Engineering Portfolio](AI_LLM_PORTFOLIO.md) · [Case Studies](CASE_STUDIES.md)

---

## Executive Summary

AI / LLM Systems Engineer with 2.5+ years of production infrastructure engineering (Linux, Docker, Zabbix/Grafana cluster observability, network security) and commercial Python backend experience in Healthcare (Laboratory Information Systems, medical analyzer protocol integration). Specialized in high-reliability LLM backend architectures, deterministic guardrails, self-hosted local inference (Ollama, Qwen 2.5/3, BGE-M3), and hybrid retrieval (RAG). Shipped a medical price list synchronization pipeline reaching 97.5% precision on a 196-item benchmark, built a zero-hallucination clinical report interpreter guarded by 218 unit tests, and engineered a 14-stage multimodal DAG pipeline achieving 6.5x real-time video processing at $0.00 compute cost.

---

## Technical Skills

- **AI & LLM Engineering**: Ollama, Local Qwen 2.5/3, Gemini 3.1 API, PyTorch, LangGraph / State DAG concepts, Pydantic v2 Structured Outputs, JSON Schema constrained decoding, Prompt Engineering, Guardrails, Offline Evaluation Benchmarks.
- **RAG & Search**: BGE-M3 dense embeddings, Cosine similarity, RapidFuzz lexical matching, Hybrid retrieval, Re-ranking, Candidate filtering.
- **Backend & APIs**: Python 3.12, FastAPI, AsyncIO, Pydantic v2, RESTful APIs, SSE streaming, Strict typing (`mypy --strict`), Poetry/Uv.
- **Databases & Storage**: PostgreSQL (Relational modeling, transactions), Redis (Response caching at temp=0, semaphores, rate limiting).
- **DevOps & Observability**: Linux (Debian/Ubuntu), Docker, Docker Compose, Zabbix, Grafana, Git, GitHub Actions, Hardware-accelerated inference (NVIDIA RTX 5070 / CUDA).
- **Domain & Security**: Laboratory Information Systems (LIS AlfaLab), RS-232 / TCP analyzer protocols (ASTM/HL7), Subnet VLAN isolation, Digital signature infrastructure (EDS / ЭЦП).

---

## Professional Experience

### AI & Systems Engineer · SkyLab Medical Laboratory
**June 2023 - Present · On-site / Hybrid**

- **Cross-Catalog Lab Price Matcher (`lab-price-matcher`)**:
  - Architected a 3-tier cascade (`retrieve → filter → judge`) mapping 2,300+ competitor medical services against 656 internal tests.
  - Implemented hybrid candidate retrieval combining BGE-M3 dense vectors with RapidFuzz token matching.
  - Formulated ~20 deterministic medical constraint rules (`compatible()`) in Python, filtering out invalid antibody and test method pairings prior to model invocation.
  - Deployed local `qwen3:8b` via Ollama on an RTX 5070 with constrained JSON Schema output; enabling reasoning mode reduced false matches from 29 to 3.
  - Achieved **97.5% Precision** and **88.1% Catalog Coverage** on an offline 196-item benchmark.
- **Clinical Lab Report Interpreter (`skylab-med-ai`)**:
  - Architected a 5-layer diagnostic report interpreter strictly decoupling deterministic numeric checks from natural language synthesis.
  - Built an adversarial PII de-identification layer ensuring patient identifiable data never reaches model contexts.
  - Developed a comprehensive **218-test automated test suite** validating boundary conditions, unit conversions, and silent failure handlers.
- **Laboratory Information System & Hardware Protocol Engineering**:
  - Engineered custom Python modules to ingest, parse, and validate continuous binary and text streams from automated diagnostic analyzers into AlfaLab LIS.
  - Designed and operated full-stack telemetry using **Zabbix + Grafana** across physical and virtual server clusters running LIS databases and services.
  - Implemented network segregation (isolated medical device VLANs vs administrative networks) and managed digital cryptographic signature (EDS/ЭЦП) lifecycle.

---

## Featured AI & Engineering Projects

### Multimodal 14-Stage DAG Pipeline · `stream-highlight-harvester`
**Stack: Python 3.12, Whisper ASR, Silero VAD, OpenCV, Video-VLM, NVENC, DAG Engine**
- Built a modular DAG engine with SHA-256 content-addressable caching to skip re-running heavy transcription and audio separation stages.
- Designed a 2-tier architecture where fast local models (Whisper with phonetic gaming lexicon, Silero VAD, CV motion) prune 98% of dead time.
- Implemented an 8-keyframe bundle strategy (~300 KB) feeding a multimodal VLM (Gemini Flash Lite / Qwen 2.5 14B) with strict Pydantic `JudgeVerdict` contracts.
- Achieved **6.5x real-time speed** (3.5h stream in 28 min) at **$0.00 compute cost** with 113 passing unit tests.

### Production Async LLM Gateway · `llm-chat-api`
**Stack: FastAPI, AsyncIO, Pydantic v2, PostgreSQL, Redis, Ollama, Docker Compose**
- Developed an asynchronous LLM backend with Redis caching for zero-temperature requests, cutting repeat response latency to <10ms.
- Built concurrency controls using async semaphores, per-request token budgeting, and sliding context window management.
- Enforced JSON Schema structured output decoding (100% schema conformance on validation tests vs 0% prompt-only failure rate).
- Validated with `mypy --strict` and 142 automated tests.

---

## Education

**University Degree in Computer Science / Information Systems**  
Focus: Linux systems administration, Docker, Ansible, Python backend engineering, Neural Networks & Deep Learning (PyTorch).
