# Level 2 Quiz

Test your understanding of core concepts.

---

## Latency and Throughput

<details>
<summary><b>1. What's the difference between latency and throughput?</b></summary>

Latency is time for one operation. Throughput is operations per unit time. They're related but not the same, a system can have low latency but low throughput, or high throughput but high latency.

</details>

<details>
<summary><b>2. Why measure p99 latency instead of average?</b></summary>

Averages hide tail latency. If 99% of requests are fast but 1% are very slow, the average looks okay but many users have bad experiences. p99 shows you what the slow requests look like.

</details>

<details>
<summary><b>3. What happens to latency as a system approaches its throughput limit?</b></summary>

Latency increases, often sharply. Requests start queueing for resources. This is why you don't run systems at 100% capacity.

</details>

---

## Availability and Reliability

<details>
<summary><b>4. What does "three nines" (99.9%) availability mean in practice?</b></summary>

About 8.76 hours of downtime per year, or ~44 minutes per month.

</details>

<details>
<summary><b>5. How does adding dependencies affect availability?</b></summary>

It reduces it. If your system depends on two services at 99.9% availability each, overall availability is 0.999 × 0.999 = 99.8%.

</details>

<details>
<summary><b>6. Two ways to improve availability?</b></summary>

1. Increase MTBF (mean time between failures), fewer failures
2. Decrease MTTR (mean time to recovery), faster recovery

Often faster recovery is more practical than preventing all failures.

</details>

---

## Scalability

<details>
<summary><b>7. What's the difference between vertical and horizontal scaling?</b></summary>

Vertical: bigger machine (more CPU, RAM). Has limits.
Horizontal: more machines. More complex but scales further.

</details>

<details>
<summary><b>8. Why is statelessness important for horizontal scaling?</b></summary>

Stateless services can have any request handled by any instance. You don't need to route specific users to specific servers, making it easy to add capacity.

</details>

<details>
<summary><b>9. What's the difference between read replicas and sharding?</b></summary>

Read replicas: copies of the same data for read scaling. Writes still go to one primary.
Sharding: different data on different databases. Distributes both reads and writes.

</details>

---

## Trade-offs

<details>
<summary><b>10. Why isn't there one "right" architecture?</b></summary>

Every option has trade-offs. The best choice depends on your specific constraints: scale, team size, budget, what matters most for your use case.

</details>

<details>
<summary><b>11. What makes a decision hard to reverse?</b></summary>

Decisions involving data (database choice, schema), fundamental architecture (service boundaries), or requiring migration of existing systems are hard to change later.

</details>

---

## CAP Theorem

<details>
<summary><b>12. What does CAP theorem state?</b></summary>

A distributed system can only guarantee two of: Consistency, Availability, Partition tolerance. Since partition tolerance is required, the practical choice is between consistency and availability during network partitions.

</details>

<details>
<summary><b>13. When would you choose AP (availability) over CP (consistency)?</b></summary>

When showing something (even if stale) is better than showing nothing. Social media feeds, caching, non-critical data.

</details>

<details>
<summary><b>14. When would you choose CP (consistency) over AP (availability)?</b></summary>

When correctness matters more than availability. Bank balances, inventory counts, booking systems.

</details>

---

## Scoring

| Score | Assessment |
|-------|------------|
| 12-14 | Solid understanding. Move on. |
| 9-11 | Good grasp. Review weak spots. |
| < 9 | Worth re-reading the chapters. |

---

[Review flashcards](flashcards.md) | [Continue to Level 3](../03-building-blocks/README.md)
