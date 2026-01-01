# Cost Optimization

Building systems that scale efficiently without breaking the bank.

---

## Why Cost Matters

Cloud spending grows with usage. Without optimization:
- Small inefficiencies compound at scale
- Overspending reduces runway
- Waste funds competitors, not your product

**Cost is a feature.** Treat it like performance or reliability.

---

## Understanding Cloud Costs

### Compute

Running servers, containers, functions.

**Cost factors:**
- Instance type (CPU, memory, GPU)
- Hours running
- On-demand vs. reserved vs. spot

### Storage

Storing data.

**Cost factors:**
- Volume (GB stored)
- Storage class (hot, cold, archive)
- Operations (reads, writes, lists)
- Egress (data leaving cloud)

### Network

Moving data.

**Cost factors:**
- Egress (data out of cloud = expensive)
- Cross-region transfer
- NAT gateway usage
- Load balancer connections

### Database

Managed database services.

**Cost factors:**
- Instance size
- Storage volume
- IOPS (for provisioned)
- Backups
- Multi-AZ/replicas

---

## Compute Optimization

### Right-Sizing

Don't over-provision.

```
Production server: 80% memory used, 10% CPU
→ Right-size: Smaller CPU, keep memory
```

**How to right-size:**
1. Measure actual usage over time
2. Compare to provisioned capacity
3. Resize to match usage + headroom
4. Repeat periodically

### Reserved Instances

Commit to 1-3 years for discount.

| Commitment | Discount |
|------------|----------|
| No commitment (on-demand) | 0% |
| 1-year reserved | 30-40% |
| 3-year reserved | 50-60% |

**Use for:** Baseline capacity you'll always need.

### Spot/Preemptible Instances

Spare cloud capacity at huge discount (60-90%).

**Catch:** Can be terminated with short notice.

**Use for:**
- Batch processing
- Stateless workers
- Fault-tolerant workloads
- Dev/test environments

### Auto-Scaling

Scale down when not needed.

```
Peak: 20 instances (daytime)
Off-peak: 5 instances (night)
Savings: 75% compute during off-peak
```

**Key:** Actually scale down. Many teams auto-scale up but not down.

### Serverless

Pay per execution.

```
Lambda: Pay only when function runs
Idle = $0
```

**Good for:** Variable, spiky workloads.
**Bad for:** Constant high load (becomes expensive).

---

## Storage Optimization

### Lifecycle Policies

Move data to cheaper storage over time.

```
Day 0-30: S3 Standard ($0.023/GB)
Day 30-90: S3 Standard-IA ($0.0125/GB)
Day 90+: S3 Glacier ($0.004/GB)
After 365 days: Delete
```

Set it and forget it. Objects move automatically.

### Delete What You Don't Need

Obvious but often neglected:
- Old logs
- Unused snapshots
- Orphaned volumes
- Failed multipart uploads
- Old backup versions

### Compression

Smaller data = cheaper storage.

```
Raw logs: 100 GB/day
Compressed (gzip): 10 GB/day
Savings: 90%
```

### Single Source of Truth

Don't duplicate data unnecessarily.

```
Bad: Copy of production database in each dev environment
Good: Read-only access to production or synthetic data
```

---

## Database Optimization

### Choose the Right Database

PostgreSQL vs. DynamoDB vs. specialized database has huge cost implications at scale.

### Aurora Serverless

Scales to zero when not in use. Great for dev/test, variable workloads.

### Reserved Capacity

Same as compute. 1-3 year commitments for discounts.

### Query Optimization

Expensive queries = high IOPS = high cost.

**Index properly.** Missing indexes = table scans = expensive.

### Right-Size Read Replicas

Do you need that read replica if you're reading from cache?

---

## Network Cost Optimization

### Egress is Expensive

Data leaving cloud: ~$0.09/GB

```
1 PB egress/month = $90,000/month
```

**Strategies:**
- CDN (cheaper egress from edge)
- Keep processing in-cloud
- Compress data before transfer
- Consider multi-cloud carefully (transfer costs)

### Same-Region Architecture

Cross-region transfer costs money. Cross-AZ is cheaper but not free.

