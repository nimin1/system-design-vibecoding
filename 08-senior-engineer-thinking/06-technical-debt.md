# Technical Debt

Managing the hidden costs of shortcuts when to pay down and when to live with it.

---

## What is Technical Debt?

Technical debt is the implied cost of future rework caused by choosing an expedient solution now instead of a better approach that takes longer.

Like financial debt:
- You get something now (faster delivery)
- You pay interest later (slower future development)
- Eventually, you may need to pay it down (refactor)

---

## Types of Technical Debt

### Deliberate and Prudent

Knowingly take shortcut with plan to address.

"We know this won't scale past 1000 users, but we need to launch. We'll rewrite before we hit that limit."

**Acceptable:** Conscious trade-off with mitigation plan.

### Deliberate and Reckless

Knowingly ignore good practices without good reason.

"We don't have time for tests."

**Problem:** Accumulates quickly, causes outages.

### Inadvertent and Prudent

Learn better way after building.

"Now that we've built it, we understand the domain better. We'd design it differently."

**Natural:** Learning through building. Plan incremental improvement.

### Inadvertent and Reckless

Didn't know better, made poor decisions.

"We didn't realize we needed indexes."

**Education issue:** Improve team skills, review processes.

---

## Common Forms of Technical Debt

### Code Debt

- Duplicated code
- Complex, hard-to-understand logic
- Missing tests
- Inconsistent patterns
- Poor naming

### Architecture Debt

- Tight coupling
- Missing abstraction layers
- Wrong technology choices
- Monolith that should be services (or vice versa)
- Scalability limitations

### Infrastructure Debt

- Manual deployments
- Missing monitoring
- Outdated dependencies
- Security vulnerabilities
- Inconsistent environments

### Documentation Debt

- Missing or outdated docs
- Tribal knowledge
- Unclear APIs
- Missing runbooks

---

## Costs of Technical Debt

### Velocity Slowdown

```
Early: Features shipped in days
After debt accumulates: Same features take weeks
```

New features require understanding old code, working around limitations.

### Bug Rate Increase

Complex, poorly tested code has more bugs.

Each bug requires investigation, fix, testing, deployment.

### Onboarding Time

New team members struggle to understand the system.

"Why is it done this way?" "I don't know, it's always been that way."

### Reliability Issues

Debt often manifests as production incidents.

Missing tests, poor error handling, lack of monitoring.

### Team Morale

Working in messy code is frustrating.

Good engineers leave for better codebases.

---

## Measuring Technical Debt

### Qualitative

- Team surveys: "How painful is this area?"
- Sprint planning: How much time spent on debt-related work?
- New feature estimates: Are estimates growing?

### Quantitative

- Bug rate by area
- Deployment frequency
- Time to onboard new developers
- Test coverage
- Dependency age/security issues

### Warning Signs

- "Don't touch that code" areas
- Features that always break something else
- Estimates are always wrong (too optimistic)
- New hires take longer to become productive
- Same bugs keep returning

---

## Managing Technical Debt

### Track It

Keep a debt register. Document:
- What the debt is
- Why it exists
- Impact if not addressed
- Estimated effort to fix
- Priority

### Prioritize

Not all debt is equal.

**High priority:**
- Actively blocking features
- Causing production issues
- In frequently changed code
- Security vulnerabilities

**Lower priority:**
- Stable, rarely touched code
- Minor inconveniences
- Cosmetic issues

### Budget for It

Allocate regular time:

**Continuous:** 10-20% of sprint capacity for debt work.

**Dedicated sprints:** Periodic "tech debt sprint."

**Opportunistic:** Clean up when working in an area.

### Pay It Down Strategically

**Prioritize high-traffic areas.** If you're going to change it anyway, clean it up.

**Address before it blocks.** Don't wait until debt stops all progress.

**Combine with features.** Refactor in support of a feature, not just for its own sake.

---

## Preventing Technical Debt

### Code Reviews

Catch shortcuts before they merge.

### Definition of Done

Include tests, documentation, cleanup.

### Architectural Reviews

For significant changes, review before building.

### Regular Refactoring

Small improvements continuously, not big rewrites rarely.

### Realistic Timelines

Rushed work creates debt. Advocate for sustainable pace.

---

## The Big Rewrite Trap

"Let's just rewrite it from scratch."

### Why Rewrites Fail

- Underestimate effort (always)
- Must maintain old system while building new
- Lose embedded knowledge and edge cases
- New system has its own bugs
- Projects get cancelled before completion

### Better Approach

**Strangler pattern:** Replace incrementally.

1. Build new component
2. Route some traffic to new
3. Gradually migrate
4. Eventually retire old

### When Rewrite Makes Sense

- Fundamental technology change (language, platform)
- Original system truly unsalvageable
- Team can commit to completion
- Can run both in parallel

---

## Common Mistakes

**Ignoring debt until crisis.** Debt compounds. Address early.

**Only paying debt, no features.** Business needs features. Balance.

**Big bang rewrites.** High risk. Incremental is safer.

**Debt without tracking.** "Clean code" becomes vague priority, always deprioritized.

**Blaming past (or other) developers.** Understand context before judging.

---

## What An Experienced Senior Engineer Thinks About

**Debt interest rate.** Some debt costs little (rarely touched). Some costs daily (constantly worked around).

**Strategic debt.** Sometimes debt is the right choice. Speed to market matters. Know when.

**Sustainability.** Current velocity must be sustainable. If debt causes slowdown, future is mortgaged.

**Communication.** Stakeholders need to understand debt. Frame in business terms: "This slows us by X."

---

## Vibe Engineering Guide

When prompting about technical debt:

**Less useful:**
> "Reduce our technical debt"

**More useful:**
> "Our main API service has significant debt:
> - No tests (0% coverage)
> - Complex business logic mixed with HTTP handling
> - 5-year-old dependencies with security vulnerabilities
> - Team estimates take 2x longer than other services
>
> We need to continue delivering features while improving. What's a pragmatic approach? How should we prioritize? What quick wins could reduce pain fastest?"

**For specific problems:**
> "We have a 10-year-old monolith. Everyone wants to rewrite. I'm skeptical. It handles $10M/year in revenue. How do I evaluate whether a rewrite makes sense vs. incremental improvement?"

---

## Quick Check

<details>
<summary><b>What's the difference between deliberate and inadvertent debt?</b></summary>

Deliberate: Knowingly take shortcut. "We know this won't scale but need to ship." Inadvertent: Didn't know better or learned after. "We'd design it differently now that we understand the domain."

</details>

<details>
<summary><b>Why do big rewrites often fail?</b></summary>

Underestimate effort, must maintain old system in parallel, lose embedded knowledge, new bugs in new system, projects cancelled before completion. Strangler pattern is safer.

</details>

<details>
<summary><b>How should you prioritize debt?</b></summary>

High priority: actively blocking features, causing production issues, in frequently changed code, security risks. Lower priority: stable rarely-touched code, cosmetic issues.

</details>

<details>
<summary><b>How much time to allocate for debt?</b></summary>

Common approaches: 10-20% of sprint capacity continuously, periodic dedicated sprints, or opportunistic cleanup when working in an area. Key: consistent allocation, not just "when we have time."

</details>

---

Next: [Architecture Reviews](07-architecture-reviews.md)
