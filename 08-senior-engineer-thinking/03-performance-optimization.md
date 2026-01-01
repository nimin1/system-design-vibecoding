# Performance Optimization

Making systems faster know what to measure, where to look, and when to stop.

---

## The First Rule

**Measure first.** Don't guess.

Intuition about performance is often wrong. Profile, benchmark, then optimize.

---

## What to Measure

### Latency

How long does a request take?

**Metrics:**
- P50: Median (half faster, half slower)
- P95: 95th percentile (5% of requests slower)
- P99: 99th percentile (1% of requests slower)

**Why percentiles?** Averages hide long-tail latency.

```
Average: 100ms (seems fine)
P99: 2,000ms (1% of users waiting 2+ seconds)
```

### Throughput

How many requests per second can you handle?

**Measure:**
- Requests per second (RPS)
- Transactions per second (TPS)
- Messages per second

### Resource Utilization

How much of your capacity are you using?

**Monitor:**
- CPU usage
- Memory usage
- Disk I/O
- Network bandwidth
- Connection pools

---

## Where to Look

### The Request Path

Trace a request through your system.

```
Client → Load Balancer → App Server → Cache → Database → Response
         0.5ms           2ms          0.1ms   50ms       = 52.6ms
```

**Optimize:** Where time is spent.

### Common Bottlenecks

**Database:**
- Slow queries (missing indexes, complex joins)
- Too many queries (N+1 problem)
- Connection pool exhaustion

**Network:**
- Excessive round trips
- Large payloads
- Cross-region latency

**Application:**
- Inefficient algorithms
- Synchronous I/O
- Lock contention

**External services:**
- Third-party API latency
- Rate limiting

---

## Database Optimization

### Indexing

Most common performance issue.

**Signs of missing index:**
- Slow queries
- Table scans in query plan
- High disk I/O

**Solution:** Add indexes for WHERE, JOIN, ORDER BY columns.

### N+1 Queries

Fetching related data in loops.

```
Bad:
  SELECT * FROM orders WHERE user_id = 1  -- 1 query
  For each order:
    SELECT * FROM items WHERE order_id = X  -- N queries  
  Total: 1 + N queries

Good:
  SELECT * FROM orders WHERE user_id = 1
  SELECT * FROM items WHERE order_id IN (...)  -- 2 queries total
```

### Query Optimization

**Check execution plan:** EXPLAIN ANALYZE

**Common fixes:**
- Add indexes
- Rewrite joins
- Limit result sets
- Avoid SELECT *

### Connection Pooling

Opening connections is expensive.

```
Without pooling: Open connection, query, close (50-100ms overhead)
With pooling: Reuse connection from pool (~0ms overhead)
```

---

## Caching

Fastest request is one you don't make.

### What to Cache

- Expensive computations
- Frequently accessed data
- Slow external API responses
- Session data

### Cache-Aside Pattern

```
1. Check cache
2. If miss: query database, store in cache
3. If hit: return cached value
```

### Cache Invalidation

Hard problem.

**Strategies:**
- TTL (time-to-live): Cache expires after X seconds
- Write-through: Update cache on write
- Event-based: Invalidate on change event

### Cache Hit Rate

Measure effectiveness.

```
Hit rate = Cache hits / (Cache hits + Cache misses)
```

Below 80%? Cache might not be helping much.

---

## Async Processing

Don't make users wait for things that can happen later.

### Move Work Off the Request Path

```
Synchronous:
  POST /order → Create order → Send email → Charge payment → Response
  Total: 500ms

Asynchronous:
  POST /order → Create order → Queue email → Queue payment → Response
  Total: 50ms
  Background: Send email, charge payment
```

### Message Queues

Background workers process queued tasks.

**Good for:**
- Sending emails/notifications
- Processing uploads
- Generating reports
- Calling slow external APIs

---

## Connection Optimization

### Keep-Alive

Reuse TCP connections.

```
Without keep-alive: New TCP handshake per request (1-2 RTTs)
With keep-alive: Reuse existing connection (0 RTTs)
```

### Connection Pooling

For database, HTTP clients, etc.

```
Pool size: Balance between resource usage and concurrency
Too small: Requests wait for connections
Too large: Too many connections open
```

### HTTP/2

