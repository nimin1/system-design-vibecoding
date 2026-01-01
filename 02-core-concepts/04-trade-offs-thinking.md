# Trade-off Thinking

System design is fundamentally about trade-offs. There are no right answers, only choices with different costs and benefits.

---

## Why Trade-offs Are Unavoidable

Every technical decision involves trade-offs. You cannot optimize for everything simultaneously:

- **Speed vs. cost:** Faster usually means more expensive.
- **Simplicity vs. flexibility:** Simple systems are easier to maintain but handle fewer cases.
- **Consistency vs. availability:** You often can't have perfect both (CAP theorem).
- **Development speed vs. optimization:** Ship fast or make it efficient, rarely both at once.

When someone presents a solution with no downsides, they either don't understand it well enough or aren't being honest.

---

## How to Think About Trade-offs

### 1. Identify What You're Choosing Between

Every decision has options. Name them explicitly.

**Example:** Database choice
- Option A: PostgreSQL
- Option B: MongoDB
- Option C: DynamoDB

Don't compare an option to some ideal. Compare options to each other.

### 2. Understand the Dimensions

Most trade-offs involve multiple dimensions:

| Dimension | Description |
|-----------|-------------|
| Cost | Infrastructure, operational, engineering time |
| Complexity | How hard to implement, debug, operate |
| Performance | Latency, throughput |
| Scalability | How well it handles growth |
| Reliability | Failure modes, recovery |
| Flexibility | How easy to change later |
| Team fit | Does your team know this technology? |

### 3. Know What Matters Most

Different situations prioritize different things:

**Early startup:**
- Speed of development matters most
- Can change everything later
- Operational simplicity important (small team)

**Scaling company:**
- Need to handle growth
- Some decisions are now harder to change
- Can afford some complexity

**Large enterprise:**
- Stability and reliability critical
- Lots of existing integration constraints
- Changes must be gradual

There's no universal "best", only best for your situation.

### 4. Make the Trade-off Explicit

Document what you're trading away and why. Future you (and your team) will want to know:
- What options did you consider?
- What did you choose?
- What are you giving up?
- Under what conditions would you reconsider?

---

## Common Trade-offs in System Design

### Consistency vs. Availability

Covered in depth in the CAP theorem section, but the basic tension:

**Strong consistency:** Every read sees the most recent write. Requires coordination, which slows things down and reduces availability during failures.

**Eventual consistency:** Reads might see stale data temporarily. Faster, more available, but application must handle temporary inconsistency.

**When to choose strong consistency:**
- Financial transactions
- Inventory counts for ordering
- Anything where wrong answers cause real harm

**When eventual consistency is fine:**
- Social media feeds
- Analytics and counts that are "approximately" correct
- User preferences that are rarely read immediately after writing

### Latency vs. Throughput

Sometimes you trade one for the other:

**Batching:** Group operations together. Each individual item waits longer (higher latency), but you process more total items per second (higher throughput).

**Database write patterns:** Fsync on every write is safer (less data loss) but slower. Batching writes risks losing recent data on crash but is faster.

### Simplicity vs. Capability

**Monolith vs. microservices:**
- Monolith: simpler to develop, deploy, debug. Harder to scale specific parts, teams step on each other in one codebase.
- Microservices: more flexible scaling, team independence. Much more complex operationally.

**SQL vs. NoSQL:**
- SQL: rigid schema, but powerful queries and transactions.
- NoSQL: flexible schema, but you lose joins and sometimes transactions.

Starting simpler and adding complexity when needed is usually better than starting complex.

### Cost vs. Performance

More resources usually mean better performance:
- Bigger instances = more capacity
- More replicas = lower latency (geographic) and higher throughput (parallel reads)
- Better storage = faster I/O

But costs add up. Over-provisioning wastes money. Under-provisioning causes problems.

**The question:** What's the cost of poor performance (lost users, lost revenue, wasted engineer time) vs. the cost of more infrastructure?

### Build vs. Buy

**Build custom:**
- Fits your exact needs
- No ongoing licensing costs
- You maintain it forever
- Takes time to build

**Use managed service / third-party:**
- Get capability immediately
- Someone else operates it
- May not fit exactly
- Ongoing cost, potential lock-in

For non-core capabilities, "buy" is usually right. Building a custom database or queue is almost never the right choice. But for core business logic, you may need to build.

### Development Speed vs. Future Flexibility

**Quick and dirty:**
- Ship fast
- Might be hard to change later
- Might accumulate technical debt

**Careful architecture:**
- Takes longer initially
- Easier to change later
- Risk: you might not know what you need, over-engineer

In early product development, speed usually wins, you need to learn if anyone wants what you're building. Once product-market fit is clear, investing in flexibility makes more sense.

---

## Reversible vs. Irreversible Decisions

Not all decisions are equal. Some are easy to undo; others are very hard.

### Easier to Reverse

- Which web framework to use (can rewrite incrementally)
- API endpoint details (can version and migrate)
- UI/frontend choices (can redo with clear interfaces)
- Internal service implementation (if interfaces are stable)

For these, don't over-analyze. Make a reasonable choice and move on.

### Harder to Reverse

- Database choice (data migration is painful)
- Service boundaries in microservices (changing requires moving code and data)
- Core data model and schema (ripples everywhere)
- Authentication and identity system (everything depends on it)
- API contracts with external clients (breaking changes break them)
- Programming language for a large codebase (massive rewrite)

