# Level 3 Quiz

Test your understanding of the building blocks.

---

## Load Balancers

<details>
<summary><b>1. What's the difference between Layer 4 and Layer 7 load balancing?</b></summary>

Layer 4 routes based on IP/port without inspecting content. Layer 7 can route based on HTTP content (URLs, headers). Layer 7 is more flexible but has more overhead.

</details>

<details>
<summary><b>2. Why avoid sticky sessions when possible?</b></summary>

They reduce flexibility, hurt availability (if "their" server dies), and complicate scaling. Better to make services stateless so any server can handle any request.

</details>

---

## Caching

<details>
<summary><b>3. What's the difference between cache-aside and write-through?</b></summary>

Cache-aside: application checks cache, on miss fetches from source and stores in cache.
Write-through: cache is updated when source is written, so cache always has current data.

</details>

<details>
<summary><b>4. What's a cache stampede?</b></summary>

When cache expires and many requests simultaneously try to rebuild it from source, potentially overwhelming the database.

</details>

<details>
<summary><b>5. What does LRU stand for and why is it useful?</b></summary>

Least Recently Used. When cache is full, remove the entry that hasn't been accessed in the longest time. Usually a good default eviction policy.

</details>

---

## CDNs

<details>
<summary><b>6. Why does a CDN improve latency?</b></summary>

Content is served from servers physically closer to users. Less distance = less time for data to travel.

</details>

<details>
<summary><b>7. How do you invalidate CDN cache after a new deployment?</b></summary>

Best approach: version your asset URLs with content hashes (app.abc123.js). New deploys create new URLs. Old cached files are never served because they're never requested.

</details>

---

## Proxies and Gateways

<details>
<summary><b>8. What's the difference between a forward and reverse proxy?</b></summary>

Forward proxy: sits in front of clients, handles outbound traffic.
Reverse proxy: sits in front of servers, handles inbound traffic.
In system design, we usually mean reverse proxy.

</details>

<details>
<summary><b>9. What's SSL termination?</b></summary>

The proxy handles HTTPS encryption/decryption so backend services don't have to. Traffic between proxy and backends is HTTP (within your network).

</details>

<details>
<summary><b>10. Why is an API gateway potentially a single point of failure?</b></summary>

All traffic goes through it. If it fails, nothing works. Solution: deploy multiple gateway instances behind a load balancer.

</details>

---

## Rate Limiting

<details>
<summary><b>11. Why use token bucket over fixed window?</b></summary>

Token bucket allows controlled bursts while maintaining an average rate. Fixed window allows double the rate at window boundaries.

</details>

<details>
<summary><b>12. Why do you need Redis for rate limiting across multiple servers?</b></summary>

Each server needs to see the same counter. Without shared state, users can hit the limit N times across N servers.

</details>

---

## Consistent Hashing

<details>
<summary><b>13. What problem does consistent hashing solve?</b></summary>

When using simple hash mod N, adding or removing a server changes where most keys map. Consistent hashing minimizes data movement when nodes change.

</details>

<details>
<summary><b>14. What are virtual nodes?</b></summary>

Multiple positions on the hash ring for each physical server. Evens out distribution and handles different server capacities.

</details>

---

## Bloom Filters

<details>
<summary><b>15. What's the key property of bloom filters?</b></summary>

No false negatives, possible false positives. "Not in set" is definite. "Maybe in set" might be wrong.

</details>

---

## Distributed Logging

<details>
<summary><b>16. Why use structured logging (JSON) instead of plain text?</b></summary>

Structured logs have fields that can be indexed and searched efficiently. Plain text requires parsing or regex, which is slow and error-prone.

</details>

<details>
<summary><b>17. What's the purpose of trace_id in logs?</b></summary>

Correlate logs across multiple services. One request touches many services, trace_id connects all their logs for debugging.

</details>

<details>
<summary><b>18. What's hot-warm-cold architecture for logs?</b></summary>

Recent logs on fast storage (SSD), older logs on slower storage (HDD), archive to cheapest storage (S3). Balances search speed with cost.

</details>

---

## Search

<details>
<summary><b>19. What's an inverted index?</b></summary>

Mapping from words to documents containing them. Enables fast "which documents contain this word" lookups.

</details>

---

## Object Storage

<details>
<summary><b>20. What's a presigned URL?</b></summary>

A URL with embedded credentials allowing temporary access to a private object. Used for direct uploads/downloads without exposing credentials.

</details>

---

## Scoring

| Score | Assessment |
|-------|------------|
| 17-20 | Solid understanding. Move on. |
| 12-16 | Good grasp. Review weak spots. |
| < 12 | Re-read the chapters. |

---

[Review flashcards](flashcards.md) | [Continue to Level 4](../04-data-layer/README.md)
