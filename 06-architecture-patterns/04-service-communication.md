# Service Communication

How services talk to each other synchronous, asynchronous, and everything in between.

---

## Why This Matters

In a distributed system, services must communicate. The choice of how they communicate affects:
- Latency and throughput
- Coupling between services
- Failure handling
- Debugging and tracing
- Operational complexity

There's no single right answer. Different patterns for different needs.

---

## Synchronous Communication

Caller waits for response.

### REST (HTTP/JSON)

Most common for external APIs and many internal services.

```
POST /api/orders
Content-Type: application/json

{"items": [...]}

Response: 201 Created
{"order_id": "ord-123"}
```

**Pros:**
- Simple, widely understood
- Human-readable
- Easy to debug (curl, browser)
- Mature tooling

**Cons:**
- Text overhead (JSON parsing)
- No schema enforcement (unless using OpenAPI)
- HTTP/1.1 connection per request (HTTP/2 helps)

**Use for:**
- External APIs
- Simple service-to-service calls
- When human readability matters

### gRPC

Binary protocol using Protocol Buffers over HTTP/2.

```protobuf
service OrderService {
  rpc CreateOrder (CreateOrderRequest) returns (Order);
}

message CreateOrderRequest {
  repeated Item items = 1;
}
```

**Pros:**
- Much faster than JSON (binary serialization)
- Strong typing with code generation
- Streaming support (client, server, bidirectional)
- Built-in deadline propagation

**Cons:**
- Not browser-native (needs proxy)
- Harder to debug (binary, need tools)
- Schema management required

**Use for:**
- Internal service-to-service
- High-performance requirements
- Streaming data

### GraphQL

Query language where client specifies what data it wants.

```graphql
query {
  order(id: "ord-123") {
    id
    items { name, price }
    customer { name }
  }
}
```

**Pros:**
- No over-fetching or under-fetching
- Single endpoint for everything
- Client-driven queries

**Cons:**
- Complex caching
- Can enable expensive queries
- Learning curve

**Use for:**
- Client-facing APIs with varied data needs
- Mobile apps (reduce round trips)

---

## Asynchronous Communication

Caller doesn't wait for response.

### Message Queues

Send message, continue. Message processed later.

```
Producer → Message Queue → Consumer

OrderService publishes "OrderCreated"
PaymentService consumes and processes
```

