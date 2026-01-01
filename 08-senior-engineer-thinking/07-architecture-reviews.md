# Architecture Reviews

Evaluating designs before building catching problems when they're cheap to fix.

---

## Why Architecture Reviews?

Architecture decisions are expensive to change.

**Bad decision caught in review:** Hours to revise document.
**Bad decision caught in production:** Months to rewrite.

Reviews bring multiple perspectives, catch blind spots, spread knowledge.

---

## When to Do Reviews

### Triggers

- New system or service
- Major changes to existing system
- New technology adoption
- Integration with external systems
- Significant scaling requirements
- Security-sensitive features

### Not Every Change

Minor changes don't need formal review. Use judgment:
- Adding a field to existing API? Probably not.
- Adding a new microservice? Yes.
- Changing database technology? Definitely.

---

## Review Process

### 1. Write It Down

Author documents the proposed design.

**Forces clarity:** Writing reveals fuzzy thinking.
**Enables async review:** Reviewers can read beforehand.
**Creates artifact:** Future reference for decisions.

### 2. Async Review

Reviewers read document, add comments, ask questions.

**Before meeting:** Everyone reads and thinks.
**Comments documented:** Thoughtful, not off-the-cuff reactions.

### 3. Synchronous Discussion

Meeting to discuss major points.

**Focus on:** Open questions, trade-offs, disagreements.
**Not for:** Reading the document together.

### 4. Decision and Document

Resolve discussions. Document outcome.

**Decision:** Approved, approved with changes, or needs rework.
**Changes:** Author updates document with agreed changes.

---

## What to Include in Design Doc

### Context

- What problem are we solving?
- Why now?
- What's the scope?

### Goals and Non-Goals

**Goals:** What this design must achieve.
**Non-goals:** What we explicitly won't address (to limit scope).

### Current State

How things work today (if applicable).

### Proposed Design

- High-level architecture
- Key components and responsibilities
- Data model
- APIs
- Data flow

### Alternatives Considered

What other approaches were evaluated?

Why were they rejected?

### Trade-offs

What are we giving up with this approach?

### Risks and Mitigations

What could go wrong?

How will we address it?

### Rollout Plan

How will we deploy this?

Can we roll back?

### Open Questions

What haven't we figured out yet?

---

## What Reviewers Look For

### Requirements Alignment

Does this actually solve the problem?

Are there missed requirements?

### Scalability

Will it handle expected load?

How does it grow?

### Reliability

What happens when things fail?

Are there single points of failure?

### Security

What are the attack vectors?

How is data protected?

### Operational Concerns

How will we monitor this?

How will we debug issues?

### Maintainability

Is this understandable?

Can we change it later?

### Cost

What will this cost to build and run?

Alternatives that cost less?

### Integration

How does this fit with existing systems?

Any compatibility issues?

---

## Review Dynamics

### Good Review Culture

**Respectful:** Critique ideas, not people.

**Constructive:** Problems come with suggestions.

**Thorough:** Read carefully, think before commenting.

**Time-bounded:** Don't let reviews drag indefinitely.

### Common Dysfunctions

**Rubber stamp:** Approval without real review.

**Bikeshedding:** Endless discussion on trivial points.

**Blocking on perfection:** Nothing is good enough.

**Missing context:** Reviewers don't understand problem.

**No decisions:** Talking in circles.

### How to Fix

- Clear review criteria
- Decision deadlines
- Escalation path for disagreements
- Training on effective reviews

---

## Lightweight vs. Heavyweight

### Lightweight

Quick review for smaller decisions.

- Brief doc (1-2 pages)
- 2-3 reviewers
- 1-day turnaround
- Short sync discussion

### Heavyweight

Thorough review for major decisions.

- Detailed doc (5-10+ pages)
- Broader reviewer set
- Week for review
- Longer discussion
- Multiple rounds

**Match process to risk.** Not everything needs heavyweight.

---

## After Approval

### Document the Decision

Architecture Decision Record (ADR):

```
# ADR-2024-01: Use PostgreSQL for User Data

## Status
Accepted

## Context
We need to choose a database for user account data.

## Decision
PostgreSQL.

## Rationale
- Team experience
- Strong consistency requirements
- Existing infrastructure

## Alternatives Rejected
- DynamoDB: Team unfamiliar
- MySQL: Less feature-rich

## Consequences
- Must plan for sharding if >10TB
- Need to manage connection pools
```

### Revisit When

The decision isn't permanent. Revisit if:
- Requirements change
- Scale exceeds assumptions
- Technology landscape shifts
- Problems emerge

---

## Common Mistakes

**Skipping review for "urgent" work.** Urgency creates desire to skip. Often most needs review.

**Too detailed too early.** First review should validate approach, not finalize every detail.

**No document.** Verbal-only reviews miss nuance, have no record.

**Wrong reviewers.** Need domain experts, not just available people.

**Ignoring feedback.** Getting review then not incorporating it.

**Analysis paralysis.** Endless review, never building.

---

## What An Experienced Senior Engineer Thinks About

**Review as teaching.** Junior engineers learn from seeing senior thinking.

**Consistency across teams.** Similar problems should have similar solutions.

**Evolution of standards.** What we review for changes as systems and practices evolve.

**Empowerment vs. gatekeeping.** Reviews should enable better decisions, not block work.

---

## Vibe Engineering Guide

When prompting for architecture help:

**Less useful:**
> "Review my design"

**More useful:**
> "Here's my design for a new notification service:
> - Receives events from 10 upstream services
> - 1 million notifications/day
> - Needs to support email, SMS, push
> - Retries on failure
>
> I'm planning Kafka for ingestion, PostgreSQL for notification state, and separate workers for each channel. 
>
> Main concerns: 
> 1. Is Kafka overkill for this volume?
> 2. How should I handle retry logic?
> 3. What would prevent message loss?"

**For reviewing:**
> "I'm reviewing a design that proposes changing from PostgreSQL to Cassandra for our session store. What questions should I ask? What are the risks of this migration?"

---

## Quick Check

<details>
<summary><b>Why write a document before review?</b></summary>

Writing forces clarity - fuzzy thinking becomes obvious. Enables async review - reviewers can read and think before meeting. Creates artifact for future reference.

</details>

<details>
<summary><b>What should alternatives section include?</b></summary>

What other approaches were considered, and why they were rejected. Shows you've thought through options. Prevents reviewers suggesting things you've already evaluated.

</details>

<details>
<summary><b>What's bikeshedding?</b></summary>

Spending disproportionate time on trivial issues while ignoring important ones. Named after arguing about bike shed color while ignoring reactor design. Recognize and redirect.

</details>

<details>
<summary><b>When should you revisit an architecture decision?</b></summary>

When requirements change, scale exceeds assumptions, technology evolves, or problems emerge in practice. Decisions aren't permanent. Document conditions for revisiting.

</details>

---

Next: [Cost Optimization](08-cost-optimization.md)
