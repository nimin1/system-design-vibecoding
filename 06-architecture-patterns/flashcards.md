# Level 6 Flashcards

Quick reference for architecture patterns.

---

## Monolith vs Microservices

| Front | Back |
|-------|------|
| When start with monolith? | Early stage, small team, domain not understood, need rapid iteration. |
| Modular monolith? | Clear internal boundaries without operational complexity of microservices. |
| When microservices? | Large team, domain well understood, need independent scaling/deployment. |

---

## API Design

| Front | Back |
|-------|------|
| 201 vs 200? | 201 for creates. 200 for successful existing resource. |
| API versioning approaches? | URL path (/v1/), header (Accept-Version), query param (?v=1). |
| Backward compatibility? | Add fields, don't remove. Don't change meaning. |

---

## Common Patterns

| Front | Back |
|-------|------|
| Saga? | Distributed transaction using local transactions + compensating actions. |
| Circuit breaker? | Stop calling failing service. Prevent cascade failures. |
| Strangler fig? | Gradually replace legacy by routing to new system. |
| CQRS? | Separate models for read (query) and write (command). |
| BFF? | Backend for Frontend. Different APIs for different clients. |

---

## Service Communication

| Front | Back |
|-------|------|
| REST vs gRPC? | REST: universal, public APIs. gRPC: performance, internal, streaming. |
| Service mesh? | Infrastructure for service communication: discovery, mTLS, observability. |
| Service discovery? | How services find each other. DNS, registry (Consul), or Kubernetes. |

---

## Event Streaming

| Front | Back |
|-------|------|
| Kafka core concepts? | Topics, partitions, producers, consumers, consumer groups. |
| Kafka vs message queue? | Kafka retains events, allows replay. Queues delete after consumption. |
| Partition key importance? | Determines which partition. Ordering only within partition. |

---

## Data Processing

| Front | Back |
|-------|------|
| Batch processing? | Process data in chunks periodically. |
| Stream processing? | Process data continuously as it arrives. |
| Lambda architecture? | Batch + stream layers. Accurate + real-time. |
| CDC? | Change Data Capture. Capture DB changes as events. |

---

## Containers/Kubernetes

| Front | Back |
|-------|------|
| Container benefit? | Consistent environment: dev = staging = prod. |
| Kubernetes purpose? | Container orchestration: deploy, scale, self-heal. |
| Pod? | Smallest deployable unit. Usually one container. |
| Service (K8s)? | Stable network endpoint for pods. |

---

[Continue to Level 7](../07-real-world-designs/README.md)
