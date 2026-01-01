# Capacity Planning

Ensuring your system can handle the load today and tomorrow.

---

## What is Capacity Planning?

Capacity planning is determining what resources you need to meet demand.

**Too little capacity:** System overloads, users suffer, revenue lost.

**Too much capacity:** Wasted money on unused resources.

**Goal:** Right-sized for current needs plus headroom for growth.

---

## The Capacity Planning Process

### 1. Understand Current Usage

**Measure:**
- Current requests per second
- Peak vs. average load
- Resource utilization (CPU, memory, disk, network)
- Growth trends (week-over-week, month-over-month)

**Key metrics:**
- Traffic patterns (daily, weekly, seasonal)
- Peak/average ratio
- Resource saturation points

### 2. Forecast Demand

**Inputs:**
- Historical growth rate
- Business forecasts (marketing campaigns, new features, partnerships)
- Seasonal patterns
- Known events (Black Friday, product launch)

**Simple forecast:**
```
Current: 1,000 requests/sec
Monthly growth: 10%
In 12 months: 1,000 × 1.1^12 ≈ 3,100 requests/sec
```

### 3. Determine Capacity Needed

**Calculate resources for forecasted demand:**
```
Current: 10 servers handle 1,000 req/sec (100 each)
Future: 3,100 req/sec ÷ 100 = 31 servers
With headroom (30%): 40 servers
```

### 4. Plan Procurement/Scaling

**Cloud:** Auto-scaling + reserved capacity for baseline.

**On-prem:** Lead time for hardware, budget cycles.

**Considerations:**
- Lead time for provisioning
- Cost (on-demand vs. reserved)
- Gradual scale-up vs. big bang

---

## Key Metrics for Capacity

### Request Rate

How many requests per second?

```
Total requests/day: 100 million
Average RPS: 100M / 86,400 ≈ 1,157
Peak RPS: Often 3-5x average ≈ 3,500-5,800
```

### Resource Utilization

How saturated are your resources?

**Healthy targets:**
- CPU: 50-70% average, spikes to 80%
- Memory: 60-80%
- Disk: 50-70%
- Network: Well below limits

**Above these?** Add capacity before trouble.

### Throughput Per Instance

How much can one unit handle?

```
Load test: 1 server saturates at 200 RPS
Production target: 150 RPS (75% of max) for headroom
```

### Latency Under Load

Performance degrades as load increases.

```
At 50% capacity: P99 = 100ms
At 80% capacity: P99 = 200ms
At 95% capacity: P99 = 500ms
```

Know your degradation curve.

---

## Load Testing

Don't guess capacity. Test it.

### Types of Load Tests

**Stress test:** Push to breaking point. Find limits.

**Load test:** Expected peak load. Verify it handles.

**Soak test:** Sustained load over time. Find memory leaks, degradation.

**Spike test:** Sudden traffic surge. Test scaling, recovery.

### What to Measure

- Requests per second achieved
- Latency percentiles (P50, P95, P99)
- Error rate
- Resource utilization
- Where it breaks first (bottleneck)

### Load Testing Tools

- **k6:** Developer-friendly, scriptable
- **Locust:** Python-based
- **Gatling:** Scala-based, detailed reports
- **JMeter:** GUI-based, enterprise favorite

---

## Scaling Strategies

### Vertical Scaling

Bigger machines.

```
db.r5.large → db.r5.4xlarge
```

**Pros:** Simple, no code changes.
**Cons:** Limits, expensive at top end.

### Horizontal Scaling

More machines.

```
3 servers → 10 servers
```

**Pros:** Theoretically unlimited, cost-effective.
**Cons:** Requires stateless design, adds complexity.

### Auto-Scaling

Adjust capacity automatically based on metrics.

```
Scale out when CPU > 70% for 5 minutes
Scale in when CPU < 30% for 15 minutes
Min: 3 instances
Max: 20 instances
```

**Key:** Tune scaling policies. Too aggressive = thrashing. Too conservative = slow response.

---

## Bottleneck Analysis

Systems break at the weakest link.

### Common Bottlenecks

**Database:**
- Connection pool exhausted
- Query latency increases
- Disk IOPS saturated

**Application:**
- CPU saturated
- Memory exhausted (OOM)
- Thread pool exhausted

**Network:**
- Bandwidth limit
- Too many connections
- High latency

**External dependencies:**
- Third-party API rate limits
- Payment gateway capacity