Multiple requests over single connection.

```
HTTP/1.1: Max 6 connections, each one request at a time
HTTP/2: Single connection, many concurrent requests
```

---

## Compression

Smaller data = faster transfer.

### Text Compression

gzip, brotli for text content.

```
Without compression: 500 KB response
With gzip: 100 KB response (80% smaller)
```

**Trade-off:** CPU for compression/decompression.

### Image Optimization

- Appropriate format (WebP, AVIF for web)
- Appropriate size (don't serve 4K for thumbnails)
- Lazy loading

---

## CDN

Serve static content from edge locations.

**Benefits:**
- Lower latency (closer to users)
- Reduced origin load
- DDoS protection

**Good for:** Images, CSS, JS, static pages.

---

## Profiling

Find where time is spent.

### Application Profiling

CPU profilers show where time is spent in code.

**Tools:** pprof (Go), py-spy (Python), async-profiler (Java)

### Distributed Tracing

Follow requests across services.

```
Request → Service A (10ms) → Service B (50ms) → Database (30ms)
```

Identify which service is slow.

**Tools:** Jaeger, Zipkin, Datadog APM

### Database Query Analysis

Find slow queries.

```
pg_stat_statements (PostgreSQL)
Slow query log (MySQL)
```

---

## When Not to Optimize

### Premature Optimization

"Premature optimization is the root of all evil" - Knuth

**Don't optimize:**
- Before you have a problem
- Based on assumptions, not measurements
- Uncommon code paths

### Diminishing Returns

```
Iteration 1: 1000ms → 100ms (10x improvement)
Iteration 2: 100ms → 50ms (2x improvement)
Iteration 3: 50ms → 45ms (1.1x improvement)
```

Each improvement is harder. Know when to stop.

### Complexity Cost

Optimizations add complexity.

```
Simple code: 100ms, easy to understand
Optimized code: 50ms, hard to maintain
```

Is 50ms worth the maintenance burden?

---

## Common Mistakes

**Guessing.** Optimize what you measured, not what you assume.

**Microbenchmarks.** Real-world differs from benchmarks.

**Ignoring P99.** Average is fine, but 1% of users suffer.

**Caching without invalidation strategy.** Eventually serves stale data.

**Optimizing cold path.** Focus on hot paths that run frequently.

**Adding complexity for marginal gain.** 5% improvement isn't worth major complexity.

---

## What An Experienced Senior Engineer Thinks About

**End-to-end latency.** Not just one component. Full user experience.

**Latency budgets.** Allocate time across components. "Database gets 50ms, service calls get 30ms."

**Trade-off with cost.** Faster often means more expensive. Find balance.

**Sustainable optimization.** Code must remain maintainable.

---

## Vibe Engineering Guide

When prompting about performance:

**Less useful:**
> "Make my app faster"

**More useful:**
> "My API latency is P50=50ms, P99=800ms. Most of the P99 time is database:
> - Query: SELECT * FROM orders WHERE user_id = X ORDER BY created_at DESC LIMIT 10
> - Table has 50 million rows
> - user_id is indexed
>
> Why is P99 so much worse than P50? What should I check?"

**For specific problems:**
> "We added Redis caching with 1-hour TTL. Cache hit rate is 40%. We expected higher because the same products are viewed repeatedly. What could explain low hit rate? How do we debug?"

---

## Quick Check

<details>
<summary><b>Why use percentiles instead of averages?</b></summary>

Averages hide outliers. Average 100ms could mean most are 50ms but some are 1000ms. P99 shows what the slowest 1% experience, which matters for user experience.

</details>

<details>
<summary><b>What's the N+1 query problem?</b></summary>

Fetching related data in a loop. 1 query for main table, N queries for related data. Solution: Fetch all related data in single query with IN clause or JOIN.

</details>

<details>
<summary><b>When should you NOT optimize?</b></summary>

Before measuring (guessing wrong target), for code paths rarely executed (cold paths), when complexity cost exceeds benefit, when performance is already sufficient.

</details>

<details>
<summary><b>What's the first step in optimization?</b></summary>

Measure. Profile the system. Find where time is actually spent. Don't guess - intuition about performance is frequently wrong.

</details>

---

Next: [Capacity Planning](04-capacity-planning.md)
