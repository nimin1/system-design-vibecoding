# System Design for AI Augmented Developers

System Design explained from first principles to senior engineer-level thinking, with a focus on AI-assisted (vibecoding) development. Learn how to reason about systems, understand trade-offs, and validate AI-generated designs beyond just working code.

---

## What This Is

This repository covers system design from fundamentals to advanced topics. It's designed to:

1. **Build understanding progressively**  -  Each level builds on the previous
2. **Connect concepts to AI prompting**  -  Learn how each concept improves your ability to work with AI tools
3. **Be practical**  -  Focus on what you'll actually use
4. **Prepare for interviews**  -  Covers topics common in system design interviews

---

## Contents

| Level | Topic | Chapters | What You'll Learn |
|-------|-------|----------|-------------------|
| 0 | [Getting Started](00-getting-started/README.md) | 3 | What this is, how to use it |
| 1 | [Foundation](01-foundation/README.md) | 5 | Client-server, networking, HTTP, databases |
| 2 | [Core Concepts](02-core-concepts/README.md) | 5 | Latency, availability, scalability, CAP theorem |
| 3 | [Building Blocks](03-building-blocks/README.md) | 10 | Load balancers, caching, CDNs, rate limiting, logging |
| 4 | [Data Layer](04-data-layer/README.md) | 4 | Indexing, replication, sharding, database selection |
| 5 | [Distributed Systems](05-distributed-systems/README.md) | 6 | Message queues, consensus, transactions, locking |
| 6 | [Architecture Patterns](06-architecture-patterns/README.md) | 7 | Microservices, Kafka, data processing, Kubernetes |
| 7 | [Real-World Designs](07-real-world-designs/README.md) | 17 | Chat, payments, crawlers, presence, proximity |
| 8 | [Senior Engineer Thinking](08-senior-engineer-thinking/README.md) | 9 | Performance, incidents, security, cost, reviews |
| 9 | [Vibe Engineering](09-vibecoding-masterclass/README.md) | 5 | Prompting, validation, production systems |

**Total: 71 chapters** covering system design from fundamentals to senior-level expertise.

---

## Quick Paths

### Path 1: Complete Learning (Recommended)

Go through levels 0-9 in order. Takes 2-4 weeks with consistent study.

### Path 2: Interview Prep

Focus path for system design interviews:
- Level 2: Core Concepts (fundamentals)
- Level 3: Building Blocks (components you'll discuss)
- Level 7: Real-World Designs (practice problems)
- Level 9: Interview Framework

### Path 3: AI Prompting Focus

To immediately improve AI collaboration:
- [HOW-TO-VIBECODE.md](HOW-TO-VIBECODE.md)  -  Start here
- Level 9: Vibe Engineering Masterclass

---

## AI-Assisted Development Focus

Every topic in this repo connects to better AI prompting:

| You Learn | You Can Prompt |
|-----------|---------------|
| Load balancing | "Running behind ALB with 3 instances, health checks on /health" |
| Caching patterns | "Cache-aside pattern with Redis, TTL 5 minutes" |
| Database indexing | "Add composite index on (user_id, created_at)" |
| Message queues | "Publish to Kafka, consumer is idempotent" |
| Circuit breakers | "Add circuit breaker for payment service calls" |
| Consistency models | "Eventual consistency is fine, use async replication" |

The more concepts you know, the more precisely you can direct AI tools.

---

## Each Section Includes

- **Chapters**  -  In-depth content with real-world examples
- **Common Mistakes**  -  What to avoid and why
- **Senior-Level Thinking**  -  What senior engineers consider
- **Vibe Engineering Guide**  -  How to prompt AI for each topic
- **Quick Check Questions**  -  Test your understanding
- **Flashcards**  -  Quick review cards
- **Quiz**  -  Self-assessment

---

## Resources

- [Learning Paths](resources/learning-paths.md) - Where to start based on your background
- [Glossary](resources/glossary.md) - Terms and definitions
- [Recommended Reading](resources/recommended-reading.md) - Books and blogs
- [Prompt Patterns](resources/cheat-sheets/prompt-patterns.md) - Quick reference for AI prompting
- [Common AI Mistakes](resources/cheat-sheets/ai-mistakes.md) - What to check in AI output
- [Numbers to Know](resources/cheat-sheets/numbers-to-know.md) - Estimation reference
- [How to Vibecode](HOW-TO-VIBECODE.md) - Complete AI prompting guide

---

## Contributing

Found a mistake or have an improvement? See [Contributing](community/contribute.md).

---

## How to Use This

**For learning:** Go through levels in order. Each builds on previous.

**For reference:** Jump to specific topics when needed.

**For AI development:** Use concepts to write better prompts. See [HOW-TO-VIBECODE.md](HOW-TO-VIBECODE.md).

**For interviews:** Focus on Levels 2, 3, 7, and the interview framework in Level 9.

---

The goal: understand systems well enough to architect them yourself, and direct AI tools to implement them correctly.

---

## Support the Project

If you find this guide helpful, please **star the repository**! It helps others find this resource and encourages further updates. 🌟
