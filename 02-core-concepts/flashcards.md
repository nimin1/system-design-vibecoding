# Level 2 Flashcards

Quick reference for core concepts.

---

## Latency and Throughput

| Front | Back |
|-------|------|
| What is latency? | Time for a single operation to complete. |
| What is throughput? | Operations per unit time (e.g., requests/second). |
| What's p99 latency? | 99% of requests are faster than this value. Shows tail latency. |
| What happens to latency near max throughput? | It increases sharply. Requests queue up. |

---

## Availability and Reliability

| Front | Back |
|-------|------|
| "Three nines" availability? | 99.9%, about 8.76 hours downtime per year. |
| Why does adding dependencies hurt availability? | Availabilities multiply. Two 99.9% services → 99.8% combined. |
| MTBF and MTTR? | Mean Time Between Failures and Mean Time To Recovery. Availability = MTBF / (MTBF + MTTR). |
| What's a SPOF? | Single Point of Failure, a component whose failure takes down the system. |

---

## Scalability

| Front | Back |
|-------|------|
| Vertical scaling? | Bigger machine. Simple but has limits. |
| Horizontal scaling? | More machines. Scales further but more complex. |
| Why statelessness matters for scaling? | Any instance can handle any request. Easy to add capacity. |
| Read replicas vs sharding? | Replicas: copies for read scaling. Sharding: different data on different databases. |

---

## Trade-offs

| Front | Back |
|-------|------|
| Why no "right answer" in system design? | Every option has trade-offs. Best choice depends on your constraints. |
| Consistency vs availability trade-off? | Stronger consistency often means lower availability or higher latency. |
| What's a reversible vs hard-to-reverse decision? | Reversible: frameworks, API details. Hard: database choice, service boundaries. |

---

## CAP Theorem

| Front | Back |
|-------|------|
| What does CAP stand for? | Consistency, Availability, Partition tolerance. |
| CAP theorem says? | Pick two of three. Since P is required, choose between C and A during partitions. |
| When to pick CP? | When correctness matters more than availability (e.g., financial data). |
| When to pick AP? | When showing something (even stale) is better than errors (e.g., social feeds). |

---

[Continue to Level 3](../03-building-blocks/README.md)
