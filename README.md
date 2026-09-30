# jarir_data_eng_pipeline
# Modern Data Engineering Pipeline for E-commerce Systems

## Project Overview
An automated, production-grade data engineering pipeline built for a retail scenario (Jarir Bookstore products) using **PySpark**, **Delta Lake**, and **Loguru**. The system acts as a quality gatekeeper by validating incoming data batches against strict data quality dimensions, routing corrupted data to quarantine, and promoting verified data to a reliable Lakehouse for analytics and AI readiness.

## Architecture & Workflow Diagram
```
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

  
