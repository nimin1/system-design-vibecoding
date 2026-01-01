# Level 5: Distributed Systems

What happens when your system spans multiple machines.

---

## What's Here

| Chapter | Topic | Key Concepts |
|---------|-------|--------------|
| [Message Queues](01-message-queues.md) | Asynchronous communication | Kafka, RabbitMQ, delivery guarantees |
| [Event-Driven Architecture](02-event-driven.md) | Building systems around events | Event sourcing, CQRS, pub/sub |
| [Handling Failures](03-handling-failures.md) | Designing for things that will fail | Retries, circuit breakers, fallbacks |
| [Consensus](04-consensus.md) | How distributed systems agree | Paxos, Raft, leader election |
| [Distributed Transactions](05-distributed-transactions.md) | Consistency across services | 2PC, Sagas, outbox pattern |
| [Distributed Locking](06-distributed-locking.md) | Coordinating access to resources | Redis locks, Redlock, fencing |

---

## Why This Matters

Once your system involves multiple services or databases, new problems appear:
- **Network partitions**  -  Services can't always reach each other
- **Partial failures**  -  Some parts fail while others succeed
- **Eventual consistency**  -  Data takes time to propagate
- **Coordination**  -  Nodes must agree on shared state

This section covers how to think about and solve these challenges.

---

## Progress

- [ ] Message Queues
- [ ] Event-Driven Architecture
- [ ] Handling Failures
- [ ] Consensus
- [ ] Distributed Transactions
- [ ] Distributed Locking
- [ ] [Quiz](quiz.md)
- [ ] [Flashcards](flashcards.md)

---

[← Back to Level 4](../04-data-layer/README.md) | [Level 6 →](../06-architecture-patterns/README.md)