### Finding Bottlenecks

1. Load test until performance degrades
2. Identify which resource hits limit first
3. Address that bottleneck
4. Repeat (next bottleneck emerges)

---

## Capacity for Databases

Databases often the hardest to scale.

### Read vs. Write Capacity

**Read-heavy:** Add read replicas, caching.

**Write-heavy:** Harder. Sharding, eventual consistency.

### Connection Limits

```
PostgreSQL: ~500 connections practical limit
Application: 10 instances × 50 connections = 500
```

**Solutions:** Connection pooling (PgBouncer), reduce connections per instance.

### Storage Growth

```
Current: 100 GB
Growth: 10 GB/month
In 2 years: 340 GB

Plan for: Disk expansion, archival strategy
```

---

## Capacity for Special Events

Black Friday, product launches, viral moments.

### Planning

1. **Estimate traffic:** 5x? 10x? 100x?
2. **Pre-scale:** Don't rely on auto-scaling for sudden spikes
3. **Test:** Load test at expected peak
4. **War room:** Engineers on standby
5. **Runbooks:** What to do when things break

### Traffic Shaping

When capacity is truly fixed:

**Queueing:** Accept requests, process later.

**Rate limiting:** Reject excess requests gracefully.

**Degraded mode:** Disable non-essential features.

---

## Headroom

Never run at 100% capacity.

### Why Headroom

- Handle unexpected spikes
- Allow for failures (lose one instance, others absorb)
- Performance degrades before 100%
- Time to scale up

### Guidelines

| Metric | Target | Action |
|--------|--------|--------|
| CPU | < 70% | Scale if consistently above |
| Memory | < 80% | Scale if consistently above |
| Disk | < 70% | Expand if approaching |
| Connections | < 80% pool | Increase pool or instances |

---

## Capacity Reviews

### Regular Reviews

Monthly or quarterly:
- Review growth trends
- Update forecasts
- Identify emerging bottlenecks
- Plan capacity changes

### Pre-Event Reviews

Before known events:
- Estimate traffic increase
- Plan capacity additions
- Test at expected load
- Prepare rollback plan

---

## Common Mistakes

**No baseline.** Don't know current capacity or usage.

**Waiting until crisis.** Scale before you hit limits.

**Over-relying on auto-scaling.** Sudden spikes too fast for auto-scale.

**Ignoring database.** App scales, database doesn't.

**Planning for average, not peak.** Systems must handle peaks.

**Not testing.** Assumptions about capacity, never verified.

---

## What An Experienced Senior Engineer Thinks About

**Capacity model.** Mathematical model relating requests to resources. Validated with testing.

**N+1 redundancy.** Lose one instance and still handle load.

**Cross-cutting dependencies.** Auth service capacity affects every service that uses it.

**Cost of over-provisioning vs. under-provisioning.** Over = wasted money. Under = outages, lost revenue. Balance.

---

## Vibe Engineering Guide

When prompting about capacity:

**Less useful:**
> "How much capacity do I need?"

**More useful:**
> "I'm planning capacity for my API:
> - Current: 500 requests/sec average, 1500 peak
> - Growth: 15% month-over-month
> - Each request: ~50ms CPU, 2 DB queries
> - Servers: c5.large (2 vCPU, 4GB)
> - Current: 5 servers at 40% CPU average
>
> How many servers will I need in 6 months? How should I factor in peak vs. average? Should I use reserved instances?"

**For specific problems:**
> "Our database is at 80% connection utilization at peak. We have 10 app servers with 50 connections each. If we add more app servers, we'll exhaust connections. What are our options?"

---

## Quick Check

<details>
<summary><b>Why measure peak vs. average load?</b></summary>

Systems must handle peaks, not just averages. Peak might be 3-5x average. Capacity for average = failure at peak.

</details>

<details>
<summary><b>What's the purpose of load testing?</b></summary>

Verify capacity assumptions. Find breaking points. Identify bottlenecks. Know actual limits before production finds them.

</details>

<details>
<summary><b>Why keep headroom?</b></summary>

Handle unexpected spikes, allow for instance failures, prevent performance degradation. Never run at 100%.

</details>

<details>
<summary><b>What's N+1 redundancy?</b></summary>

Have one more instance than minimum needed. If one fails, remaining instances can handle full load. Avoid single points of failure.

</details>

---

Next: [Incident Management](05-incident-management.md)
