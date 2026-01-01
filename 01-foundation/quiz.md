# Level 1 Quiz

Test yourself on the foundation concepts. Try to answer each question before revealing the answer.

---

## Client-Server

<details>
<summary><b>1. What's the difference between a client and a server?</b></summary>

A client initiates requests. A server responds to them. The same program can be both, when your server calls an external API, it's acting as a client of that API.

</details>

<details>
<summary><b>2. What does "stateless" mean?</b></summary>

The server doesn't remember previous requests by default. Each request is handled independently. Continuity (like staying logged in) must be implemented explicitly using sessions or tokens.

</details>

<details>
<summary><b>3. Why is statelessness useful for scaling?</b></summary>

If servers don't maintain per-client state, any server can handle any request. This lets you add more servers and distribute load across them without worrying about routing users to "their" server.

</details>

---

## Networking

<details>
<summary><b>4. What does DNS do?</b></summary>

DNS translates domain names (example.com) to IP addresses (93.184.216.34). Your browser can't connect to a server without knowing its IP.

</details>

<details>
<summary><b>5. TCP vs UDP: when would you use each?</b></summary>

TCP guarantees delivery and order. Use it for web pages, APIs, file transfers, anything where data integrity matters.

UDP is faster but doesn't guarantee delivery. Use it for video streaming, gaming, VoIP, where low latency matters more than occasional data loss.

</details>

<details>
<summary><b>6. What's a port?</b></summary>

A port is a number identifying which service on a computer should receive network traffic. Common ports: 80 (HTTP), 443 (HTTPS), 22 (SSH), 5432 (PostgreSQL).

</details>

---

## HTTP and APIs

<details>
<summary><b>7. What HTTP method would you use for each operation?</b></summary>

- Reading data: GET
- Creating new data: POST
- Replacing data entirely: PUT
- Updating part of data: PATCH
- Deleting data: DELETE

</details>

<details>
<summary><b>8. What's the difference between 401 and 403?</b></summary>

401 Unauthorized: "Who are you? Authenticate first."
403 Forbidden: "I know who you are, but you're not allowed to do this."

</details>

<details>
<summary><b>9. Why shouldn't you return 200 OK for errors?</b></summary>

Returning 200 for errors breaks HTTP caching, makes monitoring unreliable, and forces clients to parse the response body to know if something failed. Use proper status codes (4xx for client errors, 5xx for server errors).

</details>

<details>
<summary><b>10. What does "idempotent" mean?</b></summary>

An operation is idempotent if calling it multiple times has the same effect as calling it once. GET, PUT, and DELETE are idempotent. POST is not calling POST twice usually creates two resources.

This matters for retries, you can safely retry idempotent requests after a network failure.

</details>

---

## Databases

<details>
<summary><b>11. What does ACID stand for?</b></summary>

- Atomicity: Transactions fully complete or fully fail
- Consistency: Constraints are always enforced
- Isolation: Concurrent transactions don't interfere
- Durability: Committed data survives crashes

</details>

<details>
<summary><b>12. When might you choose PostgreSQL vs MongoDB?</b></summary>

PostgreSQL (SQL): When you have clear relationships between data, need complex joins, or need ACID transactions.

MongoDB (NoSQL): When your schema changes frequently, data is document-oriented, or you don't need complex joins.

</details>

<details>
<summary><b>13. What's an index and what's the trade-off?</b></summary>

An index is a data structure that speeds up queries by avoiding full table scans. Trade-off: faster reads, but slower writes (the index must be updated too).

</details>

---

## The Request Journey

<details>
<summary><b>14. What are the main steps when you load a webpage?</b></summary>

1. DNS lookup (domain → IP)
2. TCP connection (three-way handshake)
3. TLS handshake (for HTTPS)
4. HTTP request sent
5. Server processes request
6. HTTP response received
7. Browser parses and renders

</details>

<details>
<summary><b>15. What's TTFB and why does it matter?</b></summary>

Time To First Byte, the time from sending the request until receiving the first byte of the response. It includes DNS, connection setup, and server processing time. A high TTFB usually indicates a server-side or network issue.

</details>

<details>
<summary><b>16. Why are repeat visits to a site faster than the first visit?</b></summary>

- DNS results are cached
- Connections may be reused
- Resources may be cached locally
- TLS sessions can be resumed

</details>

---

## Scoring

Count how many you answered correctly before revealing the answer.

| Score | Assessment |
|-------|------------|
| 14-16 | Solid understanding. Move on to Level 2. |
| 10-13 | Good grasp of basics. Review any weak spots. |
| < 10 | Worth rereading the chapters before continuing. |

---

[Review flashcards](flashcards.md) | [Continue to Level 2](../02-core-concepts/README.md)
