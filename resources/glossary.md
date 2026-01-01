# System Design Glossary

Comprehensive definitions of system design terms and concepts.

---

## A

**Active-Active:** Architecture where multiple data centers or regions simultaneously handle traffic. More complex but provides higher availability than active-passive.

**Active-Passive:** Architecture where one data center handles traffic while another stands by as backup. Simpler than active-active but wastes standby resources.

**Adaptive Bitrate Streaming (ABR):** Video streaming technique where the player automatically adjusts video quality based on network conditions.

**API Gateway:** Entry point for client requests that handles cross-cutting concerns like authentication, rate limiting, and routing to backend services.

**Asynchronous Processing:** Handling requests without blocking the caller. Work is queued and processed later, allowing immediate response.

**At-Least-Once Delivery:** Message delivery guarantee where messages may be delivered multiple times. Requires idempotent consumers.

**At-Most-Once Delivery:** Message delivery guarantee where messages are delivered zero or one time. No duplicates but may lose messages.

**Availability:** The percentage of time a system is operational and accessible. Often expressed in "nines" (99.9% = three nines).

---

## B

**Back Pressure:** Mechanism to slow down producers when consumers can't keep up, preventing system overload.

**Backoff (Exponential):** Retry strategy where wait time increases exponentially between attempts (1s, 2s, 4s, 8s...).

**Bloom Filter:** Probabilistic data structure for set membership. Space-efficient, allows false positives but never false negatives.

**B-Tree:** Balanced tree structure used in databases for indexing. Good for range queries and random reads.

---

## C

**Cache-Aside (Lazy Loading):** Caching pattern where application checks cache first, loads from database on miss, then populates cache.

**Cache Hit Ratio:** Percentage of requests served from cache. Higher ratio = better cache effectiveness.

**CAP Theorem:** In a distributed system, you can only guarantee two of: Consistency, Availability, Partition tolerance.

**CDN (Content Delivery Network):** Geographically distributed servers that cache content close to users for faster delivery.

**Circuit Breaker:** Pattern to stop calling a failing service, preventing cascade failures. Has closed, open, and half-open states.

**Consistent Hashing:** Hashing technique where adding/removing nodes only affects a small portion of keys. Used for distributed caching and databases.

**CQRS (Command Query Responsibility Segregation):** Pattern separating read and write models for different optimization.

---

## D

**Dead Letter Queue (DLQ):** Queue where messages go after failing processing multiple times. Used for debugging and recovery.

**Denormalization:** Duplicating data to optimize read performance at the cost of write complexity.

**Durability:** Guarantee that committed data survives system failures. Part of ACID properties.

---

## E

**Eventual Consistency:** Consistency model where reads may return stale data temporarily, but all replicas converge eventually.

**Event Sourcing:** Storing state as a sequence of events rather than current state. Enables replay and audit trails.

**Exactly-Once Delivery:** Message delivery guarantee where each message is processed exactly once. Hardest to achieve.

---

## F

**Failover:** Automatic switching to a backup system when the primary fails.

**Fan-Out:** Pattern where one input triggers multiple outputs. "Fan-out on write" pre-computes for all recipients.

**Fencing Token:** Monotonically increasing number used to prevent stale operations from taking effect after lock expiry.

---

## G

**Geohash:** Encoding system that converts geographic coordinates into a string where nearby locations share prefixes.

**Gossip Protocol:** Decentralized communication protocol where nodes periodically exchange state information with random peers.

---

## H

**Heartbeat:** Periodic signal sent to indicate a component is alive and healthy.

**Hinted Handoff:** Technique where writes for an unavailable node are temporarily stored elsewhere and forwarded when it recovers.

**Horizontal Scaling (Scale Out):** Adding more machines to handle load. Preferred for web-scale systems.

**Hot Spot:** Uneven load distribution where some resources handle disproportionate traffic.

---

## I

**Idempotency:** Property where performing an operation multiple times produces the same result as performing it once.

**Idempotency Key:** Unique identifier sent with requests to prevent duplicate processing on retries.

**Index:** Data structure that speeds up database queries at the cost of write performance and storage.

**Inverted Index:** Index mapping content (terms) to locations (documents). Core of full-text search engines.

---

## J

**Jitter:** Random variation added to retry timing to prevent thundering herd problem.

---

## K

**Key-Value Store:** Simple database storing data as key-value pairs. Examples: Redis, DynamoDB.

---

## L

**Latency:** Time taken to complete an operation. Usually measured in milliseconds.

**Leader Election:** Process by which distributed nodes choose a single leader for coordination.

**Load Balancer:** Component that distributes incoming requests across multiple servers.

**LSM Tree (Log-Structured Merge-Tree):** Storage structure optimized for write-heavy workloads. Used by Cassandra, RocksDB.

---

## M

