# Event Streaming

Processing continuous flows of data in real-time.

---

## What is Event Streaming?

Event streaming is processing a continuous flow of events as they happen, rather than batch processing collected data later.

**Event:** Something that happened.
**Stream:** Unbounded, continuous sequence of events.
**Processing:** Transforming, aggregating, analyzing events in real-time.

---

## Streaming vs. Batch Processing

### Batch Processing

Collect data → Process in bulk → Output results.

```
Collect logs all day
Every night: process yesterday's logs
Generate daily report
```

**Characteristics:**
- High latency (hours to days)
- Complete data for the period
- Simple failure model (rerun batch)
- Tools: MapReduce, Spark batch, data warehouses

### Stream Processing

Process events as they arrive.

```
Log event arrives
Immediately: update real-time dashboard
Detect anomaly → alert now
```

**Characteristics:**
- Low latency (milliseconds to seconds)
- Never-ending flow
- Complex failure handling
- Tools: Kafka Streams, Apache Flink, Apache Spark Streaming

### When to Use Each

| Use Case | Approach |
|----------|----------|
| Daily sales report | Batch |
| Real-time fraud detection | Stream |
| Recommendation model training | Batch |
| Live recommendation updates | Stream |
| Historical analytics | Batch |
| Real-time dashboards | Stream |

Many systems use both: stream for real-time, batch for historical and reprocessing.

---

## Apache Kafka

The most widely used event streaming platform.

### Core Concepts

**Topic:** Named stream of events. Like a category.
```
Topics: user-events, orders, page-views
```

**Partition:** Topic split into partitions for parallelism.
```
Topic: orders
  Partition 0: orders 0, 3, 6, 9...
  Partition 1: orders 1, 4, 7, 10...
  Partition 2: orders 2, 5, 8, 11...
```

**Offset:** Position of message within partition.
```
Partition 0: offset 0, 1, 2, 3, 4...
Consumer tracks: "I've processed up to offset 42"
```

**Producer:** Publishes events to topics.
**Consumer:** Reads events from topics.
**Consumer Group:** Multiple consumers sharing the work.

### Key Properties

**Durability:** Events stored on disk, replicated.

**Ordering:** Events ordered within partition.

**Retention:** Keep events for configurable time (days, weeks, forever).

**Replay:** Consumers can reread old events by resetting offset.

### Partitioning

Events distributed across partitions by key.

```
Events with key "user-123" → always same partition
Events with null key → round-robin across partitions
```

**Why partitioning:**
- Parallelism (multiple consumers)
- Ordering guarantee (within partition)
- Scalability (add partitions for throughput)

---

## Stream Processing Patterns

### Stateless Processing

Each event processed independently.

```
Input: log line with request details
Transform: extract fields, format
Output: structured log event
```

No memory between events. Simple, scales easily.

### Stateful Processing

Processing depends on previous events.

```
Input: click events
Maintain state: count per user in last hour
Output: users with > 100 clicks (spam detection)
```

Requires state management, checkpointing.

### Windowing

Group events by time windows.

**Tumbling window:** Fixed, non-overlapping.
```
Window 1: 10:00-10:05
Window 2: 10:05-10:10
Each event belongs to exactly one window
```

**Sliding window:** Overlapping windows.
```
Every 1 minute, aggregate last 5 minutes
10:06 window includes events from 10:01-10:06
10:07 window includes events from 10:02-10:07
```

**Session window:** Based on activity.
```
Events within 30 minutes of each other = same session
Gap > 30 minutes = new session
```

### Joins

Combine events from multiple streams.

```
Stream 1: User clicks
Stream 2: User purchases
Join: Match click and purchase by user_id within 1 hour
Output: Attribution (which clicks led to purchase)
```

**Challenges:** Events arrive out of order, streams at different speeds.

---

## Stream Processing Frameworks

### Kafka Streams

Library for building Kafka stream processing apps.

**Pros:**
- No separate cluster (runs in your app)
- Simple programming model
- State management, exactly-once

**Cons:**
- Tied to Kafka
- Limited to JVM

**Use for:** Kafka-centric architectures, microservices processing events.

### Apache Flink

Distributed stream processing engine.

**Pros:**
- Very powerful (complex event processing)
- Exactly-once guarantees
- Excellent state management
- Both stream and batch

**Cons:**
- Separate cluster to manage
- More complex operations

**Use for:** Complex stream processing, low-latency requirements, large scale.

### Apache Spark Streaming

Micro-batch stream processing with Spark.

**Pros:**
- Unified batch and stream
- Spark ecosystem integration
- Familiar if you know Spark

**Cons:**
- Micro-batch has higher latency than true streaming
- Not true event-at-a-time

**Use for:** When you need batch and stream, already using Spark.

### AWS Kinesis

Managed streaming service.

**Pros:**
- Managed (no clusters to run)
- AWS integration
- Simple for AWS users

