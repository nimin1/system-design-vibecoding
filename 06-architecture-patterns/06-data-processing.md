# Data Processing

Moving and transforming data at scale batch, stream, and everything in between.

---

## Types of Data Processing

### Batch Processing

Process large volumes of data in discrete chunks.

```
Collect data over time → Process all at once → Output results
Daily log processing, weekly reports, model training
```

**Characteristics:**
- High latency (hours to days)
- High throughput
- Complete data for period
- Easier failure recovery (rerun batch)

### Stream Processing

Process data as it arrives.

```
Events arrive continuously → Process immediately → Results in real-time
Real-time dashboards, fraud detection, live recommendations
```

**Characteristics:**
- Low latency (milliseconds to seconds)
- Continuous processing
- Incomplete data (always more coming)
- Complex failure handling

### Micro-Batch

Blend of batch and stream. Process small batches frequently.

```
Collect events for 10 seconds → Process batch → Repeat
```

Easier than true streaming, lower latency than daily batch.

---

## ETL and ELT

### ETL (Extract-Transform-Load)

Traditional approach. Transform data before loading to destination.

```
Source → Extract → Transform → Load → Destination
```

**Use when:**
- Target system can't handle raw data volume
- Need to clean/standardize before loading
- Transformation is well-defined

### ELT (Extract-Load-Transform)

Modern approach. Load raw data, transform in destination.

```
Source → Extract → Load → Transform → Ready for use
                    ↓
              Raw data stored
```

**Use when:**
- Destination can handle raw data (data lakes, warehouses)
- Want to preserve raw data
- Transformations may change over time

**ELT dominates modern data architectures.** Storage is cheap. Keep raw data.

---

## Batch Processing Frameworks

### Apache Spark

The most widely used distributed processing framework.

**Core concepts:**
- **Driver:** Coordinates the work
- **Executors:** Workers that process data
- **RDD/DataFrame:** Distributed data structures

**Use for:**
- Large-scale data transformation
- Machine learning (Spark MLlib)
- SQL analytics (Spark SQL)

**Scale:** Petabytes, thousands of nodes.

### Apache Hadoop (MapReduce)

Original distributed processing framework.

```
Map: Process each record → key-value pairs
Shuffle: Group by key
Reduce: Aggregate each key's values
```

**Status:** Legacy. Spark has largely replaced it for processing. HDFS still used for storage.

### Data Warehouses

For analytics on structured data.

| Warehouse | Notes |
|-----------|-------|
| Snowflake | Cloud-native, separates storage and compute |
| BigQuery | Google, serverless, pay per query |
| Redshift | AWS, columnar, cluster-based |
| Databricks | Unified analytics, built on Spark |

---

## Stream Processing Frameworks

### Apache Kafka + Kafka Streams

Kafka for ingestion, Kafka Streams for processing.

**Use when:** Kafka-centric architecture, simpler processing.

### Apache Flink

True stream processing. Low latency, exactly-once.

**Use when:** Complex event processing, low latency requirements.

### Apache Spark Streaming

Micro-batch on Spark. Unified batch and stream.

**Use when:** Already using Spark, latency of seconds is acceptable.

### Cloud Services

| Service | Provider |
|---------|----------|
| Kinesis | AWS |
| Pub/Sub + Dataflow | GCP |
| Event Hubs + Stream Analytics | Azure |

---

## Data Lakes

Central repository for all data - structured and unstructured.

### Characteristics

- Store everything (logs, events, documents, images)
- Schema-on-read (define structure when querying)
- Use object storage (S3, GCS)
- Query with Spark, Presto, Athena

### Data Lake Architecture

```
   ┌──────────────────────────────────────────────┐
   │              Data Sources                    │
   │  (Apps, Logs, Events, External)              │
   └──────────────────────────────────────────────┘
                        │
                        ▼
   ┌──────────────────────────────────────────────┐
   │                Ingestion                      │
   │  (Kafka, Kinesis, Batch ETL)                 │
   └──────────────────────────────────────────────┘
                        │
                        ▼
   ┌──────────────────────────────────────────────┐
   │               Data Lake                       │
   │           (S3, GCS, ADLS)                    │
   │  ┌──────────────────────────────────────┐   │
   │  │ Raw │ Processed │ Curated │ Sandbox  │   │
   │  └──────────────────────────────────────┘   │
   └──────────────────────────────────────────────┘
                        │
              ┌─────────┼─────────┐
              ▼         ▼         ▼
         ┌─────────┐ ┌─────────┐ ┌─────────┐
         │Analytics│ │   ML    │ │ Reports │
         └─────────┘ └─────────┘ └─────────┘
```

### Data Lake Zones

