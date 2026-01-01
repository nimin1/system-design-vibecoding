# System Design Interview Cheat Sheet

Quick reference for system design interviews. Print this or keep it handy.

---

## Interview Framework (45 min)

| Phase | Time | What to Do |
|-------|------|------------|
| **Requirements** | 5 min | Clarify functional & non-functional requirements |
| **Estimation** | 5 min | Back-of-envelope: users, QPS, storage |
| **High-Level Design** | 10 min | Draw major components, data flow |
| **Deep Dive** | 20 min | Detail 2-3 key components |
| **Wrap Up** | 5 min | Trade-offs, bottlenecks, future improvements |

---

## Estimation Quick Math

| Metric | Approximation |
|--------|---------------|
| Seconds per day | 86,400 ≈ 100,000 |
| Seconds per month | 2.5 million |
| Seconds per year | 30 million |
| 1 million / day | ~12 per second |
| 1 billion / day | ~12,000 per second |

| Storage | Size |
|---------|------|
| Character | 1 byte |
| Integer | 4-8 bytes |
| Timestamp | 8 bytes |
| UUID | 16 bytes |
| Short URL (7 chars) | 7 bytes |

| Latency | Time |
|---------|------|
| Memory access | 100 ns |
| SSD random read | 100 μs |
| Network round trip (same DC) | 500 μs |
| HDD seek | 10 ms |
| Network round trip (cross-continent) | 100 ms |

---

## Common Components

| Need | Use |
|------|-----|
| Distribute traffic | Load Balancer (L4 or L7) |
| Fast reads | Cache (Redis, Memcached) |
| Static content globally | CDN |
| Async processing | Message Queue (Kafka, SQS) |
| Full-text search | Elasticsearch |
| Rate limiting | Token bucket / Redis |
| Real-time updates | WebSocket |
| Coordination | Zookeeper, etcd |

---

## Database Selection

| Need | Use |
|------|-----|
| Relationships, ACID | PostgreSQL, MySQL |
| Simple KV, high speed | Redis, DynamoDB |
| Documents | MongoDB |
| Wide columns, write-heavy | Cassandra |
| Graph relationships | Neo4j |
| Time-series | InfluxDB, TimescaleDB |
| Search | Elasticsearch |

---

## Scaling Patterns

| Pattern | When to Use |
|---------|-------------|
| Vertical scaling | Quick fix, small scale |
| Horizontal scaling | Web-scale, stateless services |
| Read replicas | Read-heavy workload |
| Sharding | Data won't fit on one node |
| Caching | Repeated reads, expensive queries |
| CDN | Static content, global users |
| Async processing | Non-blocking, spike handling |

---

## Load Balancing Algorithms

| Algorithm | Use When |
|-----------|----------|
| Round Robin | Servers are equal |
| Weighted Round Robin | Servers have different capacity |
| Least Connections | Request duration varies |
| IP Hash | Sticky sessions needed |
| Consistent Hashing | Distributed caching |

---

## Caching Strategies

| Strategy | Best For |
|----------|----------|
| Cache-aside | General purpose |
| Write-through | Consistency important |
| Write-behind | Write-heavy, async ok |
| Read-through | Abstraction, library support |

---

## Consistency Models

| Model | Guarantee | Latency |
|-------|-----------|---------|
| Strong | Always latest | Higher |
| Eventual | Converges eventually | Lower |
| Causal | Respects causality | Medium |

---

## Message Delivery Guarantees

| Guarantee | Duplicates? | Lost? |
|-----------|-------------|-------|
| At-most-once | No | Yes |
| At-least-once | Yes | No |
| Exactly-once | No | No (hardest) |

---

## API Design Quick Reference

| Action | Method | Status |
|--------|--------|--------|
| Get resource | GET | 200 |
| Create | POST | 201 |
| Full update | PUT | 200 |
| Partial update | PATCH | 200 |
| Delete | DELETE | 204 |
| Not found | - | 404 |
| Bad input | - | 400 |
| Unauthorized | - | 401 |
| Forbidden | - | 403 |
| Rate limited | - | 429 |

---

## Distributed System Patterns

| Pattern | Problem Solved |
|---------|----------------|
| Circuit Breaker | Cascade failures |
| Retry + Backoff | Transient failures |
| Idempotency | Duplicate requests |
| Saga | Distributed transactions |
| Outbox | Reliable event publishing |
| CQRS | Read/write optimization |

---

## Key Numbers to Know

| System | Capacity |
|--------|----------|
| Redis | 100K ops/sec per node |
| PostgreSQL | 10K-50K TPS (single node) |
| Kafka | 1M+ messages/sec per topic |
| CDN | Unlimited (adds edge nodes) |
| WebSocket | 10K-100K connections per server |

---

## Questions to Ask

**Functional:**
- What are the core features?
- Who are the users?
- What are the use cases?

**Non-Functional:**
- Expected scale? (users, data, requests)
- Latency requirements?
- Availability requirements?
- Consistency requirements?

**Constraints:**
- Budget? Timeline?
- Existing infrastructure?
- Team experience?

---

## Common Mistakes to Avoid

❌ Jumping into design without understanding requirements
❌ Over-engineering for scale you don't need
❌ Ignoring non-functional requirements
❌ Not discussing trade-offs
❌ Designing in silence (communicate!)
❌ Forgetting failure scenarios
❌ Not knowing numbers (approximate is fine)

---

## Red Flags You're Missing

⚠️ Single point of failure
⚠️ No caching strategy
⚠️ No rate limiting
⚠️ Synchronous calls for slow operations
⚠️ No monitoring/alerting mentioned
⚠️ Hot spots in sharding

---

*Good luck with your interview!*
