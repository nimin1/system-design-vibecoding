# System Design Interview Framework

A structured approach to system design interviews.

---

## The Interview Structure

Most system design interviews are 45-60 minutes:

| Phase | Time | Focus |
|-------|------|-------|
| Requirements | 5-10 min | Clarify what to build |
| Estimation | 5 min | Understand scale |
| High-level design | 10-15 min | Draw the architecture |
| Deep dive | 15-20 min | Explore specific components |
| Wrap-up | 5 min | Trade-offs, extensions |

---

## Phase 1: Gather Requirements

**Do not start designing immediately.** Ask questions first.

### Functional Requirements

What does the system do?

- "What are the core features?"
- "Who are the users?"
- "What are the main use cases?"

### Non-Functional Requirements

How should it perform?

- "How many users?"
- "What's acceptable latency?"
- "What level of consistency is needed?"
- "What's the read/write ratio?"

### Constraints

- "Any specific technologies required?"
- "Any existing systems to integrate with?"
- "Budget or cost constraints?"

### Example

**Interviewer:** "Design a URL shortener."

**You:** "Let me clarify the requirements:
- Functional: Given a URL, create a short URL. Given a short URL, redirect to original.
- Do we need custom URLs? Analytics? Expiration?
- Scale: How many URLs per day? How many redirects per second?
- Consistency: Is it okay if a new URL isn't immediately accessible globally?"

---

## Phase 2: Back-of-Envelope Estimation

Quick math to understand scale:

```
"Let's estimate scale:
- 100M new URLs per month = 40 writes/second (100M / 2.6M seconds)
- 10:1 read/write ratio = 400 reads/second
- Storage: 100M × 500 bytes × 12 months × 5 years = ~3TB

This is a read-heavy, moderate-scale system."
```

Show your work. Rough numbers are fine.

---

## Phase 3: High-Level Design

Draw the major components:

```
"Here's the high-level architecture:

User → Load Balancer → Web Servers → Cache → Database

For URL creation:
1. Generate short code
2. Store mapping in database
3. Return short URL

For redirect:
1. Look up short code
2. Return redirect to original URL"
```

**Draw a diagram.** Use boxes and arrows. Label components.

**Explain data flow.** Walk through how a request moves through the system.

---

## Phase 4: Deep Dive

Interviewer will ask about specific areas. Be prepared to go deeper on:

### Database

- "What database would you use?"
- "How would you schema look?"
- "How do you handle scale?"

### Specific Components

- "How exactly would caching work here?"
- "What happens when the cache is full?"

### Edge Cases

- "What if the database goes down?"
- "How do you handle duplicates?"
- "What about hot keys?"

### Scaling

- "How would this change at 10x scale?"
- "What's the bottleneck?"

---

## Phase 5: Trade-offs and Extensions

Show you understand it's not perfect:

"Trade-offs in this design:
- We're using cache-aside, so there's brief inconsistency on writes
- We chose eventual consistency for multi-region, which means..."

"If we had more time, I'd explore:
- Custom analytics
- Multi-region deployment
- A/B testing different redirect behavior"

---

## Common Mistakes

### Not Clarifying Requirements

Starting to design without understanding the problem. You'll build the wrong thing.

### Going Too Deep Too Early

Optimizing the hash function before drawing the architecture. Start high-level.

### Not Considering Scale

Designing for 100 users when the system needs to handle millions.

### Not Acknowledging Trade-offs

Every design has trade-offs. Pretending yours is perfect is a red flag.

### Silent Thinking

The interviewer can't see your thoughts. Explain as you go.

### Memorizing Designs

Interviewers detect memorized answers. Understand the principles, not a script.

---

## Structuring Your Answers

Use this template:

**State what you're doing:**
> "First, let me clarify the requirements..."
> "Now I'll do some quick estimates..."
> "Let me draw the high-level architecture..."
> "Let's dive into the database design..."

**Explain your reasoning:**
> "I'm choosing PostgreSQL because we need ACID transactions for..."
> "I'll add caching here because this is read-heavy..."

**Acknowledge trade-offs:**
> "This adds complexity, but..."
> "The trade-off is..."

---

## Topics to Know

Be ready to discuss:

| Topic | Key Concepts |
|-------|--------------|
| Databases | SQL vs NoSQL, indexing, replication, sharding |
| Caching | Strategies, invalidation, Redis/Memcached |
| Load balancing | Algorithms, L4 vs L7, health checks |
| Message queues | Kafka, async processing, at-least-once |
| APIs | REST, rate limiting, authentication |
| Storage | Object storage, CDNs |
| Consistency | CAP, eventual consistency |
| Scaling | Horizontal, vertical, bottlenecks |

---

## Practice Approach

**Step 1:** Pick a system (URL shortener, chat, newsfeed).

**Step 2:** Set a timer for 45 minutes.

**Step 3:** Practice out loud, explaining your thinking.

**Step 4:** Compare to reference designs.

**Step 5:** Repeat with different systems.

---

## Vibe Engineering Guide

Use AI to practice:

**Practice prompt:**
> "Act as an interviewer for a system design interview. Ask me to design a rate limiter. After I give requirements, ask clarifying follow-up questions. After my high-level design, ask about specific components. Challenge my choices."

**Review prompt:**
> "Here's my design for a URL shortener: [your design]
> - What's weak about it?
> - What questions would an interviewer ask?
> - What did I miss?"

**Learning prompt:**
> "Explain the trade-offs between SQL and NoSQL for a chat application. Give me a scenario where each would be better."

---

## Quick Check

<details>
<summary><b>What should you do first in a system design interview?</b></summary>

Clarify requirements. Ask about functional requirements, scale, and constraints before designing.

</details>

<details>
<summary><b>Why explain your thinking out loud?</b></summary>

The interviewer evaluates your thought process, not just the final design. They can't read your mind.

</details>

---

Return to [AI-Assisted Development README](README.md)
