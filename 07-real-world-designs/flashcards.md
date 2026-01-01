# Level 7 Flashcards

Quick reference for real-world system designs.

---

## URL Shortener

| Front | Back |
|-------|------|
| Short code approaches? | Hash-based (collision risk) or counter-based (sequential). |
| 301 vs 302? | 301: cached, fewer requests. 302: every visit tracked. |

---

## Rate Limiter

| Front | Back |
|-------|------|
| Token bucket? | Tokens added at rate, consumed per request. Allows bursts. |
| Sliding window? | Counts requests in rolling time window. Smoother than fixed. |

---

## Chat System

| Front | Back |
|-------|------|
| Why WebSocket? | Bidirectional, server can push without client polling. |
| Connection routing? | Registry maps user_id to server holding their connection. |

---

## Notification System

| Front | Back |
|-------|------|
| Why separate workers? | Different providers, rates, formats per channel. |
| Why priority queue? | Critical alerts before promotional messages. |

---

## News Feed

| Front | Back |
|-------|------|
| Fan-out on write? | Pre-compute feed for followers when post created. |
| Celebrity problem? | Millions of writes per post. Use hybrid: read for celebrities. |

---

## E-Commerce

| Front | Back |
|-------|------|
| Why reserve inventory? | User might abandon. Reserve temporarily, release on timeout. |
| Overselling prevention? | Optimistic locking: check and update atomically. |

---

## Payment System

| Front | Back |
|-------|------|
| Idempotency key? | Unique identifier prevents double charge on retry. |
| Authorize vs capture? | Authorize reserves funds. Capture actually charges. |

---

## Video Streaming

| Front | Back |
|-------|------|
| Adaptive bitrate? | Multiple qualities, player switches based on network. |
| Why CDN critical? | Large files, global delivery, bandwidth costs. |

---

## Search Engine

| Front | Back |
|-------|------|
| Inverted index? | Maps terms to documents containing them. Core of search. |
| Why sharding? | Distribute index across nodes. Search in parallel. |

---

## Ride-Sharing

| Front | Back |
|-------|------|
| Why ETA, not distance? | Distance doesn't account for traffic. |
| Geohashing? | Convert lat/lng to string, nearby locations share prefix. |

---

## Distributed File Storage

| Front | Back |
|-------|------|
| Why chunk files? | Resumable uploads, delta sync, deduplication. |
| Content-addressable? | Use content hash as ID. Same content = stored once. |

---

## Web Crawler

| Front | Back |
|-------|------|
| URL Frontier? | Queue with politeness (per-host rate limit) and priority. |
| Spider trap? | Site generating infinite URLs. Limit depth, detect patterns. |
| Why Bloom filter? | Space-efficient dedup for billions of URLs. |

---

## Key-Value Store

| Front | Back |
|-------|------|
| Consistent hashing? | Add/remove nodes moves minimal data. Ring-based. |
| W + R > N? | Quorums overlap, guarantees read sees latest write. |
| Hinted handoff? | Write to backup when replica down, forward when it recovers. |

---

## Autocomplete

| Front | Back |
|-------|------|
| Why trie? | O(L) prefix lookup. Shares storage for common prefixes. |
| Why top-K at each node? | Avoid traversing subtree. Precomputed suggestions. |
| Why debounce? | Reduce server load. One query per pause, not per keystroke. |

---

## Metrics & Monitoring

| Front | Back |
|-------|------|
| Why TSDB? | Optimized for time-series: compression, retention, queries. |
| Cardinality problem? | Too many tag combinations = too many time series. |
| Why downsample? | Old data doesn't need second-level precision. Save storage. |

---

## Online/Offline Status

| Front | Back |
|-------|------|
| Heartbeat vs WebSocket? | Heartbeat: polling-based. WebSocket: connection state. |
| Why grace period on disconnect? | Mobile networks are unstable. Avoid false offline. |
| Multi-device? | User is online if ANY device connected. |
| Fan-out problem? | 500 friends = 500 notifications. Use selective push or pull. |

---

## Proximity Service

| Front | Back |
|-------|------|
| Why geohash? | Convert 2D to 1D. Nearby locations share prefix. Indexable. |
| Geohash boundary problem? | Nearby locations on cell boundary have different hashes. Query neighbors. |
| R-Tree? | Balanced tree with bounding rectangles. Used by PostGIS. |
| Why cache proximity queries? | Same popular areas queried repeatedly. |

---

## General

| Front | Back |
|-------|------|
| Design approach? | Requirements → Estimates → Architecture → Deep dive → Trade-offs |

---

[Continue to Level 8](../08-senior-engineer-thinking/README.md)
