# Choosing a Database

How to pick the right database for your needs, because there is no universally "best" database.

---

## The Starting Point

Before choosing a database, you need to understand what you're storing and how you'll access it.

**Questions to answer:**
1. What's the data structure? (Relational? Document? Key-value?)
2. How much data? (GB? TB? PB?)
3. What's the read/write ratio?
4. What are the access patterns? (By ID? By query? Full-text search?)
5. What consistency guarantees do you need?
6. What's your team's expertise?

The answers guide the choice.

---

## Categories of Databases

### Relational (SQL)

Tables with rows and columns. Strong schema. ACID transactions.

**Examples:** PostgreSQL, MySQL, SQLite

**Best for:**
- Data with relationships (users, orders, products)
- Complex queries (joins, aggregations)
- Transactions that must be atomic
- Unknown or evolving query patterns

**Typical scale:** To terabytes. Single server handles most applications.

### Document

Store JSON-like documents. Flexible schema. Each document can have different fields.

**Examples:** MongoDB, CouchDB, Amazon DocumentDB

**Best for:**
- Data that's naturally document-shaped
- Schema flexibility needed
- Accessing whole documents by ID or simple queries
- When data doesn't fit neatly into tables

**Typical scale:** To terabytes. Shards well horizontally.

### Key-Value

Simple: key → value. Very fast. No complex queries.

**Examples:** Redis, DynamoDB (as K-V), etcd

**Best for:**
- Caching
- Session storage
- Configuration
- Where you always access by key

**Typical scale:** Redis: hundreds of GB with persistence. DynamoDB: virtually unlimited.

### Wide-Column

Columns can vary by row. Good for sparse data and time-series.

**Examples:** Cassandra, HBase, ScyllaDB

**Best for:**
- Time-series data
- High write throughput
- Known query patterns (designed for specific access)
- Very large scale

**Typical scale:** Petabytes.

### Graph

Nodes and edges. Optimized for traversing relationships.

**Examples:** Neo4j, Amazon Neptune, ArangoDB

**Best for:**
- Social networks
- Recommendation engines
- Fraud detection
- Any problem about relationships between entities

**Typical scale:** Billions of nodes.

### Time-Series

Optimized for timestamped data points.

**Examples:** InfluxDB, TimescaleDB, Prometheus

**Best for:**
- Metrics and monitoring
- IoT sensor data
- Financial data ticks
- Log storage

**Typical scale:** Billions of data points.

### Search

Full-text search with relevance scoring.

**Examples:** Elasticsearch, OpenSearch, Algolia

**Best for:**
- Search functionality
- Log analysis
- Faceted search (filter by category)

**Typical scale:** Terabytes.

---

## Common Choices by Use Case

| Use Case | Common Choice | Why |
|----------|---------------|-----|
| General web app | PostgreSQL | Versatile, reliable, full-featured |
| Content/blog | PostgreSQL or MongoDB | Depends on structure |
| Caching | Redis | Fast, feature-rich |
| Session storage | Redis | Fast, simple |
| User activity feed | Redis (sorted sets) or Cassandra | Ordered, high write |
| Metrics and monitoring | InfluxDB or Prometheus | Designed for time-series |
| Search | Elasticsearch | Full-text with relevance |
| Social graph | Neo4j or PostgreSQL | Depends on query complexity |
| E-commerce | PostgreSQL | Transactions, relationships |
| IoT telemetry | TimescaleDB or Cassandra | High write, time-series |
| Configuration store | etcd or Consul | Consistent, distributed |

---

## PostgreSQL as Default

For most applications, **PostgreSQL is the right default choice**.

**Why:**
- Full SQL with advanced features
- Strong ACID guarantees
- JSON support (can be document-store-like when needed)
- Full-text search (basic, can replace Elasticsearch for simple cases)
- Extensions for everything (PostGIS for geo, TimescaleDB for time-series)
- Excellent tooling and ecosystem
- Free and open source
- Works from small to large scale

**When not:**
- Extreme scale (billions of writes/day)
- Very specific use case (graph, time-series, search)
- Team strongly prefers something else

Start with PostgreSQL. Add specialized databases when you have a specific need it can't meet.

---

## Managed vs. Self-Managed

### Managed (Database as a Service)

Cloud provider runs the database.

**Examples:** AWS RDS, Cloud SQL, Atlas (MongoDB), PlanetScale

**Pros:**
- No server management
- Automated backups, updates
- Built-in high availability
- Easy scaling
- Focus on application, not operations

**Cons:**
- More expensive at scale
- Less control
- Potential vendor lock-in

### Self-Managed

You run the database on your own servers/VMs.

**Pros:**
- Full control
- Can be cheaper at scale
- No cloud dependency

**Cons:**
- You handle everything: backups, updates, failover, security
- Requires database expertise
- On-call for database issues

**Recommendation:** Start with managed unless you have specific reasons not to. The operational burden of databases is significant.

---

## Scaling Considerations

### Vertical Scaling (Scale Up)

Bigger server: more CPU, memory, disk.

**Works for:** Most databases, most use cases. A large PostgreSQL instance handles a lot.

### Read Replicas

Add read-only copies for read traffic.

**Works for:** Read-heavy workloads. Most relational databases support replicas.

### Sharding (Scale Out)

Split data across multiple database instances.

