# System Design for AI-Assisted Development

A complete guide to using system design knowledge to build better with AI tools.

---

## The Core Insight

AI coding tools are excellent at implementation but need architectural direction from you.

Without system design knowledge:
```
You: "Build me a chat app"
AI: Generic code, missing real-time considerations, no scaling strategy
```

With system design knowledge:
```
You: "Build a chat app with:
- WebSocket connections (use Socket.io)
- Connection registry in Redis for multi-server routing
- Message persistence in PostgreSQL
- Offline message queuing
Show me the architecture first, then the implementation."

AI: Production-quality code matching your requirements
```

The difference is architectural context.

---

## The Mechanism of Prompting: Why Specificity Matters

AI models predict the next token based on probability. When you use vague terms, the probability distribution is flat, the AI picks "average" code. When you use specific architectural constraints, you collapse the probability distribution onto "senior engineer" patterns.

### Level 1: Foundation
**Weak Prompt:**
> "The frontend talks to the backend."

**Deconstructed Senior Prompt:**
> "The frontend calls this REST API over **HTTP/2**. The API then queries PostgreSQL using **connection pooling**."

**Why this works:**
-   **HTTP/2:** Triggers generation of modern server configs (e.g., enabling H2 in Nginx/Node) rather than default HTTP/1.1.
-   **Connection Pooling:** Forces the AI to include libraries like `pg-pool` or configure standard pool sizes, preventing the common "new connection per request" bug.

### Level 2: Core Concepts
**Weak Prompt:**
> "Make it fast and reliable."

**Deconstructed Senior Prompt:**
> "This endpoint needs **< 100ms P99 latency**. We need **99.9% availability**. Expect **80% reads, 20% writes**."

**Why this works:**
-   **P99 Latency:** Signals that you care about outliers. The AI is more likely to suggest efficient algorithms or caching rather than naive loops.
-   **Read/Write Ratio:** This constraint directs the database choice (e.g., adding read replicas) that a generic "fast" prompt would miss.

### Level 3: Building Blocks
**Weak Prompt:**
> "Add caching."

**Deconstructed Senior Prompt:**
> "Add Redis caching with the **cache-aside pattern**. Implement **rate limiting at 100 req/min** using the **token bucket algorithm**."

**Why this works:**
-   **Cache-Aside:** Prevents the AI from hallucinating a complex "Write-Through" cache that is harder to implement. It forces a specific, standard code pattern.
-   **Token Bucket:** Instead of a simple counter (which allows bursts that crash servers), this keywords triggers the use of robust libraries designed for traffic shaping.

### Level 4: Data Layer
**Weak Prompt:**
> "Store the user data."

**Deconstructed Senior Prompt:**
> "Use PostgreSQL with a **composite index on (user_id, created_at)**. separate **read/write paths** using a replica."

**Why this works:**
-   **Composite Index:** Explicitly prevents the "Sort by date is slow" performance bug.
-   **Read/Write Paths:** Forces the code architecture to support multiple database connection strings from day one.

### Level 5: Distributed Systems
**Weak Prompt:**
> "Send a message to the other service."

**Deconstructed Senior Prompt:**
> "Publish order events to **Kafka**. Make the consumer **idempotent** using a **deduplication table**."

**Why this works:**
-   **Idempotent:** This is the magic word for reliability. It stops the AI from writing code that double-charges customers when network retries occur.
-   **Deduplication Table:** Gives the AI the *mechanism* to implement idempotency, ensuring it generates the actual SQL schema for tracking processed events.

### Level 6: Architecture Patterns
**Weak Prompt:**
> "Use microservices."

**Deconstructed Senior Prompt:**
> "Use the **Saga pattern** for the checkout flow with **compensating transactions**. Implement a **BFF (Backend for Frontend)** for mobile."

**Why this works:**
-   **Compensating Transactions:** Forces error handling to include "Undo" logic (e.g., refund payment if shipping fails), which is almost always missing in generic AI code.
-   **BFF:** Prevents the "Over-fetching" problem by creating a tailored API layer.

### Level 7: Real-World Designs
**Weak Prompt:**
> "Build a news feed."

**Deconstructed Senior Prompt:**
> "Implement a **fan-out on write** architecture. storage in **Redis Sorted Sets (ZSET)**."

**Why this works:**
-   **Fan-out on Write:** Commits to a specific scalability model (fast reads, slow writes) suitable for most users.
-   **Redis ZSET:** Specifies the exact data structure needed for O(log N) retrieval by time, preventing the AI from trying to sort SQL results in memory.

### Level 8: Senior Thinking
**Weak Prompt:**
> "Add logging."

**Deconstructed Senior Prompt:**
> "Add **structured logging (JSON)** with **correlation IDs**. Export metrics to **Prometheus**."

**Why this works:**
-   **Structured Logging:** Ensures logs are machine-readable, enabling better debugging tools.
-   **Correlation IDs:** The single most important feature for tracing requests across microservices. Without asking, AI rarely includes this.

