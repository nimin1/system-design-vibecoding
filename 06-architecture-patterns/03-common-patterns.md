# Common Architecture Patterns

Reusable solutions to common system design problems.

---

## Why Patterns Matter

Patterns are distilled experience. They're solutions that have worked for others. Understanding them:
- Saves time (don't reinvent the wheel)
- Improves communication (shared vocabulary)
- Helps recognize appropriate solutions for problems

Patterns aren't prescriptions. Apply them when the problem fits.

---

## Ambassador Pattern

Deploy a helper service alongside your main service to handle cross-cutting concerns.

**Problem:** Services need retry logic, circuit breakers, logging, auth. Implementing in each service is duplicative.

**Solution:** Ambassador (sidecar) handles these concerns. Main service just calls localhost.

```
┌──────────────────────────────────────┐
│               Pod                    │
│  ┌────────────────┐  ┌────────────┐  │
│  │  Main Service  │──│  Ambassador │──│──► External Services
│  └────────────────┘  └────────────┘  │
└──────────────────────────────────────┘
```

**Use when:**
- Multiple services need same cross-cutting logic
- You want language-agnostic common functionality
- Service mesh (Envoy, Istio)

---

## Sidecar Pattern

Extend or enhance a service without modifying it.

**Problem:** Legacy service needs new capabilities (logging, monitoring) but can't be modified.

**Solution:** Deploy a sidecar container alongside. It handles the new capability.

**Examples:**
- Logging sidecar that collects logs
- Monitoring sidecar that exposes metrics
- Auth sidecar that handles mTLS

**Difference from Ambassador:** Ambassador is specifically for outbound network. Sidecar is general-purpose enhancement.

---

## Strangler Fig Pattern

Gradually replace a legacy system by incrementally routing traffic to a new system.

**Problem:** Big-bang rewrites are risky and take forever.

**Solution:** Build new functionality. Route traffic to new system piece by piece. Old system shrinks until it's gone.

```
Phase 1: 100% → Legacy
Phase 2: 80% → Legacy, 20% → New (specific feature)
Phase 3: 50% → Legacy, 50% → New
Phase 4: 100% → New, Legacy retired
```

**Use when:**
- Replacing a monolith with microservices
- Any gradual migration
- Want to validate new system incrementally

---

## Circuit Breaker Pattern

Stop calling a failing service to prevent cascade failures.

**Problem:** Downstream service is failing. Your retries add load. Eventually your service fails too.

**Solution:** After N failures, "open" the circuit. Fail fast without calling. After timeout, "half-open" to test. If recovered, "close" the circuit.

**States:**
- **Closed:** Normal operation, requests go through
- **Open:** Failing fast, requests don't go to downstream
- **Half-Open:** Testing if downstream recovered

**Use when:**
- Calling remote services
- Any dependency that might fail

---

## Bulkhead Pattern

Isolate components so failure in one doesn't affect others.

**Problem:** Slow dependency uses all your threads. Now unrelated requests also fail.

**Solution:** Partition resources. Each component gets its own pool.

```
┌─────────────────────────────────────────────────┐
│                  Service                         │
│  ┌──────────────┐  ┌──────────────┐             │
│  │ Thread Pool   │  │ Thread Pool  │             │
│  │ for Service A │  │ for Service B │            │
│  └──────────────┘  └──────────────┘             │
└─────────────────────────────────────────────────┘
```

Service A being slow doesn't consume Service B's resources.

**Use when:**
- Multiple independent dependencies
- Want failure isolation

---

## Saga Pattern

Manage distributed transactions across multiple services.

**Problem:** ACID transactions don't work across service boundaries. How to ensure all-or-nothing?

**Solution:** Saga: sequence of local transactions with compensating actions for rollback.

**Example: Create Order**
1. Create order (Order Service)
2. Reserve inventory (Inventory Service)
3. Charge payment (Payment Service)

If step 3 fails:
- Compensate step 2: Release inventory
- Compensate step 1: Cancel order

**Orchestration vs. Choreography:**
- **Orchestration:** Central coordinator controls the flow
- **Choreography:** Each service reacts to events authentically

**Use when:**
- Cross-service operations that need consistency
- Can't use distributed transactions

---

## CQRS Pattern

Separate read and write models.

**Problem:** Read and write operations have different requirements. Optimizing for both in one model is difficult.

**Solution:** Separate models. Write model handles business logic. Read model(s) optimized for queries.

```
          Commands                    Queries
              │                           │
              ▼                           ▼
   ┌──────────────────┐        ┌──────────────────┐
   │   Write Model    │───────►│   Read Model     │
   │  (Event Store)   │ events │  (Denormalized)  │
   └──────────────────┘        └──────────────────┘
```

**Use when:**
- Read and write patterns are very different
- High-performance reads with complex writes
- Often paired with Event Sourcing

---

## Event Sourcing Pattern

Store changes as a sequence of events, not just current state.

**Problem:** You need full history. Current-state storage loses information about how you got there.

**Solution:** Store every change as an immutable event. Current state derived by replaying events.

```
Events:
1. OrderCreated(id=1, items=[A, B])
2. ItemRemoved(id=1, item=A)
3. OrderPaid(id=1, amount=$50)

Current state (derived): Order 1, items=[B], paid=$50
```

**Use when:**
- Audit trails required
- Need to replay or rewind state
- Domain is naturally event-based

---

## Retry Pattern

Retry failed operations that might succeed on retry.

**Problem:** Transient failures happen. Network blips, temporary overload.

**Solution:** Retry with backoff.

**Retry strategy:**
- Immediate retry (brief glitch)
- Exponential backoff (1s, 2s, 4s, 8s...)
- Jitter (random addition to prevent thundering herd)
- Max retries (eventually give up)

**Only retry when:**
- Operation is idempotent (safe to retry)
- Failure is transient (might succeed)

**Don't retry:**
- Client errors (400s) - request is wrong
- Non-idempotent operations without safeguards

---

## Throttling Pattern

Control resource consumption by limiting request rate.

**Problem:** Misbehaving or aggressive clients consume all resources.

**Solution:** Limit requests per client, per time window.

**Strategies:**
- Reject requests over limit (429 Too Many Requests)
- Queue requests (add delay)
- Degrade service (serve simpler response)

---

## Queue-Based Load Leveling

Use a queue to smooth out traffic spikes.

**Problem:** Traffic spikes overwhelm your service.

**Solution:** Queue absorbs spikes. Service processes at steady rate.

```
Requests → Queue → Service (processes at X/sec)
```

**Benefits:**
- Service doesn't have to handle peak rate
- Natural backpressure
- Work isn't lost during spikes

**Trade-off:** Added latency (requests wait in queue).

---

## Competing Consumers Pattern

Multiple consumers process messages from a queue in parallel.

**Problem:** Single consumer can't keep up.

**Solution:** Add more consumers. Each message goes to one consumer. Work is divided.

```
Queue ─┬─► Consumer 1
       ├─► Consumer 2
       └─► Consumer 3
```

**Use when:**
- Processing can be parallelized
- Need to scale throughput

---

## Gateway Aggregation Pattern

Use a gateway to aggregate multiple calls into one.

**Problem:** Client needs data from multiple services. Multiple round trips from client.

**Solution:** Gateway calls multiple services internally, aggregates, returns one response.

```
Client ──► Gateway ─┬─► Service A
                    ├─► Service B
                    └─► Service C
                    
Gateway aggregates and returns one response
```

**Use when:**
- Mobile clients (high latency per call)
- Need to combine multiple backends

---

## Backends for Frontends (BFF) Pattern

Create separate backends for different client types.

**Problem:** Mobile, web, and API have different needs from the same services.

**Solution:** Dedicated backend per frontend type. Each optimizes for its client.

```
Mobile App ──► Mobile BFF ──► Services
Web App    ──► Web BFF    ──► Services
Partner API──► API BFF    ──► Services
```

**Use when:**
- Different clients have significantly different needs
- API changes would break other clients

---

## Gateway Offloading Pattern

Offload cross-cutting concerns to the gateway.

**Problem:** Every service implements auth, rate limiting, logging...

**Solution:** Gateway handles these. Services focus on business logic.

**Gateway handles:**
- Authentication
- Authorization
- Rate limiting
- Request logging
- SSL termination
- IP allowlisting

---

## Common Mistakes

**Pattern for pattern's sake.** Using complex patterns when simple solution works.

**Wrong pattern.** Applying a pattern to a problem it doesn't fit.

**Not understanding trade-offs.** Every pattern has costs. Event sourcing adds complexity. CQRS adds eventual consistency.

**Skipping the basics.** Before adding patterns, ensure your fundamentals (monitoring, logging, retries) are solid.

---

## What An Experienced Senior Engineer Thinks About

**Pattern fatigue.** Junior engineers love adding new patterns. Senior engineers default to the simplest possible solution. "Do we really need CQRS here, or is a simple CRUD app fine?"

**Migration cost.** Implementing a pattern is 20% of the work. Migrating existing data and traffic to it is the other 80%.

**Operational visibility.** Patterns like Sagas and Event Sourcing make debugging hard. "Where exactly did this transaction fail?" If you can't trace it, don't build it.

**Failure modes.** "What happens when the Circuit Breaker opens?" "What if the compensating transaction in the Saga fails?" Designing the happy path is easy; designing the failure path is the job.

**Coupling in disguise.** Sharing a database is coupling. Sharing a complex Saga orchestrator can also be coupling on logic. Be careful where you tie services together.

---

## Vibe Engineering Guide

When prompting about patterns:

**Less useful:**
> "Use the saga pattern"

**More useful:**
> "I have an order flow that spans three services:
> 1. Order Service creates order
> 2. Inventory Service reserves stock
> 3. Payment Service charges card
>
> Currently, if payment fails, I manually clean up. I want to implement the Saga pattern with compensating transactions. Should I use orchestration or choreography? What's the rollback sequence if step 3 fails?"

**For pattern selection:**
> "My monolith calls 5 external services. When one is slow, the whole request slows down. Sometimes a service failure causes cascading failures. What patterns should I use? Circuit breaker? Bulkhead? How do they work together?"

---

## Quick Check

<details>
<summary><b>What's the strangler fig pattern?</b></summary>

Gradually replace a legacy system by routing traffic piece by piece to a new system. Avoids risky big-bang rewrites. Named after vines that gradually envelope and replace trees.

</details>

<details>
<summary><b>When should you use the saga pattern?</b></summary>

When you need transaction-like consistency across multiple services but can't use distributed transactions. Saga uses compensating transactions to roll back on failure.

</details>

<details>
<summary><b>What's the difference between orchestration and choreography?</b></summary>

Orchestration: central coordinator manages the flow. Choreography: services react to events independently. Orchestration is easier to understand; choreography is more loosely coupled.

</details>

<details>
<summary><b>Why use the bulkhead pattern?</b></summary>

Isolation. If Dependency A uses all resources, Dependency B's traffic is unaffected because it has its own resource pool. Prevents one slow service from taking down everything.

</details>

<details>
<summary><b>What does CQRS help with?</b></summary>

Separating read and write concerns. Write model optimized for business logic and consistency. Read model(s) optimized for queries. Useful when read and write patterns are very different.

</details>

---

Next: [Service Communication](04-service-communication.md)