**Cons:**
- AWS-only
- Less performant than Kafka

**Use for:** AWS-native architectures, simpler requirements.

---

## Delivery Guarantees

### At-Most-Once

Events may be lost, never duplicated. Fastest.

```
Produce event, don't wait for ack
If fails, event lost
```

**Use for:** Metrics where occasional loss is acceptable.

### At-Least-Once

Events never lost, may be duplicated. Most common.

```
Produce event, wait for ack
If fails, retry → might duplicate
```

**Use for:** Most applications. Handle duplicates with idempotency.

### Exactly-Once

Each event processed exactly once. Holy grail.

```
Complex mechanism:
- Idempotent producers
- Transactional consumers
- Coordinated checkpointing
```

**Available in:** Kafka (with transactions), Flink

**Trade-offs:** Performance cost, complexity.

---

## Common Use Cases

### Real-Time Analytics

```
Events: page views, clicks, transactions
Process: aggregate per minute/hour
Output: real-time dashboards
```

### Anomaly Detection

```
Events: login attempts, transactions
Process: compare against normal patterns
Output: alerts for anomalies
```

### Event Sourcing

```
Events: OrderCreated, ItemAdded, OrderPaid
Store all events, derive current state
Replay for recovery or new views
```

### Data Integration

```
Events from various sources
Transform and route to destinations
Real-time ETL (Extract-Transform-Load)
```

### IoT Data Processing

```
Events: sensor readings (millions per second)
Process: aggregate, detect thresholds
Output: alerts, dashboards, storage
```

---

## Challenges

### Event Ordering

Events may arrive out of order.

**Solutions:**
- Timestamp-based ordering
- Window-based processing (wait for stragglers)
- Accept some disorder (depending on use case)

### Late Events

Events arrive after window closed.

**Options:**
- Ignore (simple, but loses data)
- Watermarks (wait until confident all arrived)
- Allowed lateness (keep window open longer)
- Late data triggers recomputation

### State Management

Stateful processing needs:
- State storage (memory, disk)
- Checkpointing (save state periodically)
- Recovery (restore on failure)

### Backpressure

Producer faster than consumer.

**Solutions:**
- Buffer (queue fills up)
- Drop (lose events)
- Flow control (slow producer)

---

## Common Mistakes

**Not considering ordering.** Assuming events arrive in order. They don't.

**Unbounded state.** State grows forever (e.g., count per user ID for all time). OOM.

**Not handling duplicates.** At-least-once means duplicates. Process idempotently.

**Ignoring backpressure.** Fast producer, slow consumer. Queue explosion.

**Checkpointing too infrequently.** Failure means reprocessing from last checkpoint. If hours ago, that's a lot.

---

## What An Experienced Senior Engineer Thinks About

**Lambda vs. Kappa architecture.**
- Lambda: Batch layer + streaming layer, merge results
- Kappa: Everything is a stream, no separate batch

Kappa is simpler but requires reprocessing streams for historical.

**Schema evolution.** Events have schema. How do you change it? Schema registry, versioning, backward compatibility.

**Operational complexity.** Streaming systems are complex to operate. Monitoring, alerting, debugging real-time systems.

**Cost.** High throughput streaming is expensive. Compute, storage, network.

---

## Vibe Engineering Guide

When prompting about event streaming:

**Less useful:**
> "Use Kafka"

**More useful:**
> "I need to detect fraudulent transactions in real-time:
> - 10,000 transactions/second
> - Need to check: unusual amount, unusual location, velocity (many transactions quickly)
> - Alert within 1 second of suspicious transaction
>
> Should I use Kafka with Kafka Streams or Kafka with Flink? How do I maintain the state (user's recent transactions) for velocity checks? What happens if a processing node fails?"

**For specific problems:**
> "Our Kafka consumer is falling behind. Lag is increasing. We have 10 partitions and 10 consumers. Processing takes ~50ms per message. Messages arrive at 1000/second. Why are we behind and how do we scale?"

---

## Quick Check

<details>
<summary><b>What's the difference between stream and batch processing?</b></summary>

Batch: collect data, process in bulk later. High latency but complete data. Stream: process events as they arrive. Low latency but continuous flow. Different tools and patterns for each.

</details>

<details>
<summary><b>Why partition a Kafka topic?</b></summary>

Parallelism (multiple consumers), scalability (distribute load), and ordering (guaranteed within partition). More partitions = more parallelism but also more complexity.

</details>

<details>
<summary><b>What's a windowing operation?</b></summary>

Grouping events by time for aggregation. Tumbling windows are fixed non-overlapping. Sliding windows overlap. Session windows group by activity gaps.

</details>

<details>
<summary><b>What's backpressure in streaming?</b></summary>

When producer produces faster than consumer consumes. Queue fills up. Solutions: buffer (risks OOM), drop (loses data), flow control (slow producer).

</details>

---

Next: [Data Processing](06-data-processing.md)
