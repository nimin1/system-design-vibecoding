# System Design Principles

The fundamental thinking that guides good system design decisions.

---

## Start With Requirements

Every design starts with understanding what you're building.

### Functional Requirements

What does the system do? What are the features?

- Users can upload photos
- Users can follow other users
- Users see a feed of photos from people they follow

### Non-Functional Requirements

How does the system behave? Quality attributes.

- Support 100 million daily active users
- 99.99% availability
- Photo loads in under 200ms
- Data can never be lost

### Ask Clarifying Questions

Requirements documents are rarely complete. Ask:
- What's the expected scale? (users, data volume, requests/sec)
- What's the read/write ratio?
- What consistency level is needed?
- What are the latency requirements?
- What's the budget/team size?

**The answer changes the design.** A system for 1,000 users differs from one for 100 million.

---

## Constraints Shape Solutions

### Scale Constraints

- 1,000 users: single server might work
- 1 million users: need horizontal scaling
- 1 billion users: need distributed systems, geographic distribution

### Budget Constraints

- Startup: managed services, minimize ops
- Enterprise: can afford dedicated teams, custom solutions

### Team Constraints

- Small team: simpler architecture, less to maintain
- Large team: can handle microservices complexity

### Time Constraints

- Need it tomorrow: use off-the-shelf solutions
- Long runway: can invest in custom solutions

**Don't design for constraints you don't have.** Building for billion-user scale when you have 1,000 users is waste.

---

## Make Trade-offs Explicit

Every decision has trade-offs. Good engineers make them explicit.

### Common Trade-offs

| Choose | Get | Sacrifice |
|--------|-----|-----------|
| Consistency | Strong consistency | Availability during partitions |
| Availability | Always writable | Consistency (might serve stale) |
| Performance | Speed | Cost, complexity |
| Simplicity | Maintainability | Features, flexibility |
| Customization | Flexibility | Development time |

### Framework for Trade-offs

1. **Identify the options**
2. **List pros and cons of each**
3. **Consider the context** (scale, team, requirements)
4. **Make a decision and document why**

The decision process is as important as the decision.

---

## Design for Failure

Things will break. Design assuming they will.

### What Fails

- Servers crash
- Networks partition
- Disks fill up
- Dependencies slow down or stop
- Deploys introduce bugs
- Users do unexpected things

### How to Handle

**Timeouts:** Don't wait forever.

**Retries:** Try again for transient failures.

**Circuit breakers:** Stop calling failing dependencies.

**Fallbacks:** Have alternative behavior when things fail.

**Graceful degradation:** Do less rather than failing completely.

**Redundancy:** No single point of failure.

---

## Design for Observability

You can't fix what you can't see.

### Build In From the Start

- Structured logging with correlation IDs
- Metrics for key operations
- Distributed tracing across services
- Health check endpoints

### Ask: When This Breaks at 2 AM

- How will you know something is wrong? (Alerting)
- Where will you look first? (Dashboards)
- How will you find the root cause? (Logs, traces)
- How will you verify the fix? (Metrics)

If you can't answer these, your observability isn't ready.

---

## Design for Evolution

Requirements change. Systems must adapt.

### Loose Coupling

Components should have minimal knowledge of each other. Changes in one shouldn't require changes in others.

**Tight coupling:** Order Service directly calls Inventory Service, Payment Service, Notification Service with knowledge of their internals.

**Loose coupling:** Order Service publishes "OrderCreated" event. Other services subscribe and react.

### Clear Interfaces

Define boundaries between components. Changes within a component don't affect others as long as the interface is maintained.

### Reversibility

Some decisions are easy to reverse (choice of logging library). Others are hard (choice of database, data model).

**For irreversible decisions:** Be more careful. Prototype. Get feedback.

---

## Simple > Complex

Complexity has costs:
- Harder to understand
- More things can break
- More to maintain
- Harder to onboard new team members

### Choose Boring Technology

Well-understood technology has:
- Known failure modes
- Good tooling
- Experienced engineers available
- Proven at scale

Novel technology has unknown unknowns.

### YAGNI (You Aren't Gonna Need It)

Don't build for hypothetical future requirements. Build for what you need now. Add complexity when it's required, not before.

### Minimum Viable Architecture

The simplest architecture that meets requirements.

- Start with a monolith, not microservices
- Start with one database, not sharded
- Start with one region, not global

