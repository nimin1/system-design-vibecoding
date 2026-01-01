# Level 4 Quiz

Test your understanding of data layer concepts.

---

## Indexing

<details>
<summary><b>1. What's the trade-off of adding indexes?</b></summary>

Faster reads, slower writes. Each write must also update the index. Add indexes on frequently queried columns, but be conservative on write-heavy tables.

</details>

<details>
<summary><b>2. When does a compound index on (a, b) help?</b></summary>

Queries on column a, or on (a AND b). Not queries on just b. The leftmost columns must be included.

</details>

<details>
<summary><b>3. How do you verify an index is being used?</b></summary>

Use EXPLAIN (or EXPLAIN ANALYZE). Look for Index Scan vs Seq Scan (sequential scan).

</details>

---

## Replication

<details>
<summary><b>4. What's replication lag?</b></summary>

The delay between a write on the primary and that write appearing on replicas. During this time, replicas return stale data.

</details>

<details>
<summary><b>5. Sync vs async replication trade-off?</b></summary>

Synchronous: more consistent (replica has data before write confirms), but slower writes.
Asynchronous: faster writes, but replicas may lag.

</details>

<details>
<summary><b>6. What's "read-your-writes" consistency?</b></summary>

After you write, you see your own write on subsequent reads. Often implemented by routing reads to the primary temporarily after a write.

</details>

---

## Sharding

<details>
<summary><b>7. What's the difference between replication and sharding?</b></summary>

Replication: same data on multiple servers (availability, read scaling).
Sharding: different data on different servers (data size, write scaling).

</details>

<details>
<summary><b>8. What makes a good shard key?</b></summary>

High cardinality, evenly distributed, present in most queries, doesn't cause hotspots.

</details>

<details>
<summary><b>9. Why are cross-shard queries problematic?</b></summary>

They must hit multiple shards and aggregate results. This is slow and negates the benefits of sharding.

</details>

---

## Database Selection

<details>
<summary><b>10. When would you choose PostgreSQL over MongoDB?</b></summary>

When you need relationships, complex joins, ACID transactions, or data integrity constraints. PostgreSQL is also a good default when unsure.

</details>

<details>
<summary><b>11. What is Redis typically used for?</b></summary>

Caching, sessions, rate limiting  -  fast key-value operations. Usually alongside a primary database, not as the primary store.

</details>

<details>
<summary><b>12. Why not use many specialized databases?</b></summary>

Operational complexity. Each database requires deployment, monitoring, backups, and expertise. Start with fewer and add only when benefits justify the overhead.

</details>

---

## Scoring

| Score | Assessment |
|-------|------------|
| 10-12 | Solid understanding. Move on. |
| 7-9 | Good grasp. Review weak spots. |
| < 7 | Re-read the chapters. |

---

[Review flashcards](flashcards.md) | [Continue to Level 5](../05-distributed-systems/README.md)
