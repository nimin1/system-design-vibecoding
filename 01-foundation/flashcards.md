# Level 1 Flashcards

Quick reference for key concepts. Cover the "Back" column and test yourself.

---

## Client-Server

| Front | Back |
|-------|------|
| What is a client? | A program that initiates requests to a server. Examples: browsers, mobile apps. |
| What is a server? | A program that listens for requests and sends responses. |
| What does stateless mean? | The server doesn't remember previous requests. Each request is independent. |
| Why is statelessness good for scaling? | Any server can handle any request, so you can add servers easily. |

---

## Networking

| Front | Back |
|-------|------|
| What does DNS do? | Translates domain names to IP addresses. |
| TCP vs UDP? | TCP guarantees delivery and order. UDP is faster but unreliable. |
| What's a port? | A number identifying which service receives network traffic (e.g., 443 for HTTPS). |
| Why can't we eliminate network latency? | Light speed is fixed. Data traveling across the world takes tens to hundreds of milliseconds. |

---

## HTTP

| Front | Back |
|-------|------|
| HTTP methods for CRUD operations? | GET (read), POST (create), PUT (replace), PATCH (update), DELETE (remove). |
| What does 4xx mean? | Client error, the request was wrong. |
| What does 5xx mean? | Server error, something broke on the server side. |
| 401 vs 403? | 401: not authenticated. 403: authenticated but not authorized. |
| What's idempotent? | An operation that has the same effect whether called once or multiple times. |

---

## Databases

| Front | Back |
|-------|------|
| What is ACID? | Atomicity, Consistency, Isolation, Durability, guarantees for reliable transactions. |
| SQL vs NoSQL? | SQL: tables, relationships, ACID. NoSQL: flexible schemas, often trades consistency for scale. |
| What's an index? | A data structure that speeds up queries. Trade-off: faster reads, slower writes. |
| Primary vs foreign key? | Primary key: unique identifier for a row. Foreign key: reference to another table's primary key. |

---

## Request Journey

| Front | Back |
|-------|------|
| Steps to load a page? | DNS → TCP connection → TLS handshake → HTTP request → server processing → response → render. |
| What's the TCP three-way handshake? | SYN → SYN-ACK → ACK. Establishes a connection before data transfer. |
| What's TTFB? | Time To First Byte, time until the first byte of response arrives. High TTFB indicates server or network issues. |
| Why are repeat visits faster? | Cached DNS, reused connections, cached resources, TLS session resumption. |

---

## Quick Reference

```
HTTP Methods: GET (read), POST (create), PUT (replace), PATCH (update), DELETE (remove)

Status Codes:
  2xx = Success
  3xx = Redirect
  4xx = Client error
  5xx = Server error

Common Ports: 80 (HTTP), 443 (HTTPS), 22 (SSH), 5432 (PostgreSQL), 6379 (Redis)

Request journey: DNS → TCP → TLS → HTTP → Server → Response → Render
```

---

[Continue to Level 2](../02-core-concepts/README.md)
