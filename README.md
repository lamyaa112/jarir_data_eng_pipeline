# jarir_data_eng_pipeline
# Modern Data Engineering Pipeline for E-commerce Systems

## Project Overview
An automated, production-grade data engineering pipeline built for a retail scenario (Jarir Bookstore products) using **PySpark**, **Delta Lake**, and **Loguru**. The system acts as a quality gatekeeper by validating incoming data batches against strict data quality dimensions, routing corrupted data to quarantine, and promoting verified data to a reliable Lakehouse for analytics and AI readiness.

## Problem Description
Raw transactional data frequently arrives with quality flaws such as missing values, negative prices, duplicate product IDs, or invalid ratings. Without automated verification, corrupted data leaks directly into production analytics and downstream AI models, resulting in false reporting. This pipeline implements an automated quality gate that prevents corrupted batches from contaminating the production Lakehouse while isolating issues for audit.

## Data Source
Synthetic transactional retail product batches (Jarir Bookstore scenario) covering Electronics, Books, and Stationery.
- **Batch 1 (Faulty):** Contains intentional edge-case errors (missing titles, negative price, duplicate ID, rating > 5.0) to test quarantine isolation.
- **Batch 2 (Clean):** Contains complete and verified records to test successful promotion to Delta Lake.
---

## Architecture & Workflow Diagram

```text
                  Incoming Raw Data Batches (Landing Zone)
                                     │
                                     ▼
                      Data Quality Engine (4 Dimensions)
            [ Completeness | Accuracy | Uniqueness | Validity ]
                                     │
                                     ▼
                            Quality Gate Check
                              /             \
                      FAIL   /               \  PASS
                            ▼                 ▼
                  Quarantine Zone     Transformation Layer
                    (Corrupt CSV)             │
                                              ▼
                                     Delta Lake Storage
                                    (Production Trusted)
                                              │
                                              ▼
                                  Business Analytics Report
                                   & Recommendation Ready
```

## Pipeline Components

**1. Landing Zone (Data Ingestion):**
- Simulates micro-batch landing using CSV storage.
- Tests both edge cases: corrupted batches and fully clean batches.

**2. Data Quality Engine:**
Automatically inspects records and generates a structured JSON report covering the 4 core dimensions:
- Completeness: Ensures required fields (`item_id`, `item_name`) are present with zero nulls.
- Accuracy & Business Rules: Validates that transactional numbers make sense (`price > 0`).
- Uniqueness: Guarantees primary key integrity by rejecting duplicate `item_id` occurrences.
- Validity: Checks that metrics stay within legitimate operational bounds (`rating` between 1.0 and 5.0).

**3. Automated Quality Gate:**
Routes data based on test results (`passed_all_gates` flag):
- PASS: Transforms records, appends them to the Delta Lake storage layer, and flags them as `VERIFIED_CLEAN`.
- FAIL: Automatically isolates corrupted batches to the `quarantine_zone/` directory with timestamps for data audit and review.

**4. Lakehouse (Delta Lake Storage):**
- Uses Delta Lake format for transactional integrity, schema consistency, and append capabilities.

**5. Analytics Output:**
Aggregates clean records directly from the Delta table to provide business KPIs:
- Total product volume by category.
- Average price and rating distributions.
- Filtering high-rated catalog items (rating >= 4.5) for recommendation systems.

## Tech Stack
- Processing Engine: Apache Spark (PySpark)
- Table / Storage Layer: Delta Lake
- Logging & Tracking: Loguru
- Ingestion / Utilities: Pandas, OS, JSON, Datetime

## Results
- **Automated Validation:** 100% of corrupted records were detected and blocked by the Quality Gate.
- **Zero Production Contamination:** Only verified clean batches were committed to the Delta Lake storage layer.
- **Auditability:** Corrupted data was safely isolated into the quarantine zone with timestamps and execution logs via Loguru.

## How to Run the Project
1. Open the project notebook (`jarir_data_eng_pipeline.ipynb`) in Google Colab or Jupyter Notebook.
2. Install the necessary dependencies:
   ```bash
   pip install pyspark delta-spark loguru
3. Run all notebook cells sequentially from top to bottom.
4. Inspect the generated execution log (`pipeline_execution.log`), the quarantined files in `data/quarantine_zone/`, and the verified Delta table in `data/delta/jarir_production/`.

## Future Improvements
- Semantic RAG Search (ChromaDB): Implement vector search using sentence-transformers and ChromaDB to allow customers to query products using natural conversational language.
- Real-Time Streaming: Upgrade ingestion from batch files to continuous event streaming using Kafka and Spark Structured Streaming.




- SDAIA Academy GitHub Repository Link
---

**Acknowledgements**
This project was developed as part of the **Modern Data Engineering for AI Systems** training program delivered by **SDAIA Academy**.

[SDAIA Academy on GitHub](https://github.com/SDAIAAcademy)
