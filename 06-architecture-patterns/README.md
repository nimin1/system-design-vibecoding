# Level 6: Architecture Patterns

High-level system organization and communication patterns.

---

## What's Here

| Chapter | Topic | Key Concepts |
|---------|-------|--------------|
| [Monolith vs Microservices](01-monolith-vs-microservices.md) | Architectural approaches | Trade-offs, when to split |
| [API Design](02-api-design.md) | Building good APIs | REST, versioning, errors |
| [Common Patterns](03-common-patterns.md) | Reusable solutions | Saga, Circuit Breaker, CQRS |
| [Service Communication](04-service-communication.md) | How services talk | REST, gRPC, service mesh |
| [Event Streaming](05-event-streaming.md) | Real-time pipelines | Kafka, partitions, consumers |
| [Data Processing](06-data-processing.md) | Transforming data | Batch vs stream, ETL vs ELT |
| [Containers and Kubernetes](07-containers-kubernetes.md) | Modern infrastructure | Pods, deployments, scaling |

---

## Why This Matters

Architecture patterns are recurring solutions to common problems. Understanding them helps you:

- **Communicate efficiently**  -  "Let's use a saga here" conveys a lot
- **Avoid reinventing solutions**  -  Proven patterns exist
- **Recognize trade-offs**  -  Every pattern has costs
- **Evaluate existing systems**  -  Understand why they were built that way

---

## How This Helps with AI Tools

After this section, you can specify architectural context:

**Before:**
> "Build a checkout service"

**After:**
> "Build a checkout service using the saga pattern. Orchestrator coordinates inventory reservation, payment, and order creation. Each step has compensating action for rollback."

---

## Progress

- [ ] Monolith vs Microservices
- [ ] API Design
- [ ] Common Patterns
- [ ] Service Communication
- [ ] Event Streaming
- [ ] Data Processing
- [ ] Containers and Kubernetes
- [ ] [Quiz](quiz.md)
- [ ] [Flashcards](flashcards.md)

---

[← Back to Level 5](../05-distributed-systems/README.md) | [Level 7 →](../07-real-world-designs/README.md)
