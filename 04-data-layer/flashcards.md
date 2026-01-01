# Level 4 Flashcards

Quick reference for data layer concepts.

---

## Indexing

| Front | Back |
|-------|------|
| What does an index do? | Maps column values to row locations for fast lookups. |
| Index trade-off? | Faster reads, slower writes. |
| Best index candidates? | Columns in WHERE, JOIN, ORDER BY with high cardinality. |
| Compound index (a, b) helps which queries? | Queries on (a) or (a AND b). Not just (b). |

---

## Replication

| Front | Back |
|-------|------|
| Why replicate? | Availability, read scaling, geographic distribution. |
| Replication lag? | Delay between write on primary and appearance on replica. |
| Sync vs async replication? | Sync: consistent but slower. Async: faster but replicas may lag. |
| Read-your-writes consistency? | After writing, you see your own writes. Route to primary after write. |

---

## Sharding

| Front | Back |
|-------|------|
| Why shard? | When one database can't handle data size or write volume. |
| Sharding vs replication? | Sharding: different data on each server. Replication: same data. |
| Good shard key? | High cardinality, even distribution, in most queries. |
| Hash vs range sharding? | Hash: even distribution. Range: range queries within shard. |

---

## Database Selection

| Front | Back |
|-------|------|
| When to use PostgreSQL? | Default for most apps. Relationships, transactions, complex queries. |
| When to use MongoDB? | Document-shaped data, flexible schema, no complex joins. |
| When to use Redis? | Caching, sessions, rate limiting. Usually alongside primary DB. |
| When to use Elasticsearch? | Full-text search, alongside primary DB. |

---

[Continue to Level 5](../05-distributed-systems/README.md)