```
Same AZ: Free
Cross AZ: ~$0.01/GB
Cross region: ~$0.02/GB
To internet: ~$0.09/GB
```

### NAT Gateway Costs

NAT gateways charge per GB processed. High-volume = expensive.

**Optimization:**
- VPC endpoints for AWS services (no NAT)
- S3 gateway endpoints (free)

---

## Caching

Caching reduces compute and database load.

```
Without cache: 100,000 DB queries/hour
With cache (90% hit rate): 10,000 DB queries/hour

Smaller database instance needed
Fewer DB connections needed
Faster response times
```

**Cost:** Redis/Memcached instances.
**Savings:** Reduced database costs, reduced compute for processing.

**ROI is usually very positive.** Calculate actual savings.

---

## Cost Monitoring

### Visibility

You can't optimize what you can't see.

**Set up:**
- Cost explorer / budgets
- Per-service breakdown
- Per-team/project tagging
- Alerts on anomalies

### Tagging

Tag everything for cost allocation.

```
Environment: production/staging/dev
Team: platform/payments/frontend
Project: user-service
```

Now you know what and who is spending.

### Unit Economics

Cost per business unit.

```
Cost per order = Total cost / Orders processed
Cost per user = Total cost / Active users
Cost per API call = Total cost / API calls
```

If cost per order is increasing, investigate.

---

## Common Mistakes

**Orphaned resources.** Experiments left running. EBS volumes without instances.

**Over-provisioned for "safety."** 2x headroom on everything adds up.

**Ignoring egress.** Designs that shuffle data in and out of cloud.

**Not using reserved instances.** Paying on-demand for always-on workloads.

**Gold-plating dev environments.** Production-sized instances for development.

**Not reviewing regularly.** Costs drift. Monthly reviews essential.

---

## What An Experienced Senior Engineer Thinks About

**Cost per X at scale.** What does it cost to serve one more user, process one more order?

**Build vs. buy.** Managed services cost more but save engineering time. What's the trade-off?

**Multi-cloud costs.** Redundancy is good, but egress between clouds is expensive.

**FinOps as culture.** Everyone thinks about cost, not just finance. Engineering decisions have cost implications.

**Total cost of ownership.** Not just cloud bill. Engineering time, on-call, maintenance.

---

## Vibe Engineering Guide

When prompting about cost:

**Less useful:**
> "Reduce my AWS bill"

**More useful:**
> "My AWS bill is $50,000/month:
> - EC2: $30,000 (mix of instance types, no reserved)
> - RDS: $10,000 (db.r5.4xlarge, multi-AZ)
> - S3: $5,000 (500 TB stored)
> - Data transfer: $5,000
>
> We have predictable weekday traffic that drops 80% at night and on weekends. What's the optimization strategy? Should we use reserved instances, spot, or serverless?"

**For specific problems:**
> "Our data transfer costs jumped from $2K to $15K last month. We added a new analytics pipeline that syncs data to BigQuery. How should we optimize cross-cloud data transfer?"

---

## Quick Check

<details>
<summary><b>What's the difference between reserved and spot instances?</b></summary>

Reserved: commitment for 1-3 years, 30-60% discount, guaranteed capacity. Spot: spare capacity, 60-90% discount, can be terminated anytime. Reserved for baseline; spot for fault-tolerant batch work.

</details>

<details>
<summary><b>Why is egress expensive?</b></summary>

Cloud providers charge significantly more for data leaving their network (~$0.09/GB). Keeps data in their cloud. Consider CDN (cheaper egress), compression, and keeping processing in-cloud.

</details>

<details>
<summary><b>What's right-sizing?</b></summary>

Matching instance size to actual usage. If CPU is 10% utilized, you're paying for unused capacity. Monitor usage, resize to match with reasonable headroom.

</details>

<details>
<summary><b>Why use storage lifecycle policies?</b></summary>

Automatically move aging data to cheaper storage tiers. Day-old logs accessed frequently; year-old logs rarely accessed. Automated policies save money without manual intervention.

</details>

---

Next: [Security Fundamentals](09-security-fundamentals.md)
