# Hi, I'm Pranav Yeleswarapu 👋

**Data Engineer & AI Systems Engineer**  
Hyderabad, India 
[LinkedIn](https://www.linkedin.com/in/pranavyeleswarapu) · [GitHub](https://github.com/Yprnv7) · [Email](mailto:yeleswarapupranav@gmail.com)

---

I build low-latency data pipelines, streaming architectures, and grounded retrieval systems. My engineering focus centers on **transactional consistency**, **streaming Change Data Capture (CDC)**, **serverless columnar lakehouses (DuckDB / Parquet)**, and **empirically evaluated hybrid search (RAG)**.

Every project in my portfolio is accompanied by reproducible benchmark scripts, unit and property-based test suites, and honest empirical trade-off evaluations.

---

## 🚀 Featured Engineering Projects

### 1. [NeuroFlow Hybrid RAG](https://github.com/Yprnv7/neuroflow-hybrid-rag)
*Flagship AI Engineering Project*

Hybrid search and retrieval engine combining sparse lexical matching, dense embeddings, Reciprocal Rank Fusion, cross-encoder reranking, and a calibrated semantic cache for technical and regulatory retrieval.

- **Dual-Stage Hybrid Retrieval:** Fuses sparse **BM25Okapi** lexical tokens with dense `BAAI/bge-small-en-v1.5` embeddings using **Reciprocal Rank Fusion (RRF, $k=60$)** and convex combination fusion.
- **Empirical BEIR SciFact Benchmark:** Evaluated zero-shot over 5,183 scientific passages and 150 test queries. Hybrid RRF achieves **0.7392 NDCG@10** and **0.9767 Recall@100** (improving over standalone BM25 at 0.6872 and Dense at 0.7258).
- **Cross-Encoder Candidate Truncation:** Benchmarks show reranking Top-25 candidates with `ms-marco-MiniLM-L-6-v2` balances contextual precision against CPU serving latency (~443 ms p50).
- **Calibrated Semantic Cache & GDPR:** Cosine similarity threshold calibrated at $\tau = 0.92$ to eliminate adversarial false positives, backed by an atomic `DELETE /v1/tenants/{tenant_id}/forget` endpoint for GDPR Article 17 right-to-erasure sweeps.
- **Stack:** Python, FastAPI, Sentence-Transformers, BM25, BEIR, Pytest (89% coverage), Ruff, Docker.

---

### 2. [PostgreSQL CDC Lakehouse](https://github.com/Yprnv7/postgres-cdc-lakehouse)
*Flagship Data Engineering Project*

Streaming Change Data Capture pipeline capturing row-level PostgreSQL WAL mutations into DuckDB with SCD Type 2 dimension versioning, schema contract validation, and Dead-Letter Queue routing.

- **WAL-Level Streaming Ingestion:** Decodes PostgreSQL transaction logs via **Debezium** (`pgoutput`) and **Redpanda**, avoiding analytical query contention on operational OLTP databases.
- **Slowly Changing Dimension Type 2 (SCD2):** Upfront 16-character SHA-256 diff fingerprinting prevents redundant historical rows on no-op mutations.
- **Property-Based Verification (Hypothesis):** `tests/test_scd2_properties.py` runs randomized mutation sequences asserting temporal invariants (single `is_current`, valid temporal windows, zero chronological overlap).
- **Measured Ingestion Benchmark:** Sustains **1,413.5 events/sec** single-threaded with **0.683 ms median commit-to-lakehouse latency** (p95 = 0.951 ms).
- **Resilience & Lineage:** Isolates malformed records to an operational Dead-Letter Queue (DLQ) with zero worker crashes; emits OpenLineage 1.0.5 `RunEvent` payloads to Marquez.
- **Stack:** Python, PostgreSQL, Debezium, Redpanda / Kafka, DuckDB, dbt, Hypothesis, Pydantic, Docker Compose.

---

### 3. [Startup Leads Live](https://github.com/Yprnv7/startup-leads-live)
*Applied Labor Market Intelligence Pipeline*

Automated labor market data pipeline that discovers verified remote engineering jobs and internships, validates corporate decision-maker contacts, and invalidates expired postings.

- **First-Party ATS Discovery:** Polls primary Applicant Tracking Systems (Ashby, Greenhouse, Lever) via direct API feeds, eliminating scraping fragility and phantom listings.
- **Contact & Email Verification:** Resolves DNS MX records and executes SMTP handshakes via ZeroBounce/Prospeo to ensure 100% of published corporate mailboxes are active and deliverable.
- **Data Minimization & Compliance:** Built strictly under **GDPR Article 6(1)(f)** and **India DPDP Act 2023** frameworks. Personal mobile scraping is permanently disabled in favor of verified business emails and corporate switchboards.
- **Scale:** Tracks **250+ startups**, **600+ verified engineering roles**, and updates hourly via automated serverless workflows.
- **Stack:** Node.js, Python, Vanilla JavaScript, GitHub Actions, ZeroBounce, DNS Resolver, GitHub Pages.

---

### 4. [KStream-Lake](https://github.com/Yprnv7/kstream-lake)
*Distributed Event Streaming & Columnar OLAP*

Event streaming and columnar lakehouse engine combining Apache Kafka Streams stateful processing, rolling Z-score statistical anomaly detection, PyArrow Parquet serialization, and embedded DuckDB OLAP analytics.

- **Stateful Kafka Streams Topology:** Implemented in **Java 21** with exactly-once processing (`processing.guarantee=exactly_once_v2`) and RocksDB tumbling window state stores.
- **Dynamic Rolling Z-Score Anomaly Detector:** Computes windowed mean and running sum of squares in $O(1)$ memory, flagging transactions deviating by $\ge 3$ standard deviations ($Z \ge 3.0$). Unit-tested via `TopologyTestDriver`.
- **Columnar Lakehouse Ingestion:** PyArrow micro-batch serializer flushing partitioned Apache Parquet files at **226,000+ records/sec**, queried serverlessly via DuckDB in under 6 milliseconds.
- **Stack:** Java 21, Apache Kafka Streams, RocksDB, Python, PyArrow, DuckDB, JUnit 5, Maven.

---

### 5. [Self-Healing Text-to-SQL](https://github.com/Yprnv7/self-healing-text-to-sql)
*AST-Guarded AI Database Copilot & Self-Correction Engine*

Schema-aware Text-to-SQL execution engine that intercepts SQL queries, enforces deterministic AST security guards (sqlglot), and automatically repairs execution and schema errors through a bounded feedback loop.

- **Deterministic AST Guard (`sqlglot`):** Blocks non-read statements (DDL/DML mutations), stacked injections, and unauthorized functions (`pg_sleep`). Automatically intercepts restricted PII columns (`raw_email`, `phone`) and rewrites them to pseudonymized hashes under GDPR / India DPDP guidelines.
- **Mandatory Fact-Table Partition Enforcement:** Enforces date/timestamp boundaries on partitioned tables (`fct_orders`), preventing accidental full-lakehouse scans and runaway cloud compute costs.
- **In-Database Error Diagnostics Loop:** Leverages DuckDB compiler error diagnostics (`Candidate bindings:`, `Did you mean "..."?`) for zero-shot query self-healing across up to 3 bounded attempts without external LLM re-prompting.
- **BIRD-Style Enterprise Benchmark:** Evaluated across 50 analytical queries spanning Simple, Moderate, and Complex tiers. Boosts overall execution accuracy from **82.98% to 100.0%**, improves Complex query accuracy by **+57.2%**, blocks **100.0%** of malicious injections, and runs with **2.11 ms median (p50) latency** on Apple Silicon.
- **Stack:** Python, DuckDB, sqlglot, FastAPI, Plotly, Pytest (93% coverage), Ruff, Mypy, Docker Compose.

---

### 6. [LLM Evaluation & Observability](https://github.com/Yprnv7/llm-eval-observability)
*Self-Hosted LLM Tracing, Prompt Diffing & Human-Calibrated Judge Platform*

Self-hosted alternative to Langfuse/Braintrust featuring an asynchronous Python `@observe` SDK, automated judge calibration using Cohen's Kappa, and CI regression gating.

- **Zero-Overhead Asynchronous SDK:** In-process non-blocking ring buffer queue decouples span shipping from the application request thread, adding only **0.015 ms (14.8 µs p95)** instrumentation overhead.
- **Human-Calibrated Judge ($\kappa$):** Evaluates automated evaluators against 100 hand-graded enterprise RAG and SQL ground-truth traces, computing **Cohen's Kappa ($\kappa = 0.9480$, Almost Perfect Agreement)**, 2x2 confusion matrices, and per-criterion alignment.
- **CI Regression Gate:** Automated GitHub Actions quality gate that fails pull requests if evaluation scores drop by $>2\%$ or inference costs rise by $>15\%$.
- **EU AI Act Article 12 Compliance:** Cryptographic audit trail recording model identifiers, prompt SHA-256 hashes, generation parameters, and HMAC digital signatures for every production run.
- **Stack:** Python, FastAPI, DuckDB, NumPy, SciPy, Pytest (85.5% coverage), Hypothesis, Ruff, Mypy, Docker Compose.

---

### 7. [Streaming Iceberg Lakehouse](https://github.com/Yprnv7/streaming-iceberg-lakehouse)
*Streaming Table-Format Lakehouse with Watermarking, Compaction & Replay*

Streaming table-format lakehouse engine handling 10-minute event-time watermarking, late-arriving event dead-letter routing, transactional schema evolution, Dagster small-file compaction, and 24-hour deterministic raw stream replay.

- **Event-Time Watermarking:** Tracks monotonically advancing watermarks (`max(event_time) - 10m`), preserving temporal ordering and routing late-arriving trade records to DLQ storage with zero loss.
- **Transactional Schema Evolution:** Full backward-compatible additive evolution (V1 $\rightarrow$ V2 adding `exchange_fee` and `trade_tier`), queryable across disparate physical Parquet generations seamlessly with DuckDB columnar unions.
- **Small-File Compaction:** Background compaction rewriting fragmented 25 micro-batch Parquet files into single contiguous ZSTD-compressed data files, yielding a **70.2% analytical query speedup** (1.673 ms $\rightarrow$ 0.499 ms).
- **GDPR Article 17 Purge & Replay Parity:** In-place metadata rewriting to purge user identifiers across snapshots without full-table rewrites; 24-hour event replay achieves **100% SHA-256 cryptographic state checksum parity**.
- **Measured Ingestion Benchmark:** Sustains **15,231 events/sec** with **0.54 ms median commit latency** on Apple Silicon hardware.
- **Stack:** Python, Apache Iceberg format, DuckDB, PyArrow, Dagster, FastAPI, Hypothesis, Pytest (98% coverage), Ruff, Mypy, Docker Compose.

---

### 8. [Multimodal Document Intelligence](https://github.com/Yprnv7/multimodal-doc-intelligence)
*Layout-Aware Document Parsing, Steganography Defense & Spatial Grounded RAG*

Layout-aware document intelligence engine that reconstructs multi-column reading orders, extracts structured tables into Markdown grids, intercepts physical and steganographic prompt injections, and generates pixel-grounded bounding-box citations.

- **Adversarial Prompt Injection Shield:** Intercepts micro-fonts ($< 2.0\text{pt}$), zero-opacity spans ($< 0.05$), low-contrast steganography via WCAG relative luminance ($< 1.15:1$), and system prompt overrides with **100.0% detection accuracy** at **7.56 µs per span**.
- **Multi-Column Reading Order:** Resolves reading flow across complex multi-column documents and extracts structured tables preserving cell coordinate bounds.
- **Pixel-Grounded Citations:** Spatial BM25 inverted index generates exact bounding-box coordinates `[p{page}:({ymin},{xmin})-({ymax},{xmax})]` for verifiable audit trails.
- **Measured Performance Benchmark:** Achieves **92,887 spans/sec (1,857 pages/sec)** parsing throughput and **1.942 ms median (p50) retrieval latency** on local Apple Silicon hardware.
- **Stack:** Python, FastAPI, Pillow, Pydantic, Hypothesis, Pytest (94% coverage), Ruff, Mypy, Docker Compose.

---

### 9. [Sandboxed Code Analyst Agent](https://github.com/Yprnv7/sandboxed-code-analyst-agent)
*Stateful Data Science Agent, AST Security Sandbox & Self-Healing Execution*

Autonomous data analyst agent that generates, audits, and executes Python / DuckDB data analysis in an isolated micro-sandbox with deterministic AST security guards, resource circuit breakers, and bounded self-repair.

- **Deterministic AST Security Guard:** Statically audits Python syntax trees before execution, blocking forbidden imports (`os`, `sys`, `subprocess`, `socket`, `shutil`), builtins (`eval`, `exec`, `globals`), and reflection dunders (`__subclasses__`, `__mro__`) with **100.0% interception accuracy** in **13.21 µs median latency**.
- **Micro-Sandbox Execution:** Runs code inside an in-process restricted namespace with execution timeouts, output capturing, and **0.057 ms median startup overhead**.
- **Stateful REPL Multi-Turn Sessions:** Preserves analytical variables, DataFrames, and models across conversational turns without memory leakage or cold-start serialization overhead.
- **Automated Self-Healing Error Reflection:** Diagnoses compiler tracebacks (`NameError`, `ZeroDivisionError`, table syntax) and applies automated code repairs across up to 3 bounded iterations with a **100.0% recovery rate** on common defects.
- **Stack:** Python, FastAPI, DuckDB, NumPy, Pydantic, Hypothesis, Pytest (94% coverage), Ruff, Mypy, Docker Compose.

---

### 10. [AI Guardrails & PII Firewall](https://github.com/Yprnv7/ai-guardrails-pii-firewall)
*Ultra-Low-Latency Security Proxy, GDPR Pseudonymization & Jailbreak Firewall*

Reverse proxy and security firewall for LLM applications that intercepts prompt injections, redacts sensitive personal data under GDPR & India DPDP guidelines using reversible pseudonymization, and normalizes Unicode homoglyphs.

- **Reversible Token Pseudonymization:** Replaces structured PII (Emails, Irish PPSNs, IBANs, Phone numbers, IPs, Credit Cards with Luhn validation) with contextual tokens (`<EMAIL_1>`, `<IBAN_1>`), restoring cleartext upon response generation with **100.0% exact-match accuracy**.
- **Microsecond In-Line Processing:** Operates at **13.71 µs median latency** (**54,473 docs/sec** throughput), enabling transparent proxying without degrading end-user experience.
- **Adversarial Jailbreak & Homoglyph Normalizer:** Applies Unicode NFKC normalization and maps Cyrillic/Greek lookalikes to Latin characters, achieving a **100.0% interception rate** across obfuscated prompt injection and roleplay attack vectors.
- **Compliance & Rate Limiting:** Generates tamper-evident HMAC-SHA256 audit digests for GDPR Article 17 proof-of-erasure verification; token-bucket limiter blocks denial-of-wallet scraping attacks.
- **Stack:** Python, FastAPI, Pydantic, Hypothesis, Pytest (98% coverage), Ruff, Mypy, Docker Compose.

---

### 11. [Data Observability & SQL Lineage](https://github.com/Yprnv7/data-observability-sql-lineage)
*Column-Level SQL Lineage Engine, Statistical Observability & Blast Radius Analysis*

Automated data observability and column-level SQL lineage engine that parses transformation DAGs using `sqlglot`, detects statistical freshness/volume/drift anomalies, and performs downstream blast radius impact analysis.

- **Column-Level Lineage DAG Parser:** Evaluates SQL expressions across CTEs, joins, and aggregations using `sqlglot`, mapping exact column provenance at **1,056.2 queries/sec** parsing throughput.
- **Blast Radius Impact Analysis:** Forward BFS graph walk traverses 20-hop multi-stage data pipelines in **5.08 µs median latency**, pinpointing all affected downstream tables and executive dashboards.
- **Statistical Anomaly Monitors:** Combines $O(1)$ SLA freshness alerts, dynamic 3-sigma ($Z \ge 3.0$) row count volume monitors, and dual-gated distribution drift testing (Kolmogorov-Smirnov test + 10-decile PSI).
- **Incident Dispatcher:** Generates correlated incident payloads linking anomalous source columns to downstream business impacts for automated triage.
- **Stack:** Python, sqlglot, FastAPI, NumPy, SciPy, Pydantic, Hypothesis, Pytest (94% coverage), Ruff, Mypy, Docker Compose.

---

### 12. [Go Multi-Model AI Gateway](https://github.com/Yprnv7/go-multi-model-ai-gateway)
*High-Throughput Concurrent AI Proxy, Circuit Breaking & Model Failover Engine*

Ultra-low-latency AI gateway and resilience proxy implemented in Go 1.23 standard library featuring OpenAI-compatible routing (`/v1/chat/completions`), multi-model failover cascades, thread-safe 3-state circuit breakers, and tenant token-bucket rate limiting.

- **Microsecond Gateway Routing:** Achieves **1.92 Million req/sec** raw routing throughput with a **0.83 µs median (p50) latency** and **100.00 µs p95 latency** across 50 concurrent worker goroutines.
- **Automated Failover Cascade:** Transparently re-routes in-flight requests across Google Gemini, Anthropic Claude, and OpenAI GPT-4o upon upstream 5xx errors or timeouts with **0.38 µs failover hop overhead**.
- **3-State Circuit Breaking:** Enforces thread-safe `CLOSED` $\to$ `OPEN` $\to$ `HALF_OPEN` state transitions with cool-down timers to protect degraded providers from thundering herd crashes.
- **Tenant Isolation & SSE Streaming:** Leaky token-bucket rate limiter prevents noisy-neighbor resource starvation; native `http.Flusher` integration streams Server-Sent Events (SSE) with zero buffering delay.
- **Stack:** Go 1.23, net/http, sync/atomic, Docker, Docker Compose, GitHub Actions CI (96.6% coverage, zero race conditions).

---

### 13. [Real-Time Feature Store & MLOps Platform](https://github.com/Yprnv7/realtime-feast-mlops-platform)
*Real-Time Feature Store, Point-in-Time Correct Joins & Distribution Drift Monitoring*

Enterprise-grade feature store and MLOps platform delivering sub-microsecond online inference serving, dual-write streaming ingestion, point-in-time correct historical joins in DuckDB with zero training data leakage, and statistical drift monitoring.

- **Sub-Microsecond Online Serving:** In-memory online feature store achieves **0.88 µs median (p50) lookup latency** (p95 = 0.96 µs) across 10,000 multi-feature entity vectors.
- **Point-in-Time Correct Joins (DuckDB):** Temporal windowed ASOF joins eliminate lookahead bias / data leakage, joining 1,000 training observations across 20,000 historical mutations in **203.28 ms** with **0.0% data leakage violations** (proven via Hypothesis property testing).
- **Dual-Write Streaming Pipeline:** Synchronously updates low-latency online memory and appends to the offline temporal log at **5,170.7 events/sec** throughput.
- **Statistical Feature Drift & TTL Eviction:** Evaluates 10,000 production samples in **9.94 ms** using two-sample **Kolmogorov-Smirnov tests** and **Population Stability Index (PSI)**; evicts stale features during upstream pipeline lags to protect model safety.
- **Stack:** Python 3.11, DuckDB, NumPy, SciPy, FastAPI, Pydantic, Hypothesis, Pytest (97% coverage), Ruff, Mypy, Docker Compose.

---

## 🛠️ Technical Toolkit

| Category | Technologies & Frameworks |
| :--- | :--- |
| **Streaming & Message Brokers** | Apache Kafka, Kafka Streams, Redpanda, Debezium, RabbitMQ |
| **Data Warehousing & Query Engines** | DuckDB, Apache Parquet, PyArrow, PostgreSQL, ClickHouse, dbt |
| **Search & Information Retrieval** | BM25Okapi, BGE Embeddings, Cross-Encoders, Reciprocal Rank Fusion, Vector Databases (Qdrant, Redis) |
| **Data Governance & Reliability** | OpenLineage, Marquez, Hypothesis (Property-Based Testing), Pydantic Contracts, GDPR Article 17 Purges |
| **Languages & Tooling** | Python, Go 1.23, Java 21, SQL, TypeScript / Node.js, Docker, Docker Compose, Linux, Git, GitHub Actions, Maven, Ruff |

---

## 📬 Contact & Links

- **GitHub:** [@Yprnv7](https://github.com/Yprnv7)
- **LinkedIn:** [linkedin.com/in/pranavyeleswarapu](https://www.linkedin.com/in/pranavyeleswarapu)
- **Email:** [yeleswarapupranav@gmail.com](mailto:yeleswarapupranav@gmail.com)
