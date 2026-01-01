# Level 3 Flashcards

Quick reference for building blocks.

---

## Load Balancers

| Front | Back |
|-------|------|
| What's a load balancer? | Distributes traffic across multiple servers. |
| Layer 4 vs Layer 7? | L4: routes by IP/port. L7: routes by HTTP content (URL, headers). |
| Common load balancing algorithms? | Round-robin, least connections, IP hash, weighted. |
| Why avoid sticky sessions? | Reduces flexibility and availability. Better to be stateless. |

---

## Caching

| Front | Back |
|-------|------|
| Why cache? | Speed (faster than source), reduced load, cost savings. |
| Cache-aside pattern? | Check cache → miss → fetch from source → store in cache. |
| Write-through pattern? | Write to cache and source together. Cache always current. |
| What's a cache stampede? | Many requests hit source simultaneously when cache expires. |
| LRU eviction? | Remove least recently used items when cache is full. |

---

## CDNs

| Front | Back |
|-------|------|
| What's a CDN? | Distributed servers that cache content near users. |
| Why use CDN? | Lower latency, reduced origin load, better availability. |
| How to invalidate CDN cache? | Version URLs with content hash. New deploy = new URLs. |

---

## Proxies and Gateways

| Front | Back |
|-------|------|
| Reverse proxy vs forward proxy? | Reverse: in front of servers. Forward: in front of clients. |
| What's an API gateway? | Reverse proxy for APIs with auth, rate limiting, routing. |
| What's SSL termination? | Proxy handles HTTPS, backends get plain HTTP. |
| BFF pattern? | Backend for Frontend  -  different gateways for different clients. |

---

## Rate Limiting

| Front | Back |
|-------|------|
| Why rate limit? | Protect systems, fair usage, security, cost control. |
| Token bucket algorithm? | Tokens added at fixed rate. Each request uses a token. Allows bursts. |
| HTTP 429? | Too Many Requests  -  client has been rate limited. |
| Why Redis for rate limiting? | Shared counter across multiple app servers. |

---

## Consistent Hashing

| Front | Back |
|-------|------|
| What problem does it solve? | Minimizes data movement when servers are added/removed. |
| Virtual nodes? | Multiple positions per physical server for even distribution. |
| When not needed? | When server count is fixed and rarely changes. |

---

## Bloom Filters

| Front | Back |
|-------|------|
| Key property? | Possible false positives, no false negatives. |
| When to use? | Filter out definite negatives before expensive checks. |
| Can you delete? | No (standard bloom filter). Use counting bloom filter for deletion. |

---

## Distributed Logging

| Front | Back |
|-------|------|
| Why centralize logs? | Can't SSH into 100 servers. Need one searchable place. |
| Structured vs unstructured? | JSON logs indexable and searchable. Plain text requires parsing. |
| Common stack? | ELK (Elasticsearch, Logstash, Kibana) or Loki + Grafana. |
| Why trace_id? | Correlate logs across services for a single request. |
| Hot-warm-cold? | Recent logs on fast storage, older on slow, archive to cheapest. |

---

## Search

| Front | Back |
|-------|------|
| Inverted index? | Maps words to documents containing them. |
| Why not SQL LIKE? | Slow (table scan), no ranking, no typo tolerance. |
| Common search engine? | Elasticsearch (alongside primary database). |

---

## Object Storage

| Front | Back |
|-------|------|
| S3 concepts? | Buckets (containers) and objects (files with keys). |
| Presigned URL? | Temporary access to private object. |
| Storage classes? | Standard, Infrequent Access, Glacier (trade access speed for cost). |

---

[Continue to Level 4](../04-data-layer/README.md)
