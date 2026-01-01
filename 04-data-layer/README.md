# Level 4: Data Layer

Deeper into databases and data management at scale.

---

## What's Here

| Chapter | Topic | Key Concepts |
|---------|-------|--------------|
| [Database Indexing](01-database-indexing.md) | How indexes work | B-trees, composite indexes, covering |
| [Replication](02-replication.md) | Copying data | Leader-follower, quorum, lag |
| [Sharding](03-sharding.md) | Splitting data | Partition keys, hotspots, resharding |
| [Choosing a Database](04-choosing-database.md) | Selection framework | SQL vs NoSQL, decision criteria |

---

## Why This Matters

Data is the core of most applications. Poor database decisions are expensive to fix later.

**Common costly mistakes:**
- Missing indexes causing slow queries
- Wrong database type for the access pattern
- Sharding too early or too late
- Not planning for read/write scaling

Understanding these concepts helps you make better choices upfront and identify when you need to scale.

---

## How This Helps with AI Tools

After this section, your prompts can specify data requirements:

**Before:**
> "Create a users table"

**After:**
> "Create a users table with composite index on (status, created_at) for querying active users sorted by signup date. Expect 10M rows, reads 10x writes."

---

## Progress

- [ ] Database Indexing
- [ ] Replication
- [ ] Sharding
- [ ] Choosing a Database
- [ ] [Quiz](quiz.md)
- [ ] [Flashcards](flashcards.md)

---

[← Back to Level 3](../03-building-blocks/README.md) | [Level 5 →](../05-distributed-systems/README.md)
