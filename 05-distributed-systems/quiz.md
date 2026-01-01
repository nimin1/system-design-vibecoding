# Level 5 Quiz

Test your understanding of distributed systems concepts.

---

## Message Queues

<details>
<summary><b>1. Why use a message queue instead of direct calls?</b></summary>

Decoupling (services don't know about each other), handling traffic spikes (queue buffers), reliability (messages wait for downed consumers), and async processing (caller doesn't wait).

</details>

<details>
<summary><b>2. What's the difference between point-to-point and pub-sub?</b></summary>

Point-to-point: each message goes to one consumer. Pub-sub: each message goes to all subscribers.

</details>

<details>
<summary><b>3. Why must consumers be idempotent?</b></summary>

Most queues provide at-least-once delivery. Messages might be delivered multiple times. Idempotent consumers handle duplicates without side effects.

</details>

---

## Event-Driven Architecture

<details>
<summary><b>4. What's the difference between events and commands?</b></summary>

Events describe what happened (past tense, immutable facts). Commands describe what should happen (imperative, instructions).

</details>

<details>
<summary><b>5. What's the main benefit of event-driven architecture?</b></summary>

Decoupling. Producers don't know about consumers. Adding new reactions doesn't change existing services.

</details>

<details>
<summary><b>6. What's event sourcing?</b></summary>

Storing the sequence of events instead of current state. State is derived by replaying events. Good for audit trails and replays.

</details>

---

## Handling Failures

<details>
<summary><b>7. Why set timeouts on all external calls?</b></summary>

Without timeouts, a slow service can block threads indefinitely, exhausting resources and causing cascade failures.

</details>

<details>
<summary><b>8. What's exponential backoff?</b></summary>

Increasing wait time between retries: 1s, 2s, 4s, 8s... Prevents hammering a struggling service.

</details>

<details>
<summary><b>9. What does a circuit breaker do?</b></summary>

Stops calling a failing service after repeated failures. Prevents cascade failures. Periodically tests if the service has recovered (half-open state).

</details>

---

## Consensus

<details>
<summary><b>10. What is consensus in distributed systems?</b></summary>

Getting multiple nodes to agree on a single value (or sequence of values) despite failures.

</details>

<details>
<summary><b>11. Why is majority quorum important?</b></summary>

Any two majorities overlap, preventing conflicting decisions. Also allows progress with minority failures.

</details>

<details>
<summary><b>12. How does Raft handle leader failure?</b></summary>

Followers timeout without heartbeat, become candidates, request votes. Node with majority votes becomes leader. Term numbers prevent old leaders from causing problems.

</details>

---

## Distributed Transactions

<details>
<summary><b>13. What's the main problem with 2PC?</b></summary>

Blocking: if coordinator fails after Phase 1, participants are stuck holding locks. Also slow and doesn't scale.

</details>

<details>
<summary><b>14. What's a saga?</b></summary>

Sequence of local transactions with compensating actions. If a step fails, previous steps are undone via compensation.

</details>

<details>
<summary><b>15. Choreography vs orchestration?</b></summary>

Choreography: each service reacts to events independently. Orchestration: central coordinator directs the flow.

</details>

---

## Distributed Locking

<details>
<summary><b>16. Why do distributed locks need expiration?</b></summary>

If a holder crashes without releasing, the lock would be held forever. Expiration ensures automatic release.

</details>

<details>
<summary><b>17. What's a fencing token?</b></summary>

Monotonically increasing number given with each lock acquisition. Protected resource rejects operations with old tokens.

</details>

---

## Scoring

| Score | Assessment |
|-------|------------|
| 14-17 | Solid understanding. Move on. |
| 10-13 | Good grasp. Review weak spots. |
| < 10 | Re-read the chapters. |

---

[Review flashcards](flashcards.md) | [Continue to Level 6](../06-architecture-patterns/README.md)
