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

## 🛠️ Technical Toolkit

| Category | Technologies & Frameworks |
| :--- | :--- |
| **Streaming & Message Brokers** | Apache Kafka, Kafka Streams, Redpanda, Debezium, RabbitMQ |
| **Data Warehousing & Query Engines** | DuckDB, Apache Parquet, PyArrow, PostgreSQL, ClickHouse, dbt |
| **Search & Information Retrieval** | BM25Okapi, BGE Embeddings, Cross-Encoders, Reciprocal Rank Fusion, Vector Databases (Qdrant, Redis) |
| **Data Governance & Reliability** | OpenLineage, Marquez, Hypothesis (Property-Based Testing), Pydantic Contracts, GDPR Article 17 Purges |
| **Languages & Tooling** | Python, Java 21, SQL, TypeScript / Node.js, Docker, Docker Compose, Linux, Git, GitHub Actions, Maven, Ruff |

---

## 📬 Contact & Links

- **GitHub:** [@Yprnv7](https://github.com/Yprnv7)
- **LinkedIn:** [linkedin.com/in/pranavyeleswarapu](https://www.linkedin.com/in/pranavyeleswarapu)
- **Email:** [yeleswarapupranav@gmail.com](mailto:yeleswarapupranav@gmail.com)
