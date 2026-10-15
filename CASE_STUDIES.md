# Engineering Case Studies

**Mikhail (DelixeamI)**  
AI / LLM Systems Engineer · Production LLM Backend · Healthcare & Multimodal AI Platforms  
[GitHub](https://github.com/DelixeamI) · [Telegram](https://t.me/your_handle) · [Email](mailto:mihail_jackson@list.ru)

These case studies detail real production problems, systems designed, personal responsibilities, and quantified engineering outcomes. Proprietary clinical rules, private patient records, and internal credentials are de-identified or omitted.

---

## 1. Automated Cross-Laboratory Price List Normalization

### Problem & Context
SkyLab Medical Laboratory required mapping its internal service catalog (~650 items) against a competitor's expanded catalog of 2,300+ diagnostic services to normalize market pricing.
- Incompatible internal nomenclatures, arbitrary abbreviations, and mixed alphabets (Cyrillic lookalikes in Latin terms like "IgM") made simple database joins impossible.
- An erroneous match carries direct financial risk: assigning the price of a comprehensive panel to an isolated biomarker creates business loss. Therefore, **precision is paramount (false positive pairs are far worse than unmatched pairs)**.
- Price lists are strictly confidential and could not be transmitted to cloud APIs.

### System Architecture & Pipeline
A 3-tier cascade (`retrieve → filter → judge`):
1. **Intake & Lexical Normalization**: Regex cleanup and `delookalike()` font normalizer resolving mixed Cyrillic/Latin characters.
2. **Hybrid Candidate Retrieval**: Union of top-15 candidates from BGE-M3 dense embeddings and RapidFuzz token-ratio matching.
3. **Deterministic Domain Gatekeeper**: ~20 hardcoded domain rules (`compatible()`) rejecting mismatches in antibody types (IgM vs IgG), pathogen strains, test methods (qualitative vs quantitative PCR), and sample types before model invocation.
4. **Local LLM Arbiter**: Local `qwen3:8b` via Ollama on an RTX 5070 running in reasoning mode with constrained JSON Schema output, returning matched candidate indices and confidence levels.
5. **Agreement Logic**: High-confidence agreement between heuristic rules and LLM predictions are automatically written; edge cases are routed to human review.

### My Responsibility
- Defining the pipeline architecture and the `retrieve → filter → judge` separation.
- Developing the normalization routines and the domain-rule predicate `compatible()`.
- Designing the JSON Schema and Pydantic validation contracts for the LLM arbiter.
- Implementing the offline double-annotated ground-truth benchmark (196 tests).
- Analyzing error cases and demonstrating why model reasoning reduced false pairs.

### Results & Impact
- **Precision**: Reached **97.5%** on the verified 196-item benchmark (surpassing the heuristic baseline of 96.2%).
- **Coverage**: Successfully mapped **88.1% of all items (578/656)** with prices, exceeding the laboratory stakeholder's requirement of ≥85%.
- **Error Reduction**: Enabling reasoning mode on Qwen 8B dropped false matches from **29 down to 3**.
- **Data Security**: 100% local self-hosted inference; zero proprietary data transmitted externally.

---

## 2. Clinical Lab Report Interpretation with Strict Guardrails

### Problem & Context
Patients receiving PDF lab reports struggle to comprehend diagnostic results. SkyLab sought an MVP service to parse PDF test blanks and provide accessible, plain-language summaries highlighting out-of-range indicators.
- **The Core Risk**: LLMs are inherently probabilistic and prone to hallucinating reference intervals, inventing false numbers, or attempting medical diagnoses.
- **Privacy Constraint**: Russian Federal Law 152-FZ on Personal Data strictly prohibits sending Patient Identifiable Information (PII) to uncertified third-party services.

### System Architecture
A strictly decoupled 5-tier pipeline:
1. **PDF Text Extraction**: Structured extraction of tabular test results via `pdfplumber` and robust regex tokenization.
2. **PII De-identification**: Stripping patient names, birthdates, specimen barcodes, and clinician notes before entering internal structures.
3. **Deterministic Clinical Engine**: 100% rule-based classification of numeric values against verified reference ranges (low, normal, elevated, critical).
4. **Synergy Evaluator**: Deterministic mapping of multi-marker groups (e.g., Ferritin + Hemoglobin + Serum Iron to evaluate anemia patterns).
5. **Local SLM Summarizer**: Read-only local language model generating an empathetic summary based strictly on the deterministic findings. The model has zero permission to calculate or alter numbers.

### My Responsibility
- Architecture and boundary design between deterministic decision layers and LLM synthesis.
- Developing the PII de-identification layer and testing against adversarial text patterns.
- Building the test suite: **218 automated unit tests** verifying edge cases, boundary values, and unit mismatches.
- Measuring the silent error rate across anonymized clinical test blanks.

### Results & Impact
- **Zero Medical Hallucination**: Model is prevented from modifying or calculating medical numbers by architectural design.
- **Test Integrity**: All 218 unit tests pass consistently; invalid PDFs fail safely with clear audit logs.
- **Privacy Compliance**: All patient identifiers stay within the local execution perimeter.

---

## 3. High-Throughput Multimodal Stream Highlight Extraction

### Problem & Context
Extracting viral highlight clips (kills, clutch plays, teamfights) from 2–8 hour gaming streams is labor-intensive.
- Naive acoustic triggers trigger on screams, laughs, or background music (false positives).
- Ingesting raw video into cloud multimodal models costs ~$40+ per stream and takes hours to upload.

### System Architecture
A two-tier cascade wrapped in a 14-stage DAG engine:
1. **Content-Addressed DAG**: Each stage hashes input tensors, parameters, and code version (SHA-256). Pre-calculated stages (ASR, audio separation) are cached and skipped on re-runs.
2. **Tier 1 (Fast Deterministic ML)**: Demucs audio separation, Whisper ASR with custom phonetic gaming dictionary, Silero VAD, and OpenCV scene-change heuristics generate an interest curve $S(t)$, pruning 98% of the stream.
3. **Tier 2 (Multimodal VLM Arbiter)**: Top 2% candidate windows are sampled for 8 keyframes at 360p (~300 KB total) and passed to a multimodal VLM (Gemini Flash Lite / Qwen 2.5 14B).
4. **Pydantic Grounding Filter**: VLM outputs strict `JudgeVerdict` JSON Schema. An automated filter validates killfeed OCR and first-person POV before triggering hardware NVENC rendering.

### My Responsibility
- Developing the DAG execution engine and the SHA-256 caching mechanism.
- Implementing the phonetic gaming lexicon for Whisper to accurately transcribe gaming jargon.
- Designing the 8-keyframe bundle strategy to eliminate network bandwidth and cloud compute costs.
- Building the Pydantic structured output contract and automated repair loop for the VLM.

### Results & Impact
- **Speed**: Processes a 3.5-hour stream in **28 minutes (6.5x real-time)**.
- **Cost**: **$0.00 compute cost** using local GPU hardware and efficient keyframe sampling.
- **Reliability**: 113 automated unit tests covering DAG stages, VLM keyframe builders, and rendering triggers.

---

## 4. Laboratory Information System (LIS) & Medical Analyzer Protocol Engineering

### Problem & Context
SkyLab operates automated clinical chemistry, hematology, and immunoassay analyzers that transmit real-time diagnostic readings to the "AlfaLab" Laboratory Information System (LIS).
- Analyzers communicate over legacy serial (RS-232) and TCP/IP socket streams using raw protocols (ASTM 1381/1394, HL7).
- Dropped frames, buffer overflows, or malformed packets risk losing patient test results or delaying emergency diagnostics.

### Engineering & Infrastructure Implementation
- **Custom Python Drivers**: Engineered robust async Python modules to read, validate, and parse continuous binary/text byte streams from medical analyzers into structured relational records for AlfaLab LIS.
- **Infrastructure Telemetry**: Designed and deployed enterprise monitoring using **Zabbix + Grafana** across all physical and virtual servers running LIS services, tracking CPU load, disk I/O, database connection pools, and network latencies.
- **Network Segmentation & Security**: Isolated laboratory diagnostic analyzers into a dedicated VLAN, segregated from the public Wi-Fi and general administrative networks, and managed digital signature (EDS/ЭЦП) issuance and cryptographic key life cycles.

### Results & Impact
- Maintained uninterrupted telemetry and data pipelines across clinical analyzers.
- Eliminated packet loss in automated result ingestion into the LIS database.
- Ensured strict network security and compliance with laboratory medical information handling.
