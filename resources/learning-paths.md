# Learning Paths

Where to start based on your background.

---

## Complete Beginner

Start with foundations, build up to distributed concepts.

**Week 1-2: Foundations**
1. [What is System Design](../00-getting-started/what-is-system-design.md)
2. [How the Internet Works](../01-foundation/02-how-the-internet-works.md)
3. [HTTP and APIs](../01-foundation/03-http-and-apis.md)

**Week 3-4: Core Concepts**
1. [Latency and Throughput](../02-core-concepts/01-latency-and-throughput.md)
2. [Availability](../02-core-concepts/02-availability-and-reliability.md)
3. [Scalability](../02-core-concepts/03-scalability.md)

**Week 5-6: Building Blocks**
1. [Load Balancers](../03-building-blocks/01-load-balancers.md)
2. [Caching](../03-building-blocks/02-caching.md)
3. [CDNs](../03-building-blocks/03-cdns.md)

**Week 7-8: Data**
1. [Database Indexing](../04-data-layer/01-database-indexing.md)
2. [Replication](../04-data-layer/02-replication.md)
3. [Choosing a Database](../04-data-layer/04-choosing-database.md)

**After that:** Pick real-world designs that interest you.

---

## Experienced Dev, New to Distributed Systems

You write code but haven't designed systems at scale.

**Start here:**
1. [Scalability](../02-core-concepts/03-scalability.md) - Vertical vs horizontal
2. [CAP Theorem](../02-core-concepts/05-cap-theorem-simplified.md) - The fundamental trade-off

**Core infrastructure:**
1. [Caching](../03-building-blocks/02-caching.md)
2. [Message Queues](../05-distributed-systems/01-message-queues.md)
3. [Replication](../04-data-layer/02-replication.md)
4. [Sharding](../04-data-layer/03-sharding.md)

**Distributed challenges:**
1. [Eventual Consistency](../05-distributed-systems/02-eventual-consistency.md)
2. [Idempotency](../05-distributed-systems/03-idempotency.md)
3. [Distributed Locking](../05-distributed-systems/06-distributed-locking.md)

**Then:** Work through real-world designs, starting with [URL Shortener](../07-real-world-designs/01-url-shortener.md) (simpler) to [Payment System](../07-real-world-designs/07-payment-system.md) (complex).

---

## Interview Prep

Focus on what interviewers ask about.

**Must-know concepts:**
1. [Scalability](../02-core-concepts/03-scalability.md)
2. [CAP Theorem](../02-core-concepts/05-cap-theorem-simplified.md)
3. [Capacity Planning](../08-senior-engineer-thinking/04-capacity-planning.md)

**Must-know building blocks:**
1. [Load Balancers](../03-building-blocks/01-load-balancers.md)
2. [Caching](../03-building-blocks/02-caching.md)
3. [Message Queues](../05-distributed-systems/01-message-queues.md)
4. [Sharding](../04-data-layer/03-sharding.md)

**Practice designs (commonly asked):**
1. [URL Shortener](../07-real-world-designs/01-url-shortener.md)
2. [Rate Limiter](../07-real-world-designs/02-rate-limiter.md)
3. [Chat System](../07-real-world-designs/03-chat-system.md)
4. [News Feed](../07-real-world-designs/05-news-feed.md)

**Interview cheat sheet:** [Numbers and patterns to know](./numbers-to-know.md)

**Approach:** Use the framework in [System Design Principles](../08-senior-engineer-thinking/01-system-design-principles.md).

---

## AI-Augmented Development Focus

Learn to direct AI tools effectively.

**Core knowledge first:**
Work through any of the paths above. You need to understand the concepts before you can prompt effectively.

**Then vibecoding mastery:**
1. [Effective Prompting](../09-vibecoding-masterclass/01-effective-prompting.md)
2. [Validating Output](../09-vibecoding-masterclass/02-validating-output.md)
3. [Production Systems](../09-vibecoding-masterclass/03-production-systems.md)

**Quick reference:**
- [Prompt Patterns](./prompt-patterns.md)
- [Common AI Mistakes](./ai-mistakes.md)

**Practice:** Take any real-world design, and practice prompting AI to implement pieces of it. Validate the output against what you learned.

---

## How to Use Any Path

1. **Read the concept** - Understand what and why
2. **Do the Quick Check** - Test yourself
3. **Read the Vibe Engineering Guide** - Learn how to ask AI about it
4. **Practice prompting** - Actually try the prompts
5. **Move on** - Don't get stuck perfecting one topic

The goal is broad understanding, not memorization. You're building intuition for what questions to ask.

---

Return to [Resources](../README.md)
