# Level 2: Core Concepts

Fundamental ideas that come up constantly in system design.

---

## What's Here

| Chapter | Topic | Key Concepts |
|---------|-------|--------------|
| [Latency and Throughput](01-latency-and-throughput.md) | Performance dimensions | P50/P99, bandwidth, Little's Law |
| [Availability and Reliability](02-availability-and-reliability.md) | Keeping systems running | Nines, SLAs, MTBF/MTTR |
| [Scalability Basics](03-scalability-basics.md) | Handling growth | Horizontal vs vertical, stateless |
| [Trade-off Thinking](04-trade-offs-thinking.md) | Core design skill | Decision frameworks, reversibility |
| [CAP Theorem](05-cap-theorem-simplified.md) | Distributed constraints | Consistency, availability, partitions |

---

## Why This Matters

These concepts are the vocabulary of system design discussions. When someone says "we need three nines of availability" or "this is a latency-sensitive path," these are the ideas they're referencing.

Understanding trade-offs is the single most important skill in system design. Every decision involves giving something up to gain something else.

---

## How This Helps with AI Tools

After this section, your prompts can include actual requirements:

**Before:**
> "Build me an API"

**After:**
> "Build an API that handles 1000 requests/second with P99 latency under 100ms. Eventual consistency is acceptable. Target 99.9% availability."

---

## Progress

- [ ] Latency and Throughput
- [ ] Availability and Reliability
- [ ] Scalability Basics
- [ ] Trade-off Thinking
- [ ] CAP Theorem
- [ ] [Quiz](quiz.md)
- [ ] [Flashcards](flashcards.md)

---

[← Back to Level 1](../01-foundation/README.md) | [Level 3 →](../03-building-blocks/README.md)
