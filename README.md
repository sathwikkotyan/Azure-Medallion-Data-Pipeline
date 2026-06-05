# Real-Time Supply Chain Streaming Analytics

An end-to-end data engineering pipeline that simulates a real-world supply chain system — processing live order events through Kafka, applying Medallion Architecture on Databricks, orchestrating with Airflow, and surfacing KPIs in Power BI.

---

## What this project does

Raw supply chain orders are generated and streamed through **Apache Kafka** at ~20 events/sec. A consumer picks them up and pushes data through three structured layers — Bronze (raw), Silver (cleaned), Gold (aggregated business metrics) — all orchestrated by **Apache Airflow** and stored on **Databricks Delta Lake**.

Final output: a **Power BI dashboard** showing revenue by product, orders by warehouse, and fulfilment status in near real-time.

---

## Architecture

```
CSV Data Generator
      ↓
Kafka Producer → Kafka Topic (5,000+ order events)
      ↓
Kafka Consumer → Bronze Layer (raw ingestion)
      ↓
         Silver Layer (cleaning, null handling, standardization)
      ↓
         Gold Layer (revenue aggregations, KPIs)
      ↓
Airflow DAGs (orchestration + scheduling + retry logic)
      ↓
Power BI Dashboard (business reporting)
```

---

## Tech Stack

| Layer | Tool |
|---|---|
| Streaming | Apache Kafka |
| Processing | Python, PySpark |
| Storage | Databricks Delta Lake |
| Orchestration | Apache Airflow |
| Visualization | Power BI |
| Environment | Ubuntu Linux |

---

## Medallion Architecture

**Bronze** — Raw Kafka consumer output. Unprocessed, stored as single source of truth.

**Silver** — Cleaned dataset. Null values handled, formats standardized, schema enforced.

**Gold** — Business-ready aggregations: Revenue by Product, Revenue by Warehouse, Orders by Status, Total Units Sold.

---

## Airflow Orchestration

Four DAGs manage the pipeline:

- `bronze_dag.py` — ingests from Kafka consumer output
- `silver_dag.py` — runs cleaning transformations
- `gold_dag.py` — runs aggregations
- `master_pipeline_dag.py` — chains all three with dependency management, retry logic, and daily scheduling

---

## Project Structure

```
SUPPLY_CHAIN_PROJECT/
├── Data_generation/       # synthetic order event generator
├── kafka_producer/        # publishes events to Kafka topic
├── kafka_consumer/        # consumes and writes to Bronze
├── airflow_dags/
│   ├── bronze_dag.py
│   ├── silver_dag.py
│   ├── gold_dag.py
│   └── master_pipeline_dag.py
└── data/
    ├── bronze/
    ├── silver/
    └── gold/
```

---

## How to Run

```bash
# 1. Activate virtual environment
python -m venv venv && source venv/bin/activate

# 2. Start Kafka (Zookeeper + Broker)
bin/zookeeper-server-start.sh config/zookeeper.properties
bin/kafka-server-start.sh config/server.properties

# 3. Run producer and consumer
python kafka_producer/producer.py
python kafka_consumer/consumer.py

# 4. Start Airflow
airflow webserver -p 8080
airflow scheduler

# 5. Trigger master DAG from Airflow UI
# 6. Connect Power BI to Gold layer output
```

---

## Key Challenges Solved

- Kafka offset handling and consumer group management
- Airflow DAG folder configuration and environment isolation
- Python version compatibility across pipeline components
- Structured debugging approach using virtual environments and clear layer separation

---

## Future Enhancements

- Deploy on Azure / AWS
- Dockerize the full pipeline
- Add data quality checks (Great Expectations)
- CI/CD integration
- FastAPI layer for external data access

---

## Author

**Sathwik Kotian** — [LinkedIn](https://www.linkedin.com/in/sathwik-kotian-bb6791265/) · [GitHub](https://github.com/sathwikkotyan)



📊 End-to-End Architecture Diagram
<img width="1529" height="1079" alt="image" src="https://github.com/user-attachments/assets/9c37f12a-4866-4705-8e1c-bd449ee891d4" />