For these, spend more time deciding. Get more input. Consider more carefully.

### The One-Way Door Metaphor

Some decisions are one-way doors, once you go through, you can't easily go back. Others are two-way doors, you can try something and reverse if it doesn't work.

Make two-way door decisions quickly. Make one-way door decisions carefully.

---

## Trade-offs Change Over Time

What's right at one scale or stage isn't right at another.

### At 100 Users

Almost anything works. SQLite in a single server is fine. Don't over-engineer.

**Priority:** Build the product, learn if people want it.

### At 10,000 Users

You probably need a real database, proper hosting. But still mostly straightforward.

**Priority:** Start paying attention to performance, add basic monitoring.

### At 100,000 Users

You likely need caching, might need read replicas, need good observability.

**Priority:** Scale the things that are becoming bottlenecks.

### At 1,000,000+ Users

You need serious infrastructure: load balancing, sharding or horizontal scaling, maybe multi-region.

**Priority:** Reliability and scale become primary concerns.

The point: Don't build for 1M users when you have 100. But also don't build so poorly that you can't get to 10,000.

---

## Avoiding Analysis Paralysis

Trade-off thinking can lead to paralysis, endless comparison of options.

### When to Decide

**Decide when:**
- You've identified the main options
- You understand the key trade-offs
- You know what matters most in your context
- Continued analysis has diminishing returns

**Just pick one when:**
- The decision is easily reversible
- The differences between options are small
- The best way to learn is to try something

### It's Okay to Be Wrong

Some decisions will turn out wrong. That's normal. What matters:
- Did you make a reasonable choice with what you knew?
- Can you detect if it was wrong?
- Can you course-correct?

Building systems that can evolve is more important than perfect upfront design.

---

## Common Mistakes

**Not recognizing trade-offs.** Someone proposes a solution and you don't ask "what are we giving up?" Every solution has costs.

**Optimizing for the wrong thing.** Spending months on performance when the product isn't useful. Doing premature optimization. Focusing on scalability when you don't have users.

**Ignoring team context.** Choosing technology nobody on the team knows because it's "technically best." The operational and learning costs are real.

**Not revisiting decisions.** Context changes. What was right 2 years ago might be wrong now. Periodically revisit big decisions.

**Copying without context.** Netflix uses this architecture because they're Netflix. Google does X because they're Google. Your constraints are different.

---

## What An Experienced Senior Engineer Thinks About

**Second-order effects.** The immediate trade-off is obvious. But what does this decision affect downstream? If we choose X, what does that make easier or harder later?

**Organizational trade-offs.** Technical decisions affect teams. A microservices architecture enables team independence but requires coordination. A monolith is simpler but creates contention.

**Time horizons.** Short-term and long-term answers might differ. What's fastest now might create debt. What's cleanest long-term might be too slow now. Finding the right balance is judgment.

**Principles over individual decisions.** Rather than deciding everything case-by-case, establish principles: "We prefer boring technology." "We don't take dependencies lightly." Then apply them.

**When to pay costs.** Some costs must be paid. Do you pay them now (build the right thing up front) or later (deal with consequences when they materialize)? Neither is always right.

---

## Vibe Engineering Guide

When prompting about trade-off decisions:

**Less useful:**
> "Should I use PostgreSQL or MongoDB?"

**More useful:**
> "I'm building a user-generated content app. Data is somewhat structured: users, posts, comments, likes. I'll need feeds and search. Team knows SQL well, less familiar with MongoDB. We're pre-launch with expected hundreds of users initially. What are the trade-offs of PostgreSQL vs. MongoDB for this case?"

**When you have constraints:**
> "I need a message queue for background job processing. Constraints: team of 2, limited ops expertise, AWS environment, about 10,000 jobs/day. Considering SQS vs. running our own Redis. What are the trade-offs given these constraints?"

**For architecture decisions:**
> "We're debating monolith vs. microservices. Team is 4 engineers. We have two distinct product areas that might scale differently. Current codebase is a Rails monolith with reasonably clean separation. What should we consider? We value simplicity but are worried about future scaling."

Context about your constraints lets AI give relevant trade-off analysis rather than generic pros/cons lists.

---

## Quick Check

<details>
<summary><b>Why is there no "right answer" in system design?</b></summary>

Because every option involves trade-offs. What's "right" depends on your specific constraints: scale, team, budget, timeline, what matters most. Different constraints lead to different right answers.

</details>

<details>
<summary><b>What should you consider when evaluating trade-offs?</b></summary>

What are the options? What dimensions matter (cost, complexity, performance, team fit, flexibility)? What matters most in your specific context? What are you giving up with each choice?

</details>

<details>
<summary><b>What makes a decision hard to reverse?</b></summary>

When changing later requires significant effort: data migrations, rewriting core components, changing contracts with external systems, retraining teams. Database choice and data model are classic examples.

</details>

<details>
<summary><b>When should you stop analyzing and just decide?</b></summary>

When it's easily reversible. When options are similar. When continued analysis has diminishing returns. When the best way to learn is to try. Don't over-analyze two-way doors.

</details>

<details>
<summary><b>Why shouldn't you copy what big companies do?</b></summary>

Their constraints are different. They operate at different scale, with different resources, different organizational structures. Their solutions are optimized for their problems. Copying them adds complexity that doesn't benefit you.

</details>

---

Next: [CAP Theorem Simplified](05-cap-theorem-simplified.md)
