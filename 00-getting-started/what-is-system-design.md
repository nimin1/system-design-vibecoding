# What is System Design?

System design is figuring out how to structure software so it actually works under real conditions with real traffic, real data and real failures.

It's the set of decisions you make *before* you write most of the code: what databases to use, how different services talk to each other, where to put caching, how to handle failures.

---

## A Quick Clarification

"System design" can mean different things:

1. **Interview context:** A 45 minute exercise where you sketch out a system architecture on a whiteboard
2. **Real work context:** The ongoing process of deciding how to structure software

This repo focuses on the concepts themselves, which apply to both.

---

## What Kinds of Decisions?

When designing a system, you are answering questions like:

| Question | The underlying concern |
|----------|----------------------|
| "Where does this data live?" | Database selection, schema design |
| "What happens when this server crashes?" | Redundancy, failover |
| "Will this work with 100x more users?" | Scalability |
| "How do these services communicate?" | APIs, message passing |
| "What happens if users spam requests?" | Rate limiting, resource protection |
| "How do we update this without downtime?" | Deployment strategy |

---

## Why It's Separate From Coding

You can write perfectly correct code that fails at scale. Here is a simple example:

Say you build a feature that lists all comments on a post. You write correct code that queries the database and returns the results. It works in development. It works in testing.

Then an influencer links to your site and suddenly one post has 50,000 comments. Your query takes 30 seconds. The database connection pool fills up. Other requests start timing out. Your site goes down.

The *code* was fine. The *design* didn't account for the possibility of many comments.

System design is about thinking through these scenarios ahead of time.

---

## The Common Building Blocks

Most systems are built from a relatively small set of components:

| Component | What it does |
|-----------|--------------|
| **Load balancer** | Distributes incoming requests across multiple servers |
| **Cache** | Stores frequently accessed data in memory for faster retrieval |
| **Database** | Persists data durably |
| **CDN** | Serves static files from servers close to users |
| **Message queue** | Lets services communicate asynchronously |
| **API gateway** | Single entry point that routes requests to appropriate services |

The skill is knowing when to use each one and how they fit together.

---

## Trade-offs, Not Right Answers

Almost every system design decision involves trade-offs:

- **Caching** improves read speed but creates consistency challenges
- **Replication** improves reliability but increases complexity
- **Microservices** improve modularity but add operational overhead
- **Normalization** reduces data redundancy but requires more joins

There's rarely a "correct" answer. There are approaches that fit your constraints better or worse.

---

## Common Misconceptions

**"I'll figure it out when I need to scale"**

Sometimes true for prototypes. But some architectural decisions are expensive to change later. Migrating databases or splitting a monolith is much harder than getting the rough structure right initially.

**"This only matters at big company scale"**

The concepts apply at smaller scales too. A 3 person startup still benefits from basic load balancing, caching, and database indexing. The scale changes; the concepts don't.

**"There's a right way to design every system"**

Different constraints lead to different designs. A banking system has different requirements than a social media app. Context matters.

---

## The Thinking Process

A typical approach to system design:

1. **Clarify requirements**: What exactly are we building? What are the constraints?
2. **Estimate scale**: How much data? How many requests? Read-heavy or write-heavy?
3. **Outline high-level structure**: What are the main components?
4. **Dive into key pieces**: How does the most critical part work?
5. **Consider edge cases**: What happens under failure or unusual load?

This repo goes through each of these areas in detail.

---

## Quick Check

<details>
<summary><b>What does "system design" refer to?</b></summary>

The process of making architectural decisions about how a system is structured: what components exist, how they communicate, where data is stored, how to handle failures and scale.

</details>

<details>
<summary><b>Why is it different from coding?</b></summary>

Coding is implementation, making features work correctly. System design is architecture deciding how the pieces fit together so the whole thing works reliably at scale.

</details>

<details>
<summary><b>Why can't you just fix architecture problems later?</b></summary>

Some decisions are deeply embedded and expensive to change. Database schema changes, service boundaries, and data flow patterns become harder to modify as a system grows and more code depends on them.

</details>

---

Next: [Why This Helps with AI Tools](why-it-matters-for-vibecoders.md)