**Message Queue:** System for asynchronous communication between services. Decouples producers and consumers.

**Microservices:** Architecture pattern where application is split into small, independently deployable services.

**Monolith:** Architecture where entire application is a single deployable unit.

**MTBF (Mean Time Between Failures):** Average time between system failures. Higher is better.

**MTTR (Mean Time To Recovery):** Average time to restore service after failure. Lower is better.

---

## N

**N+1 Query Problem:** Performance anti-pattern where fetching N items requires N+1 database queries.

**NoSQL:** Databases that don't use traditional relational tables. Includes document, key-value, wide-column, graph.

---

## O

**Optimistic Locking:** Concurrency control using version numbers instead of locks. Detects conflicts at commit time.

**Outbox Pattern:** Reliability pattern where database changes and events are written atomically to local storage, then published.

---

## P

**Partition (Shard):** Subset of data in a distributed database. Enables horizontal scaling.

**Partition Key:** Field used to determine which partition stores a piece of data.

**Partition Tolerance:** System's ability to continue operating despite network partitions between nodes.

**P50, P95, P99 (Percentiles):** Latency measurements showing the value below which X% of observations fall.

**Polyglot Persistence:** Using different database types for different parts of an application based on requirements.

**Pub/Sub (Publish/Subscribe):** Messaging pattern where publishers send messages to topics, subscribers receive relevant messages.

---

## Q

**Quorum:** Minimum number of nodes that must agree for an operation to succeed. Often majority (N/2 + 1).

**Quota:** Limits on resource usage, often per user or per API key.

---

## R

**Raft:** Consensus algorithm designed to be understandable. Used by etcd, CockroachDB.

**Rate Limiting:** Controlling the rate of requests to protect systems from overload.

**Read Replica:** Database copy that handles read queries, reducing load on the primary.

**Rebalancing:** Redistributing data across nodes when cluster membership changes.

**Replication:** Copying data to multiple nodes for durability and availability.

**Replication Factor:** Number of copies of data maintained across nodes.

**Replication Lag:** Delay between a write to the primary and when it appears on replicas.

**RPO (Recovery Point Objective):** Maximum acceptable data loss, measured in time.

**RTO (Recovery Time Objective):** Maximum acceptable downtime after a failure.

---

## S

**Saga:** Pattern for managing distributed transactions using a sequence of local transactions with compensating actions.

**Service Discovery:** Mechanism for services to find and communicate with each other dynamically.

**Service Mesh:** Infrastructure layer handling service-to-service communication, including discovery, load balancing, and security.

**Sharding:** See Partition.

**SLA (Service Level Agreement):** Contract defining expected service quality (uptime, latency, etc.).

**SLI (Service Level Indicator):** Metric used to measure service level (e.g., request latency, error rate).

**SLO (Service Level Objective):** Target value for an SLI (e.g., 99.9% availability).

**Split-Brain:** Situation where a distributed system splits into multiple groups, each believing it's the primary.

**SSTable (Sorted String Table):** Immutable, sorted file format used by LSM trees.

**Sticky Sessions:** Load balancing approach where a user's requests always go to the same server.

**Strong Consistency:** Guarantee that all reads see the most recent write.

**Synchronous Replication:** Writing to all replicas before acknowledging success. Ensures consistency but adds latency.

---

## T

**Throughput:** Rate at which a system processes operations. Often measured in requests per second (RPS).

**Timeout:** Maximum time to wait for an operation before considering it failed.

**Token Bucket:** Rate limiting algorithm where tokens accumulate over time and are consumed by requests.

**Tombstone:** Marker indicating deleted data in systems that don't immediately remove records (LSM trees, Cassandra).

**Trie (Prefix Tree):** Tree structure for efficient prefix matching. Used in autocomplete systems.

**TTL (Time To Live):** Duration after which data expires automatically.

---

## U

**USE Method:** Approach to resource monitoring: Utilization, Saturation, Errors.

---

## V

**Vector Clock:** Data structure tracking causality in distributed systems. Detects concurrent updates.

**Vertical Scaling (Scale Up):** Adding more resources (CPU, RAM) to existing machines.

**Virtual Node (vNode):** Multiple hash ring positions for a single physical node. Improves load distribution in consistent hashing.

---

## W

**WAL (Write-Ahead Log):** Log of changes written before applying to main storage. Ensures durability and enables recovery.

**WebSocket:** Protocol for full-duplex communication over a single TCP connection.

**Write-Ahead Logging:** See WAL.

**Write-Through Cache:** Caching pattern where writes go to both cache and database simultaneously.

---

## X-Z

**Zero-Copy:** Optimization where data is transferred without copying between kernel and user space.

**Zookeeper:** Coordination service for distributed systems. Handles configuration, naming, synchronization.

---

*This glossary covers the key terms used throughout this system design guide. For deeper explanations, see the relevant chapters.*