**Works for:** When data or write volume exceeds single-server capacity.

**Trade-offs:** Much more complex. Consider only when necessary.

### Which to Consider First

1. Optimize queries and indexes (free performance)
2. Add caching (often biggest impact)
3. Vertical scaling (easy, effective)
4. Read replicas (if read-heavy)
5. Sharding (last resort for most)

---

## Making the Decision

### Decision Framework

1. **Start with your data model.** Does it fit tables? Documents? Needs relationships?

2. **Consider your query needs.** Complex queries? Just by ID? Full-text search?

3. **Estimate scale.** How much data? How many operations/sec? Now and in 2-3 years?

4. **Assess consistency needs.** Must have ACID? Eventual consistency okay?

5. **Factor in your team.** Skills, preferences, on-call capability?

6. **Consider ops burden.** Managed or self-hosted? Who responds when it breaks?

### Common Paths

**Building a web app and not sure:**
→ PostgreSQL (managed)

**Need caching:**
→ Add Redis (managed, like ElastiCache)

**Need search:**
→ Add Elasticsearch (or PostgreSQL full-text for simple cases)

**Very high write volume for events/analytics:**
→ Kafka for ingestion → Clickhouse or Cassandra for storage

**Graph-heavy queries:**
→ Evaluate if PostgreSQL with recursive CTEs is enough. If not, Neo4j.

---

## Multi-Database Architecture

Most production systems use multiple databases.

```
┌─────────────────────────────────────────────────────────────┐
│                     Application                             │
└─────────────────────────────────────────────────────────────┘
                            │
       ┌────────────────────┼────────────────────┐
       │                    │                    │
       ▼                    ▼                    ▼
┌──────────────┐    ┌──────────────┐    ┌──────────────┐
│ PostgreSQL   │    │    Redis     │    │Elasticsearch │
│(Primary data)│    │  (Caching)   │    │  (Search)    │
└──────────────┘    └──────────────┘    └──────────────┘
```

Each database does what it's best at:
- PostgreSQL: source of truth, transactions
- Redis: fast access, sessions, caching
- Elasticsearch: search functionality

### Be Intentional

Every database adds:
- Operational complexity
- Data sync concerns (keeping multiple stores consistent)
- Another thing to learn and manage

Only add a new database when you have a clear need it addresses.

---

## Common Mistakes

**Choosing based on hype.** Using MongoDB because it's "modern" when PostgreSQL would work better for your relational data.

**Premature optimization.** Choosing Cassandra for web-scale when you have 100 users.

**Ignoring team skills.** Picking a database nobody knows how to operate.

**Not using managed.** Spending time on database operations when you should be building product.

**One database for everything.** Forcing time-series data into PostgreSQL when you have 10 billion data points and need a time-series database.

**Not planning for growth.** But also: over-planning for growth that may never come. Balance.

---

## What An Experienced Senior Engineer Thinks About

**Total cost of ownership.** Not just hosting cost. Operational overhead, engineering time, learning curve, hiring for specific skills.

**Migration path.** If this choice is wrong, how hard is it to change? Plan for how you'd migrate if needed.

**Failure modes.** How does this database fail? What's the blast radius? What's the recovery process?

**Vendor and community.** Is this actively maintained? Is the community healthy? Will there be support in 5 years?

**Polyglot persistence.** Using the right tool for each job, but balancing against the cost of managing multiple systems.

---

## Vibe Engineering Guide

When prompting about database choice:

**Less useful:**
> "Which database should I use?"

**More useful:**
> "I'm building a SaaS application with:
> - Users, organizations, projects (relational data)
> - Activity feeds (ordered events)
> - Search across projects (full-text)
> - Need transactions for billing operations
> - Team of 3, first product, expect 10K users initially
>
> Should I use PostgreSQL for everything, or add specialized databases? What are the trade-offs?"

**For scaling decisions:**
> "Our PostgreSQL database is 500GB with 5,000 queries/sec. We're approaching I/O limits on our largest RDS instance. What are our options before considering sharding?"

---

## Quick Check

<details>
<summary><b>When would you choose a document database over relational?</b></summary>

When data is naturally document-shaped (varied structure between records), you need schema flexibility (rapidly evolving data model), or your access patterns are primarily by document ID without complex joins.

</details>

<details>
<summary><b>Why is PostgreSQL a good default?</b></summary>

It handles relational data with full SQL, supports JSON for flexibility, has basic full-text search, is reliable with ACID transactions, has excellent tooling, and scales well for most applications. It's versatile enough for many use cases.

</details>

<details>
<summary><b>Why use managed databases over self-managed?</b></summary>

Managed handles operations: backups, updates, high availability, scaling. This lets you focus on your application instead of database administration. Worth the cost for most teams without dedicated database expertise.

</details>

<details>
<summary><b>When should you add a second database type?</b></summary>

When you have a specific need that your primary database can't meet well: search (Elasticsearch), caching (Redis), time-series (InfluxDB). Don't add databases speculatively - each one adds operational burden.

</details>

<details>
<summary><b>What's the first scaling approach to try?</b></summary>

Optimize queries and indexes first (free). Then caching (often biggest impact). Then vertical scaling (bigger instance). Read replicas if read-heavy. Sharding is last resort.

</details>

---

Next: [Level 5: Distributed Systems](../05-distributed-systems/README.md)