**Raw (Bronze):** Data as received. Immutable.

**Processed (Silver):** Cleaned, typed, deduplicated.

**Curated (Gold):** Business-ready aggregates and features.

---

## Data Pipelines

### What Is a Pipeline?

Sequence of processing steps. Output of one is input to next.

```
Source → Step 1 → Step 2 → Step 3 → Destination
```

### Orchestration

Coordinate when jobs run, handle dependencies.

**Tools:**
- **Apache Airflow:** Define pipelines as DAGs in Python. Very popular.
- **Prefect:** Modern Airflow alternative.
- **Dagster:** Data-aware orchestration.
- **AWS Step Functions:** Managed, serverless.

### How Orchestration Works

Define pipelines as directed acyclic graphs (DAGs). Each node is a task, edges are dependencies. Scheduler runs tasks in order, retries failures, handles parallelism.

**Key concepts:**
- Tasks have dependencies (extract → transform → load)
- Scheduler manages execution order
- Built-in retry and failure handling
- Backfill capability for historical data

---

## Change Data Capture (CDC)

Capture changes from databases in real-time.

### How It Works

```
Database transaction log → CDC tool → Stream (Kafka)
```

Every insert, update, delete captured as an event.

### Use Cases

- Real-time replication
- Event sourcing from legacy systems
- Cache invalidation
- Analytics on live data

### Tools

- **Debezium:** Open source, Kafka-based
- **AWS DMS:** Managed migration/replication
- **Fivetran, Airbyte:** Managed ELT with CDC

---

## Data Quality

Garbage in, garbage out.

### Quality Dimensions

- **Completeness:** Are required fields present?
- **Accuracy:** Is the data correct?
- **Consistency:** Do related values match?
- **Timeliness:** Is data fresh enough?
- **Uniqueness:** No duplicates?

### Implementing Quality

**Schema validation:** Reject malformed data.

**Constraints:** Check business rules.

**Anomaly detection:** Flag unusual values.

**Tools:** Great Expectations, dbt tests, Deequ.

---

## Common Mistakes

**No idempotency.** Rerunning pipeline creates duplicates.

**No schema enforcement.** Bad data propagates, breaks downstream.

**Monolithic pipelines.** One change requires reprocessing everything.

**No data versioning.** Can't reproduce past results.

**Ignoring late data.** Events arriving after processing window.

**No lineage tracking.** Don't know where data came from or what depends on it.

---

## What An Experienced Senior Engineer Thinks About

**Data contracts.** Producers and consumers agree on schema, quality, SLAs. Changes follow process.

**Cost management.** Data processing is expensive. Right-size jobs, partition data, use spot instances.

**Recovery.** When pipeline fails, how do you backfill? Design for reprocessing.

**Observability.** Monitor data freshness, volume, quality. Alert on anomalies.

**Privacy and compliance.** PII handling, data retention, GDPR/CCPA. Built into pipelines.

---

## Vibe Engineering Guide

When prompting about data processing:

**Less useful:**
> "Build a data pipeline"

**More useful:**
> "I need to process user events for analytics:
> - 10 million events/day from Kafka
> - Need to aggregate: daily active users, feature usage by cohort
> - Results should be queryable in Looker (connected to BigQuery)
> - Some historical reprocessing required monthly
>
> Should I use Spark batch or Flink streaming? How should I structure the data in BigQuery? What orchestration tool?"

**For specific problems:**
> "Our daily Spark job takes 4 hours. It's processing 500GB of JSON logs. We're using 10 r5.xlarge instances. How can we speed this up? Should we change file formats? Partitioning? More instances?"

---

## Quick Check

<details>
<summary><b>What's the difference between ETL and ELT?</b></summary>

ETL transforms data before loading to destination. ELT loads raw data first, transforms in destination. ELT is modern approach - storage is cheap, keep raw data, transformations can evolve.

</details>

<details>
<summary><b>What's a data lake?</b></summary>

Central repository for all data (structured and unstructured) stored in raw format. Uses object storage (S3). Schema defined at query time. Unlike data warehouse which has pre-defined schema.

</details>

<details>
<summary><b>What's Change Data Capture (CDC)?</b></summary>

Capturing database changes (inserts, updates, deletes) from transaction logs in real-time. Creates event stream from database changes without polling. Used for replication, analytics, event sourcing.

</details>

<details>
<summary><b>Why use an orchestration tool like Airflow?</b></summary>

Manage dependencies between jobs, schedule runs, handle retries on failure, monitoring and alerting. Pipelines have steps that depend on each other - orchestration coordinates.

</details>

---

Next: [Containers and Kubernetes](07-containers-kubernetes.md)
