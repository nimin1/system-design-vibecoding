# Numbers Every Engineer Should Know

Quick reference for estimation and back-of-envelope calculations.

---

## Latency Numbers

| Operation | Time | Notes |
|-----------|------|-------|
| L1 cache reference | ~1 ns | Fastest |
| L2 cache reference | 4-7 ns | |
| RAM reference | ~100 ns | |
| Redis (localhost) | 0.1-0.5 ms | |
| Redis (network) | 0.5-2 ms | |
| SSD random read | 100 μs | 100x slower than RAM |
| HDD seek | 10 ms | 100x slower than SSD |
| Network: same datacenter | 500 μs | |
| Network: same continent | 50 ms | |
| Network: cross-continent | 150 ms | |
| Read 1 MB from SSD | 1 ms | |
| Read 1 MB from HDD | 20 ms | |
| Read 1 MB from network | 10 ms | 1 Gbps |

---

## Time Conversions

| Unit | Seconds | Easy Approximation |
|------|---------|-------------------|
| Minute | 60 | 60 |
| Hour | 3,600 | 4,000 |
| Day | 86,400 | 100,000 |
| Week | 604,800 | 600,000 |
| Month | 2,592,000 | 2.5 million |
| Year | 31,536,000 | 30 million |

---

## Request Rate Conversions

| Requests | Per Second |
|----------|------------|
| 1 million / day | 12 |
| 10 million / day | 115 |
| 100 million / day | 1,150 |
| 1 billion / day | 11,500 |

**Formula:** requests per day ÷ 86,400 ≈ requests per second

**Peak factor:** Multiply by 2-5x for peak traffic

---

## Data Size Reference

| Type | Size |
|------|------|
| ASCII character | 1 byte |
| Unicode character | 2-4 bytes |
| Integer | 4-8 bytes |
| Long/Double | 8 bytes |
| UUID | 16 bytes (128 bits) |
| Timestamp | 8 bytes |
| MD5 | 16 bytes (128 bits) |
| SHA-256 | 32 bytes (256 bits) |

| Content | Size |
|---------|------|
| Tweet (280 chars) | ~300 bytes |
| JSON object (small) | 1-5 KB |
| JPEG image (web) | 100-500 KB |
| HD photo | 2-5 MB |
| MP3 (per minute) | 1 MB |
| Video (HD, per minute) | 100-300 MB |

---

## Size Prefixes

| Prefix | Factor | Bytes |
|--------|--------|-------|
| KB | 10³ | 1,000 |
| MB | 10⁶ | 1,000,000 |
| GB | 10⁹ | 1,000,000,000 |
| TB | 10¹² | 1,000,000,000,000 |
| PB | 10¹⁵ | 1,000,000,000,000,000 |

**Quick powers of 2:**
- 2¹⁰ = 1,024 ≈ 1,000 (KB)
- 2²⁰ = 1,048,576 ≈ 1 million (MB)
- 2³⁰ = 1,073,741,824 ≈ 1 billion (GB)

---

## System Capacity Reference

| Component | Typical Capacity |
|-----------|-----------------|
| Redis (single) | 100K-1M ops/sec (depends on operation) |
| PostgreSQL (single) | 10K-50K TPS |
| MySQL (single) | 10K-50K TPS |
| Kafka (per partition) | 10K-100K msg/sec |
| Elasticsearch | 10K-50K writes/sec per shard |
| WebSocket server | 10K-100K concurrent connections |
| HTTP server (nginx) | 10K-100K+ concurrent connections |

*These are ballpark figures. Actual capacity depends on hardware, configuration, and workload patterns.*

---

## Database Row Counts

| Rows | Can Single DB Handle? |
|------|----------------------|
| < 1 million | Easily |
| 1-10 million | Yes, with proper indexing |
| 10-100 million | Yes, consider read replicas |
| 100M - 1B | Consider sharding |
| > 1 billion | Likely needs sharding |

*These are rough guidelines, not hard rules. Actual limits depend on:*
- *Query patterns (point lookups vs. full scans)*
- *Write throughput requirements*
- *Index size vs. available memory*
- *Hardware specifications*

*Well-indexed PostgreSQL can handle billions of rows for read-heavy, point-lookup workloads.*

---

## Connection Pool Sizing

**Classic formula:** connections = (core_count × 2) + spindle_count

**For SSDs:** connections ≈ core_count × 2

For web apps: 10-50 connections per application server

For database: total connections across all app servers < DB max (usually 100-500)

---

## Availability Numbers

| Availability | Downtime/Year | Downtime/Month |
|--------------|---------------|----------------|
| 99% (two nines) | 3.65 days | 7.3 hours |
| 99.9% (three nines) | 8.76 hours | 43.8 minutes |
| 99.99% (four nines) | 52.6 minutes | 4.4 minutes |
| 99.999% (five nines) | 5.26 minutes | 26 seconds |

---

## Network Bandwidth

| Speed | Transfer Rate |
|-------|---------------|
| 100 Mbps | 12.5 MB/sec |
| 1 Gbps | 125 MB/sec |
| 10 Gbps | 1.25 GB/sec |

**Time to transfer 1 GB:**
- 100 Mbps: 80 seconds
- 1 Gbps: 8 seconds
- 10 Gbps: < 1 second

---

## Quick Estimation Examples

### Twitter-Scale Daily Tweets

**Given:** 500 million tweets/day, 280 chars average

**Storage:** 500M × 300 bytes = 150 GB/day ≈ 55 TB/year (before media)

### URL Shortener Scale

**Given:** 100 million URLs/month, 7 char codes

**QPS:** 100M ÷ 30 days ÷ 86,400 ≈ 40 writes/sec

**Read:Write ratio 100:1:** 4,000 reads/sec

### Video Platform Storage

**Given:** 500 hours video uploaded/minute, 100 MB/minute

**Storage:** 500 × 60 × 100 MB = 3 TB/hour ≈ 72 TB/day

---

*Use these numbers for quick estimation. Exact values vary - order of magnitude is what matters.*
