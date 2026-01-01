# Distributed Logging

Collecting, aggregating, and searching logs from thousands of servers.

---

## Why Distributed Logging

**The problem:** You have 100 application servers. Something went wrong. Which log file has the error?

SSH-ing into each server and grep-ing log files doesn't scale.

**The solution:** Centralize logs in one searchable place.

---

## Requirements

### Functional Requirements

- Collect logs from all servers
- Search logs by time range, service, severity
- Filter by arbitrary fields
- View log context (before/after a specific log)
- Alerting on error patterns

### Non-Functional Requirements

- High throughput (thousands of logs/second)
- Low latency search (< 5 seconds for recent logs)
- Retention (days to months depending on compliance)
- Durability (don't lose logs)
- Minimal impact on application performance

---

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    Distributed Logging                          │
└─────────────────────────────────────────────────────────────────┘

┌────────────────┐
│   App Servers  │ ←─ Log to local files or stdout
│   (100s-1000s) │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Log Shipper   │ ←─ Filebeat, Fluentd, Vector
│   (per host)   │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Message Queue │ ←─ Kafka (buffer, decouple)
│                │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Log Processor │ ←─ Parse, enrich, filter
│                │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│  Log Storage   │ ←─ Elasticsearch, Loki
│    & Index     │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│   Search UI    │ ←─ Kibana, Grafana
└────────────────┘
```

---

## Log Collection

### Application Logging

Applications write logs to:
- **stdout/stderr:** Container-friendly, captured by orchestrator
- **Files:** Traditional, requires rotation
- **Direct to collector:** Tighter coupling but simpler

### Structured Logging

JSON logs are searchable. Plain text isn't.

**Bad (unstructured):**
```
2024-01-15 10:23:45 ERROR Failed to process order 12345 for user 67890
```

**Good (structured):**
```json
{
  "timestamp": "2024-01-15T10:23:45Z",
  "level": "ERROR",
  "message": "Failed to process order",
  "order_id": 12345,
  "user_id": 67890,
  "service": "order-service",
  "trace_id": "abc123"
}
```

Structured logs enable:
- Filtering by any field
- Aggregation (count errors by service)
- Correlation (find all logs for a trace_id)

### Log Shippers

Lightweight agents on each server.

| Tool | Notes |
|------|-------|
| Filebeat | ELK stack, lightweight |
| Fluentd | Flexible, CNCF project |
| Fluent Bit | Smaller than Fluentd |
| Vector | Rust-based, performant |
| Promtail | For Loki/Grafana stack |

**Responsibilities:**
- Tail log files
- Parse log format
- Add metadata (hostname, environment)
- Buffer if destination is unavailable
- Ship to central system

---

## Buffering with Message Queue

**Why buffer?**
- Logs spike during incidents (when you need them most)
- Storage might be temporarily unavailable
- Smooths out traffic patterns

**Kafka is common:**
- High throughput
- Durability (logs won't be lost)
- Multiple consumers possible
- Replay capability

---

## Log Processing

Transform logs before storage.

### Parsing

Extract fields from semi-structured logs:

```
Input: "192.168.1.1 - - [15/Jan/2024:10:23:45] "GET /api/users HTTP/1.1" 200 1234"

Output:
{
  "client_ip": "192.168.1.1",
  "timestamp": "2024-01-15T10:23:45Z",
  "method": "GET",
  "path": "/api/users",
  "status": 200,
  "bytes": 1234
}
```

### Enrichment

Add context:
- Geo-location from IP
- Service name from hostname
- Environment (prod/staging)

### Filtering

Drop or sample high-volume, low-value logs:
- Debug logs in production
- Health check requests
- Successful routine operations

### Redaction

Remove sensitive data:
- Passwords
- Credit card numbers
- PII (comply with regulations)

---

## Log Storage

### Elasticsearch

Most common choice for log storage.

**Why:**
- Full-text search
- Fast queries
- Aggregations
- Kibana UI

**Considerations:**
- Resource intensive
- Requires tuning at scale
- Index management needed

### Loki (Grafana)

Log aggregation inspired by Prometheus.

**Why:**
- Lighter weight than Elasticsearch
- Only indexes labels, not content
- Good Grafana integration
- Cost-effective

**Trade-off:** Less powerful search, grep-style filtering.

### S3 + Athena

For archival and compliance.

**Why:**
- Cheapest storage
- SQL queries via Athena
- Long retention

**Trade-off:** Slower queries, not real-time.

---

## Index Lifecycle Management

Logs grow forever. Manage storage costs.

### Hot-Warm-Cold Architecture

```
Hot (SSD):   Last 24 hours - fast search
Warm (HDD):  Last 7 days - slower but searchable
Cold (S3):   30+ days - archived, slow queries
Delete:      After retention period
```

### Index Rollover

Create new index daily/weekly:
- `logs-2024.01.15`
- `logs-2024.01.16`

Enables:
- Deleting old indices easily
- Different settings per age
- Parallel queries across indices

---

## Search and Querying

### Common Queries

**By time range:**
```
timestamp:[2024-01-15T10:00:00 TO 2024-01-15T11:00:00]
```

**By service and level:**
```
service:order-service AND level:ERROR
```

**By trace (distributed tracing):**
```
trace_id:abc123
```

### Query Performance

**Fast:** Indexed fields (service, level, trace_id)
**Slow:** Full-text search in message content
**Very slow:** Regex on unindexed fields

Index fields you filter on frequently.

---

## Log Correlation

Connecting logs across services.

### Trace ID

Include a unique ID for each request:

```
Request → Service A (trace_id=abc) → Service B (trace_id=abc) → Service C (trace_id=abc)
```

All logs with same trace_id show the full journey.

### Request ID

Similar to trace ID, sometimes used for single-service correlation.

### Implementation

- Generate trace_id at entry point
- Pass in headers (X-Trace-ID)
- Include in all log entries

---

## Alerting on Logs

Detect problems from log patterns.

### What to Alert On

- Error rate spike
- Specific error messages (payment failures)
- Unusual patterns (login failures)
- Missing expected logs (no logs = problem?)

### Tools

- ElastAlert (for Elasticsearch)
- Grafana alerting (for Loki)
- Custom consumers from Kafka

---

## Common Mistakes

**Too much logging.** Log what matters. Don't log every function call.

**Too little logging.** Log enough to debug problems. Include context.

**Unstructured logs.** Can't search what you can't parse.

**No log rotation.** Disks fill up. Rotate and archive.

**No retention policy.** Logs grow forever. Define how long to keep.

**Logging sensitive data.** PII, passwords, tokens. Redact or don't log.

---

## What An Experienced Senior Engineer Thinks About

**Cost management.** Logging can be expensive at scale. Sample where appropriate.

**Compliance.** Some industries require log retention. Some require log deletion.

**Correlation.** Logs, metrics, and traces together tell the full story.

**Debugging experience.** Good logging makes debugging 10x faster.

---

## Vibe Engineering Guide

When prompting about logging:

**Less useful:**
> "Add logging to my service"

**More useful:**
> "Add structured JSON logging to this Node.js service:
> - Log all HTTP requests with method, path, status, duration
> - Log errors with stack traces and context
> - Include request_id for correlation
> - Log to stdout (containerized environment)
> - Appropriate log levels (DEBUG, INFO, WARN, ERROR)"

**For infrastructure:**
> "Design a log aggregation pipeline:
> - 50 servers, 1000 logs/second total
> - 7-day retention with search
> - Alert on error rate spikes
>
> Should I use ELK or Loki? What are the trade-offs?"

---

## Quick Check

<details>
<summary><b>Why use structured logging?</b></summary>

Structured logs (JSON) have fields that can be indexed and searched. Unstructured text requires parsing or regex, which is slow and error-prone.

</details>

<details>
<summary><b>Why buffer logs through Kafka?</b></summary>

Handle spikes (logs increase during incidents), survive destination downtime, decouple producers from consumers, enable multiple consumers.

</details>

<details>
<summary><b>What's hot-warm-cold architecture?</b></summary>

Recent logs on fast storage (SSD), older logs on slower storage (HDD), archive to cheap storage (S3). Balances search speed with cost.

</details>

<details>
<summary><b>Why include trace_id in every log?</b></summary>

Enables correlation across services. One request touches multiple services - trace_id connects all their logs for debugging.

</details>

---

Next: [Object Storage](10-object-storage.md)
