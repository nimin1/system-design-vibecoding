# Level 7 Quiz

Test your understanding of real-world system designs.

---

## URL Shortener

<details>
<summary><b>1. What's the difference between 301 and 302 redirects?</b></summary>

301 (permanent): Browser caches, good for SEO, analytics undercount.
302 (temporary): No caching, every visit tracked (better for analytics).

</details>

---

## Rate Limiter

<details>
<summary><b>2. How does token bucket work?</b></summary>

Tokens added at fixed rate. Each request uses a token. Empty bucket = rejected. Allows controlled bursts.

</details>

---

## Chat System

<details>
<summary><b>3. Why use WebSocket for chat?</b></summary>

Persistent, bidirectional connection. Server can push messages without client polling.

</details>

<details>
<summary><b>4. How route messages across servers?</b></summary>

Connection registry (Redis) maps user_id to server. Look up recipient's server, forward message.

</details>

---

## News Feed

<details>
<summary><b>5. What's the celebrity problem?</b></summary>

Fan-out on write causes millions of writes when celebrity posts. Solution: hybrid approach (fan-out on read for celebrities).

</details>

---

## E-Commerce

<details>
<summary><b>6. Why reserve inventory instead of decrementing immediately?</b></summary>

User might abandon checkout. Reserve temporarily, release on timeout.

</details>

---

## Payment System

<details>
<summary><b>7. Why are idempotency keys critical?</b></summary>

Prevent double charges. Retried requests with same key return previous result.

</details>

---

## Video Streaming

<details>
<summary><b>8. What is adaptive bitrate streaming?</b></summary>

Video split into chunks at multiple qualities. Player switches quality based on network conditions.

</details>

---

## Search Engine

<details>
<summary><b>9. What's an inverted index?</b></summary>

Maps terms to documents containing them. Instead of "doc → terms," stores "term → docs."

</details>

---

## Ride-Sharing

<details>
<summary><b>10. Why match on ETA, not just distance?</b></summary>

Distance doesn't account for traffic, one-way streets, or driver direction. ETA is actual travel time.

</details>

---

## Distributed File Storage

<details>
<summary><b>11. Why chunk large files?</b></summary>

Resumable uploads, parallel transfer, delta sync, block-level deduplication.

</details>

---

## Web Crawler

<details>
<summary><b>12. Why can't the URL frontier be a simple FIFO queue?</b></summary>

Need politeness (rate limit per host), prioritization (important pages first), and re-crawling. Multiple queues organized by host and priority.

</details>

<details>
<summary><b>13. What's a spider trap?</b></summary>

Site generating infinite URLs via dynamic parameters. Solutions: limit depth, limit pages per site, detect patterns.

</details>

---

## Key-Value Store

<details>
<summary><b>14. Why use consistent hashing?</b></summary>

Adding or removing nodes only moves data for the affected range, not all data.

</details>

<details>
<summary><b>15. What does W + R > N guarantee?</b></summary>

At least one node in read quorum has the latest write. The quorums overlap, giving strong consistency.

</details>

---

## Autocomplete

<details>
<summary><b>16. Why use a trie for autocomplete?</b></summary>

Optimized for prefix lookups. O(L) time where L is prefix length. Shares storage for common prefixes.

</details>

<details>
<summary><b>17. Why debounce on the client?</b></summary>

Reduces server load. 10 characters typed generates 1 query instead of 10.

</details>

---

## Metrics & Monitoring

<details>
<summary><b>18. What's the cardinality problem?</b></summary>

Each unique name + tags is a time series. High-cardinality tags create millions of series, overwhelming storage.

</details>

<details>
<summary><b>19. Why downsample old data?</b></summary>

Old data rarely needs second-level precision. Store aggregates for long-term, raw for recent. Saves storage and query time.

</details>

---

## Online/Offline Status

<details>
<summary><b>20. Why not mark user offline immediately on disconnect?</b></summary>

Mobile connections are unstable. Brief disconnects would trigger false offline status. Use a grace period (30-60 seconds) before marking offline.

</details>

<details>
<summary><b>21. How handle user with 1M followers going offline?</b></summary>

Don't push to all followers. Options: only push to online friends, use pull model where clients fetch status, or batch updates.

</details>

<details>
<summary><b>22. When is a user "online" with multiple devices?</b></summary>

Online if ANY device is connected. Only offline when ALL devices disconnect.

</details>

---

## Proximity Service

<details>
<summary><b>23. Why not calculate distance to every business?</b></summary>

O(N) per query doesn't scale. With millions of businesses, can't scan all. Spatial indexes prune search to O(log N).

</details>

<details>
<summary><b>24. What's the geohash boundary problem?</b></summary>

Two nearby locations might have different geohash prefixes if on cell boundary. Solution: always query neighboring cells too.

</details>

<details>
<summary><b>25. What precision geohash to use for 5km search?</b></summary>

Geohash length 5 (~5km cells). Query center cell + 8 neighbors to cover search area and handle boundaries.

</details>

---

## Scoring

| Score | Assessment |
|-------|------------|
| 22-25 | Excellent understanding. Ready for interviews. |
| 17-21 | Good grasp. Review weak spots. |
| 12-16 | Reread the designs you missed. |
| < 12 | Go through all designs again. |

---

[Review flashcards](flashcards.md) | [Continue to Level 8](../08-senior-engineer-thinking/README.md)