---

## Prompting Framework: CARD

**C - Context:** What's the system, what exists, what's the scale?
**A - Architecture:** What components, patterns, technologies?
**R - Requirements:** Functional and non-functional needs?
**D - Details:** Error handling, edge cases, testing?

### Example:

```
Context:
- E-commerce platform, 10K orders/day
- Node.js backend, PostgreSQL, Redis

Architecture:
- Order service calls Payment service (sync)
- Order events published to Kafka for fulfillment

Requirements:
- Payment must be exactly-once
- Order creation < 500ms latency
- 99.9% availability

Details:
- Use idempotency keys for payment
- Handle payment timeout (retry with same key)
- Return pending status, reconcile async

Implement the order creation endpoint.
```

---

## Validation Checklist

After AI generates code, check:

### For APIs
- [ ] Correct HTTP methods and status codes?
- [ ] Input validation present?
- [ ] Error handling for all failure cases?
- [ ] Authentication/authorization checked?
- [ ] Rate limiting considered?

### For Database Code
- [ ] Queries efficient (no N+1)?
- [ ] Indexes exist for queried columns?
- [ ] Transactions where needed?
- [ ] Connection handling correct?
- [ ] No SQL injection possible?

### For Distributed Systems
- [ ] Timeouts on external calls?
- [ ] Retry logic with backoff?
- [ ] Idempotent operations?
- [ ] Failure handling defined?
- [ ] Events/messages handled at-least-once safely?

---

## From Concept to Prompt

| Concept | Prompt Enhancement |
|---------|-------------------|
| Load balancing | "Running behind an ALB with 3 instances" |
| Caching | "Cache in Redis, TTL 5 minutes, cache-aside" |
| Async processing | "Queue job to SQS, process with worker" |
| Database scaling | "PostgreSQL with read replica for reports" |
| API design | "RESTful, use 201 for creates, 404 for not found" |
| Security | "JWT authentication, bcrypt password hashing" |
| Observability | "Log with structured JSON, include request ID" |
| Consistency | "Use transaction for order + payment records" |

---

## Common Prompt Mistakes

**Too vague:**
❌ "Build me a backend"

**Missing scale:**
❌ "Build a video upload feature" (no mention of file sizes, number of uploads)

**No error handling spec:**
❌ "Connect to the database" (what if it's down?)

**Assuming AI knows your code:**
❌ "Use the same pattern as the user service" (AI doesn't know it)

**Not iterating:**
❌ Accepting first output without follow-up

---

## Iteration Pattern

**Round 1: Core functionality**
> "Implement the basic order creation endpoint"

**Round 2: Add validation**
> "Now add input validation for the order endpoint"

**Round 3: Add error handling**
> "Add error handling for database failures and invalid payment"

**Round 4: Add observability**
> "Add logging and metrics to the order endpoint"

**Round 5: Add tests**
> "Write unit tests covering success and each error case"

---

## AI as Study Partner

Use AI to learn system design:

**Explain concepts:**
> "Explain the trade-offs between SQL and NoSQL for a high-write workload"

**Quiz yourself:**
> "Ask me 5 questions about database sharding and tell me if I'm right"

**Explore designs:**
> "Show me how rate limiting works with the token bucket algorithm. Walk through an example."

**Practice interviews:**
> "Be a system design interviewer. Ask me to design a URL shortener. Challenge my answers."

---

## The Path

```
Beginner → Learn concepts → Can describe systems
    ↓
Intermediate → Apply to prompts → Get better AI output
    ↓
Senior → Validate output → Catch issues before production
    ↓
Senior → Design systems → Guide teams + AI effectively
```

System design knowledge compounds. Each concept you learn makes every prompt more effective.

---

## Quick Reference

### What to specify in prompts:

| Aspect | Example |
|--------|---------|
| Database | "PostgreSQL with index on user_id" |
| Cache | "Redis, cache-aside, 10 min TTL" |
| API style | "REST, proper status codes, JSON errors" |
| Auth | "JWT, validate in middleware" |
| Errors | "Return 400 for validation, 500 for internal" |
| Scale | "Handle 1000 RPS, read-heavy" |
| Async | "Queue to SQS, idempotent consumer" |
| Monitoring | "Structured logs, request IDs, metrics" |

### Red flags in AI output:

| Issue | Fix |
|-------|-----|
| No error handling | Ask "Add error handling for X failure" |
| No timeout | Ask "Add 5 second timeout on external calls" |
| No validation | Ask "Add input validation" |
| 200 for everything | Ask "Use proper HTTP status codes" |
| No auth check | Ask "Add authentication to this endpoint" |
| Hardcoded secrets | Ask "Use environment variables for secrets" |

---

## Start Here

1. Go through the levels in order
2. At each level, practice prompting with the concepts
3. Build something real, using AI with your new knowledge
4. Notice how your prompts get more specific
5. Notice how AI output gets better

The goal: become the architect, let AI be the implementer.

---

[← Back to Home](README.md)
