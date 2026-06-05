# Azure End-to-End Data Engineering Pipeline

A production-style data engineering pipeline built entirely on Microsoft Azure — ingesting raw files via REST API, transforming with PySpark on Databricks, warehousing in Synapse Analytics, and visualising in Power BI. Follows the **Medallion Architecture (Bronze → Silver → Gold)**.

---

## What this project does

Raw AdventureWorks CSV files are pulled from a GitHub REST API into **Azure Data Lake Gen2** (Bronze). **Azure Databricks** cleans and enriches the data using PySpark (Silver). **Azure Synapse Analytics** hosts the final fact and dimension models (Gold). **Power BI** connects via DirectQuery for live dashboards on sales, products, customers, and returns.

---

## Architecture

```
GitHub REST API (AdventureWorks CSVs)
      ↓
Azure Data Factory (parameterized Copy Activity)
      ↓
ADLS Gen2 — Bronze Layer (raw files as received)
      ↓
Azure Databricks — Silver Layer (PySpark cleaning, schema enforcement, Delta format)
      ↓
Azure Synapse Analytics — Gold Layer (fact & dimension tables, OPENROWSET external tables)
      ↓
Power BI Dashboard (DirectQuery → Sales, Products, Customers, Returns)
```

---

## Tech Stack

| Tool | Role |
|---|---|
| Azure Data Factory | Pipeline orchestration, parameterized ingestion |
| Azure Data Lake Gen2 | Layered storage (Bronze / Silver / Gold) |
| Azure Databricks + PySpark | Scalable data transformation |
| Azure Synapse Analytics | SQL data warehouse, external tables |
| Power BI | Reporting and KPI dashboards |
| GitHub REST API | Data source |

---

## Dataset

**AdventureWorks (2015–2017)** — a retail dataset covering:

- Sales transactions
- Product catalogue
- Customer records
- Returns data
- Calendar dimension

Source: Kaggle AdventureWorks / GitHub-hosted CSVs

---

## Pipeline Breakdown

### Bronze — Raw Ingestion
- ADF Copy Activity pulls CSVs from GitHub API
- Files stored exactly as received in `/bronze/<dataset>/2026/01/15/`
- No transformation — preserves source truth

### Silver — Transformation
- Databricks notebooks clean, validate, and join raw tables
- PySpark handles null removal, type casting, schema enforcement
- Output written as optimised Delta files to `/silver/`

### Gold — Warehousing
- Synapse external tables reference Silver Delta data via `OPENROWSET`
- Fact and dimension models built for BI consumption
- Stored in `/gold/` container

### Reporting
- Power BI connects to Synapse via DirectQuery
- Dashboards: Sales performance, Product revenue, Customer segments, Return rates

---

## Setup Guide

### Prerequisites
- Azure account (free tier + $200 credits sufficient)
- Power BI Desktop (optional for local dashboard editing)

### Steps

**1. Create Azure Resources**
```
Resource Group → ADLS Gen2 → ADF → Databricks Workspace → Synapse Workspace
```

**2. Configure Storage Containers**
```
bronze/   silver/   gold/
```

**3. Build ADF Ingestion Pipeline**
- Create Linked Services: HTTP (GitHub) + ADLS Gen2
- Build parameterized pipeline with Copy Activity
- Set dynamic folder paths by dataset name and date

**4. Run Databricks Notebooks**
```python
# Silver transformation example
df = spark.read.format("parquet").load("/mnt/bronze/sales/")
df_clean = df.dropna().withColumnRenamed("SalesAmount", "revenue")
df_clean.write.format("delta").save("/mnt/silver/sales/")
```

**5. Load into Synapse**
```sql
CREATE EXTERNAL TABLE gold.sales
WITH (LOCATION = 'gold/sales/', DATA_SOURCE = silver_storage, ...)
AS SELECT * FROM OPENROWSET(...)
```

**6. Connect Power BI**
- Open Power BI Desktop → Get Data → Azure Synapse Analytics
- Use DirectQuery mode for live refresh

---

## Key Features

- **Parameterized pipelines** — one ADF pipeline handles all datasets dynamically
- **Delta Lake format** — ACID transactions, time travel, efficient reads
- **External tables in Synapse** — no data duplication, query directly from lake
- **DirectQuery in Power BI** — always reflects latest Gold layer data

---

## Future Enhancements

- Add Great Expectations for data quality checks
- Automate with ADF triggers (schedule / event-based)
- Deploy Databricks jobs via CI/CD (GitHub Actions)
- Add streaming ingestion via Azure Event Hubs

---


📊 End-to-End Architecture Diagram
<img width="1529" height="1079" alt="image" src="https://github.com/user-attachments/assets/9c37f12a-4866-4705-8e1c-bd449ee891d4" />


## Author

**Sathwik Kotian** — [LinkedIn](https://www.linkedin.com/in/sathwik-kotian-bb6791265/) · [GitHub](https://github.com/sathwikkotyan)

