# Recommended Reading

Books, blogs, and resources worth your time.

---

## Essential Books

### For Everyone

**Designing Data-Intensive Applications** by Martin Kleppmann  
The standard reference for system design. Covers databases, distributed systems, and data processing. Dense but thorough. Read this once, reference forever.

**System Design Interview Vol 1 & 2** by Alex Xu  
Practical and accessible. Works through specific system designs (TinyURL, Twitter, rate limiter, etc.). Good for interview prep or general understanding.

### For Deeper Dives

**Building Microservices** by Sam Newman  
Focused on microservices architecture. Covers decomposition, communication, deployment, and organizational impacts.

**Database Internals** by Alex Petrov  
How databases actually work under the hood. Storage engines, distributed database concepts, consensus.

**Understanding Distributed Systems** by Roberto Vitillo  
Practical distributed systems concepts. More accessible than academic papers.

**Site Reliability Engineering** by Google  
How Google runs production systems. Covers monitoring, alerting, incident response, and reliability practices.

**Staff Engineer** by Will Larson  
Not about system design directly, but about thinking at the senior/staff level. Good for understanding the broader context.

---

## Engineering Blogs

Learn from how real companies solve problems:

### Infrastructure & Scale
- **[Netflix Tech Blog](https://netflixtechblog.com/)**  -  Streaming at scale, chaos engineering
- **[Uber Engineering](https://www.uber.com/blog/engineering/)**  -  Real-time systems, geospatial
- **[Meta Engineering](https://engineering.fb.com/)**  -  Graph systems, caching at scale
- **[LinkedIn Engineering](https://engineering.linkedin.com/blog)**  -  Kafka (they created it), data infrastructure

### Payments & Reliability
- **[Stripe Engineering](https://stripe.com/blog/engineering)**  -  Payments, API design, reliability
- **[Shopify Engineering](https://shopify.engineering/)**  -  E-commerce scale, Ruby at scale

### Cloud & Infrastructure
- **[AWS Architecture Blog](https://aws.amazon.com/blogs/architecture/)**  -  Cloud architecture patterns
- **[Cloudflare Blog](https://blog.cloudflare.com/)**  -  Networking, CDN, edge computing
- **[Google Cloud Blog](https://cloud.google.com/blog/)**  -  Cloud-native patterns

### Databases
- **[PlanetScale Blog](https://planetscale.com/blog)**  -  MySQL, Vitess, database scaling
- **[CockroachDB Blog](https://www.cockroachlabs.com/blog/)**  -  Distributed SQL

---

## Key Papers

Original papers for foundational systems:

| Paper | Topic | Why Read |
|-------|-------|----------|
| **Dynamo** (Amazon, 2007) | Key-value store | Eventual consistency, consistent hashing |
| **Google File System** (2003) | Distributed storage | Chunk-based storage, replication |
| **MapReduce** (Google, 2004) | Batch processing | Distributed computation |
| **Raft** (2014) | Consensus | Leader election, more readable than Paxos |
| **Kafka** (LinkedIn, 2011) | Event streaming | Log-based messaging |
| **Spanner** (Google, 2012) | Global database | Globally consistent transactions |
| **TAO** (Facebook, 2013) | Social graph | Graph caching at scale |
| **Cassandra** (Facebook, 2010) | Wide-column store | Partition tolerance, eventual consistency |

These are academic papers - denser than blog posts. Worth reading if you want deep understanding.

---

## Courses & Videos

### Free
- **MIT 6.824: Distributed Systems**  -  Graduate-level, labs with real implementations
- **Martin Kleppmann's Distributed Systems lectures**  -  Based on his book

### Paid
- **Grokking the System Design Interview** (Educative)  -  Structured interview prep
- **ByteByteGo** (Alex Xu)  -  Video explanations of system designs

---

## Practice Tools

**Excalidraw**  -  Free diagramming tool, great for sketching designs

**Draw.io**  -  More structured diagrams

**Miro**  -  Collaborative whiteboarding

**GitHub**  -  Read open source implementations

---

## Podcasts

- **Software Engineering Daily**  -  Deep dives on specific technologies
- **The Changelog**  -  Developer interviews and news
- **CoRecursive**  -  Stories behind software

---

## Reading Order Suggestion

### If You're Starting Out
1. System Design Interview Vol 1 (get the patterns)
2. This repository (fill in concepts)
3. Engineering blogs (see real examples)

### If You Want Depth
1. Designing Data-Intensive Applications (foundational)
2. Specific papers for topics you're interested in
3. Database Internals or Building Microservices

### If Preparing for Interviews
1. This repository (concepts + designs)
2. System Design Interview Vol 1 & 2 (practice)
3. Cheat sheets for quick review

---

[← Home](../README.md)
