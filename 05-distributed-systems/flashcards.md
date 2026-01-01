# Level 5 Flashcards

Quick reference for distributed systems concepts.

---

## Message Queues

| Front | Back |
|-------|------|
| Why use message queues? | Decoupling, handling spikes, reliability, async processing. |
| Point-to-point vs pub-sub? | Point-to-point: one consumer. Pub-sub: all subscribers get message. |
| At-least-once delivery? | Message may be delivered multiple times. Consumer must be idempotent. |
| Dead letter queue? | Where failed messages go after max retries. |

---

## Event-Driven Architecture

| Front | Back |
|-------|------|
| Events vs commands? | Events: what happened (facts). Commands: what should happen (instructions). |
| Why event-driven? | Decoupling, extensibility, scalability, resilience. |
| Event sourcing? | Store sequence of events instead of current state. Replay to get state. |
| CQRS? | Separate models for reading (queries) and writing (commands). |

---

## Handling Failures

| Front | Back |
|-------|------|
| Why timeouts? | Prevent blocking on slow services. Avoid resource exhaustion. |
| Exponential backoff? | Increasing wait between retries: 1s, 2s, 4s, 8s... |
| Circuit breaker? | Stop calling failing service. Prevent cascade failures. Test recovery. |
| Why idempotency? | Retries may duplicate operations. Idempotent ops handle this safely. |
| Graceful degradation? | Reduced functionality instead of complete failure. |

---

## Consensus

| Front | Back |
|-------|------|
| What is consensus? | Multiple nodes agreeing on a value despite failures. |
| Why majority quorum? | Any two majorities overlap. Prevents conflicting decisions. |
| Raft leader election? | Timeout → become candidate → request votes → majority wins. |
| Paxos vs Raft? | Same problem, Raft is designed to be understandable. |

---

## Distributed Transactions

| Front | Back |
|-------|------|
| 2PC problem? | Blocking if coordinator fails. Slow. Doesn't scale. |
| Saga pattern? | Local transactions + compensations. Eventual consistency. |
| Choreography vs orchestration? | Choreography: event-driven. Orchestration: central coordinator. |

---

## Distributed Locking

| Front | Back |
|-------|------|
| Why expiration? | Dead holder would hold lock forever. |
| Fencing token? | Increasing number to reject operations from expired locks. |
| Redlock? | Lock on majority of Redis instances for reliability. |

---

[Continue to Level 6](../06-architecture-patterns/README.md)