Add complexity as scale demands.

---

## Numbers Everyone Should Know

Latency comparison:

| Operation | Latency |
|-----------|---------|
| L1 cache reference | 0.5 ns |
| L2 cache reference | 7 ns |
| Main memory reference | 100 ns |
| SSD random read | 150 μs |
| HDD seek | 10 ms |
| Network round trip (same DC) | 0.5 ms |
| Network round trip (cross-country) | 40 ms |
| Network round trip (intercontinental) | 100-200 ms |

Capacity estimates:

| Metric | Rule of Thumb |
|--------|---------------|
| Requests/sec for single server | 1,000-10,000 |
| DB queries/sec for PostgreSQL | 5,000-20,000 simple |
| Redis ops/sec | 100,000+ |
| Kafka messages/sec | 100,000+ per broker |

These help you reason about feasibility without needing to benchmark everything.

---

## The Back-of-Envelope Estimation

Before diving into design, estimate scale.

### The Process

1. **Identify key metrics:** DAU, storage, bandwidth, requests/sec
2. **Make reasonable assumptions:** Based on similar systems
3. **Calculate:** Often just multiplication
4. **Sanity check:** Does the result seem reasonable?

### Example: File Storage Service

**Assumptions:**
- 100 million users
- 10% upload daily
- Average file: 1 MB
- Files kept for 1 year

**Storage per day:** 100M × 10% × 1 MB = 10 TB/day

**Storage per year:** 10 TB × 365 = 3.6 PB

**Uploads per second:** 10M / 86400 ≈ 115/sec

Now you know the scale you're designing for.

---

## Iterative Design

Don't try to get everything right in one pass.

### The Process

1. **Understand requirements**
2. **High-level design** (boxes and arrows)
3. **Identify components** (what each box does)
4. **Drill into critical components** (most complex or risky)
5. **Identify bottlenecks** (where will it break?)
6. **Refine** (address bottlenecks)

### Get Feedback Early

Share your design. Get critique. Revise. Better to find problems in design than in production.

---

## Document Decisions

Decisions made today will be forgotten tomorrow.

### What to Document

- What decision was made
- What alternatives were considered
- Why this option was chosen
- What trade-offs were accepted
- When to revisit the decision

### Architecture Decision Records (ADRs)

Lightweight format for recording decisions:

```
# ADR 1: Use PostgreSQL for primary database

## Status
Accepted

## Context
We need a primary database for user and order data.

## Decision
Use PostgreSQL.

## Consequences
- Positive: Team has experience, good tooling
- Negative: May need to shard if we exceed 10TB
```

---

## Vibe Engineering Guide

When prompting about system design:

**Less useful:**
> "Design a system"

**More useful:**
> "Design a photo sharing system with:
> - Functional: upload photos, follow users, view feed
> - Non-functional: 10M DAU, 99.9% availability, feed loads in 500ms
> - Constraints: team of 5, limited ops capacity
>
> Start with high-level architecture, then focus on feed generation (likely the hardest part) and how to handle photo storage at scale."

**For trade-off decisions:**
> "We're designing a payment system. We need to decide between eventual consistency (async processing, better availability) and strong consistency (sync processing, simpler correctness). What factors should guide this decision for a payment system specifically?"

---

## Quick Check

<details>
<summary><b>Why start with requirements?</b></summary>

Requirements determine constraints. A system for 1,000 users differs entirely from one for 100 million. Non-functional requirements (latency, availability) drive architectural choices.

</details>

<details>
<summary><b>Why choose boring technology?</b></summary>

Boring technology has known failure modes, good tooling, available expertise, and proven track records. Novel technology has unknown unknowns that create risk.

</details>

<details>
<summary><b>What does "design for failure" mean?</b></summary>

Assume things will fail. Build in timeouts, retries, circuit breakers, fallbacks, and redundancy. Don't wait for failures to happen - plan for them from the start.

</details>

<details>
<summary><b>Why document decisions?</b></summary>

Context is forgotten over time. When someone asks "why did we do it this way?" in a year, documentation answers. It also surfaces trade-offs that might need revisiting.

</details>

<details>
<summary><b>What's the point of back-of-envelope estimation?</b></summary>

Understand scale before designing. Different scales require different approaches. Estimation tells you if your approach is even feasible (10 PB storage is different from 10 GB).

</details>

---

Next: [Observability](02-observability.md)
