# Level 6 Quiz

Test your understanding of architecture patterns.

---

## Monolith vs Microservices

<details>
<summary><b>1. When is a monolith the right choice?</b></summary>

Early stage, small team, domain not well understood, rapid iteration needed. Most applications should start as monoliths.

</details>

<details>
<summary><b>2. What's a modular monolith?</b></summary>

A monolith with clear internal module boundaries. Gets some benefits of microservices (clear boundaries) without the operational complexity.

</details>

---

## API Design

<details>
<summary><b>3. What status code for successful creation?</b></summary>

201 Created (not 200). Include the created resource or its ID in the response.

</details>

<details>
<summary><b>4. Why version APIs?</b></summary>

To make breaking changes without disrupting existing clients. Old versions keep working while clients migrate.

</details>

---

## Service Communication

<details>
<summary><b>5. When use gRPC over REST?</b></summary>

Internal service-to-service calls where performance matters, strong typing is beneficial, or streaming is needed. REST is better for public APIs.

</details>

<details>
<summary><b>6. What's a service mesh?</b></summary>

Infrastructure layer handling service-to-service communication: discovery, load balancing, encryption (mTLS), observability.

</details>

---

## Event Streaming

<details>
<summary><b>7. How does Kafka differ from a message queue?</b></summary>

Kafka retains events and allows replay. Consumers track position. Message queues delete after consumption.

</details>

<details>
<summary><b>8. Why does partition key matter in Kafka?</b></summary>

Ordering is only guaranteed within a partition. Events needing order must use the same key.

</details>

---

## Data Processing

<details>
<summary><b>9. Batch vs stream processing?</b></summary>

Batch: process data in chunks periodically (analytics, reports). Stream: process as it arrives (real-time dashboards, fraud detection).

</details>

<details>
<summary><b>10. What's the Lambda architecture?</b></summary>

Combining batch (accurate, delayed) and stream (real-time, approximate) layers. Query both and merge results.

</details>

---

## Containers and Kubernetes

<details>
<summary><b>11. What problem does Kubernetes solve?</b></summary>

Orchestrating containers at scale: deployment, scaling, self-healing, service discovery, rolling updates.

</details>

<details>
<summary><b>12. When is Kubernetes overkill?</b></summary>

Single applications, small teams, simple deployment needs. Simpler alternatives exist.

</details>

---

## Scoring

| Score | Assessment |
|-------|------------|
| 10-12 | Solid understanding. Move on. |
| 7-9 | Good grasp. Review weak spots. |
| < 7 | Re-read the chapters. |

---

[Review flashcards](flashcards.md) | [Continue to Level 7](../07-real-world-designs/README.md)