**Pros:**
- Decoupling (producer doesn't know consumers)
- Resilience (queue absorbs failures)
- Scalability (add consumers)
- Temporal decoupling (process when ready)

**Cons:**
- Eventual consistency
- Complex debugging (async flows)
- Message ordering challenges
- At-least-once delivery (need idempotency)

**Tools:** Kafka, RabbitMQ, AWS SQS, AWS SNS

**Use for:**
- Events (order created, user registered)
- Async workflows
- High-throughput ingestion

### Event Streaming

Continuous flow of events. Consumer can replay from any point.

```
Kafka topic "orders"
  offset 0: OrderCreated
  offset 1: OrderUpdated
  offset 2: OrderShipped
  ...

Consumers read from their offset, can rewind
```

**Pros:**
- Replay capability
- Multiple consumers (each with own offset)
- Data integration

**Cons:**
- More complex than simple queues
- Storage for retained messages

**Tools:** Kafka, AWS Kinesis, Apache Pulsar

**Use for:**
- Event sourcing
- Stream processing
- Data pipelines

---

## Communication Patterns

### Request-Response

Caller expects a response.

**Synchronous:** Caller blocks waiting.
**Asynchronous:** Caller sends request, gets response on callback or separate channel.

### Fire-and-Forget

Caller sends message, doesn't expect response.

```
LogService.log(event)  // don't care about response
```

**Use for:** Logging, analytics, non-critical notifications

### Publish-Subscribe

Publisher sends to topic. Multiple subscribers receive.

```
OrderService publishes "OrderCreated"

InventoryService subscribes → reserves stock
PaymentService subscribes → charges card
NotificationService subscribes → sends email
AnalyticsService subscribes → records event
```

**Use for:** Events that multiple services need to react to

### Point-to-Point

Message sent to specific destination. One consumer processes.

```
Job queue: process_image
Worker 1 takes job → processes
```

**Use for:** Work queues, task distribution

---

## Handling Failures

Distributed communication fails. Often.

### Timeouts

Every call needs a timeout.

**Without:**
```
ServiceA calls ServiceB
ServiceB hangs
ServiceA waits forever
Thread exhausted, ServiceA dies
```

**With:**
```
ServiceA calls ServiceB, timeout 2s
ServiceB slow
After 2s, ServiceA returns error
ServiceA continues operating
```

**Set timeouts based on:** P99 latency of downstream + margin.

### Retries

Transient failures happen. Retry can help.

**Retry when:**
- Network blip (connection reset)
- Server overload (503)
- Timeout (might have been temporary)

**Don't retry when:**
- Client error (400, 404) - request is wrong
- Auth error (401, 403) - won't magically succeed
- Non-idempotent operation without safeguards

**Retry strategy:**
- Exponential backoff: 1s, 2s, 4s, 8s...
- Jitter: random offset to prevent thundering herd
- Max attempts: eventually give up

### Circuit Breaker

Stop calling failing service.

```
Normal: calls go through
Failures accumulate → circuit opens
Open: calls fail fast, don't reach downstream
After timeout → half-open: try some calls
Success → close circuit
Failure → reopen circuit
```

**Why:** Prevents cascading failures. Gives downstream time to recover.

### Fallback

When call fails, do something else.

```
Try: get user from UserService
Failed → fallback: get user from cache
Cache miss → fallback: return minimal data
```

---

## Service Discovery

How does ServiceA find ServiceB?

### Static Configuration

Hardcoded hostnames/IPs.

```
# config.yaml
user_service: user-service.internal:8080
```

**Works for:** Simple setups, known infrastructure.

### DNS-Based

Services registered in DNS.

```
user-service.internal → 10.0.1.5, 10.0.1.6
```

Client picks an IP (or uses round-robin).

**Works for:** Kubernetes (built-in DNS), simple discovery.

### Service Registry

Dedicated discovery service.

Services register on startup. Clients query registry.

**Tools:** Consul, Eureka, etcd

**Works for:** Dynamic environments, health-aware routing.

### Service Mesh

Sidecars handle discovery transparently.

```
App calls localhost → Sidecar routes to destination
```

App doesn't know about discovery. Mesh handles it.

**Tools:** Istio, Linkerd

---

## Load Balancing

Distribute requests across instances.

### Client-Side

Client knows all instances, chooses one.

```
Client has list: [A, B, C]
Client picks: Round-robin, random, least connections
```

**Pros:** No central point. Direct connection.
**Cons:** Client complexity. Stale instance list.

### Server-Side

Load balancer decides.

```
Client → Load Balancer → picks backend
```

**Pros:** Simple client. Central control.
**Cons:** Extra hop. Load balancer is a component to manage.

### Algorithms

| Algorithm | How It Works |
|-----------|--------------|
| Round-robin | Next in sequence |
| Random | Random pick |
| Least connections | Pick least loaded |
| Weighted | Distribute by weight |
| Consistent hashing | Same client to same server |

---

## API Versioning

APIs evolve. How to handle?

### URL Versioning

```
/v1/users
/v2/users
```

**Pros:** Obvious, easy routing.
**Cons:** URL changes.

### Header Versioning

```
Accept: application/vnd.api+json;version=2
```

**Pros:** Clean URLs.
**Cons:** Less visible, harder to test.

### Query Parameter

```
/users?version=2
```

**Middle ground.**

### Recommendation

URL versioning is most common and clearest. Maintain backward compatibility as long as practical.

---

## Common Mistakes

**No timeouts.** Waiting forever for a response.

**Retrying everything.** Including non-idempotent operations.

**No circuit breaker.** Hammering failing service.

**Synchronous for everything.** When async would decouple and improve resilience.

**Tight coupling.** Service A knows too much about Service B internals.

**No tracing.** Request spans services; no way to see the full picture.

---

## What An Experienced Senior Engineer Thinks About

**Service contracts.** APIs are contracts. Breaking changes break consumers. Version and deprecate carefully.

**Backwards compatibility.** New version should work with old clients. Add fields, don't remove/rename.

**Idempotency.** Design operations to be safely retryable.

**Observability.** Every hop should be traceable. Distributed tracing is essential.

**Failure isolation.** One service failing shouldn't cascade. Timeouts, circuit breakers, bulkheads.

---

## Vibe Engineering Guide

When prompting about service communication:

**Less useful:**
> "How should services communicate?"

**More useful:**
> "I have 5 microservices. Currently all REST/HTTP synchronous. When one is slow, everything slows. Order workflow: Order Service → Inventory Service → Payment Service → Notification Service. I want to:
> - Decouple Notification (non-critical)
> - Add retry logic for transient failures
> - Prevent cascade when Payment is slow
>
> How should I mix sync/async? Where should I add circuit breakers?"

**For specific patterns:**
> "We're considering gRPC for internal services. Currently REST with JSON. We have 20 services. What are the migration considerations? Is the performance difference worth the operational complexity?"

---

## Quick Check

<details>
<summary><b>When should you use async messaging vs. sync HTTP?</b></summary>

Async (queues) when: caller doesn't need immediate response, want decoupling, want resilience to downstream failures. Sync (HTTP/gRPC) when: need immediate response, simple request-response pattern.

</details>

<details>
<summary><b>Why is gRPC faster than REST?</b></summary>

Binary serialization (protobuf) vs. JSON text. HTTP/2 connection multiplexing. Strongly typed with code generation. Smaller payload sizes.

</details>

<details>
<summary><b>What's the purpose of a circuit breaker?</b></summary>

Stop calling a failing service. After threshold of failures, "open" the circuit - fail fast without calling. Gives downstream time to recover. Prevents cascade failures.

</details>

<details>
<summary><b>Why add jitter to retry backoff?</b></summary>

Without jitter, all clients retry at exact same time (thundering herd). Random jitter spreads retries, reducing spike on recovering service.

</details>

<details>
<summary><b>What's publish-subscribe pattern?</b></summary>

Publisher sends to topic. Multiple subscribers receive the same message. Decouples publisher from consumers. Publisher doesn't need to know who subscribes.

</details>

---

Next: [Event Streaming](05-event-streaming.md)
