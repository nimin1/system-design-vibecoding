# Level 8 Quiz

Test your understanding of Senior Engineer thinking.

---

## System Design Principles

<details>
<summary><b>1. What should you do before designing a system?</b></summary>

Understand requirements. Functional requirements (what it does), non-functional requirements (how well it does it), constraints (scale, budget, timeline).

</details>

<details>
<summary><b>2. Why start with the simplest design that works?</b></summary>

Simpler is easier to understand, debug, and operate. Add complexity only when you have evidence you need it.

</details>

---

## Observability

<details>
<summary><b>3. What are the three pillars of observability?</b></summary>

Logs (events), Metrics (numbers over time), Traces (request paths).

</details>

<details>
<summary><b>4. What's the RED method?</b></summary>

Rate (requests/sec), Errors (failures/sec), Duration (latency). Key metrics for services.

</details>

<details>
<summary><b>5. What makes an alert good?</b></summary>

Actionable (you can do something), meaningful (indicates real problems), not noisy (low false positives).

</details>

---

## Performance Optimization

<details>
<summary><b>6. What's the first rule of performance optimization?</b></summary>

Measure first. Don't guess. Profile to find where time is actually spent.

</details>

<details>
<summary><b>7. Why use P99 instead of average latency?</b></summary>

Average hides outliers. P99 shows what the slowest 1% of users experience, which matters for user experience.

</details>

<details>
<summary><b>8. What's the N+1 query problem?</b></summary>

Fetching a list (1 query), then querying per item (N queries). Total: N+1 queries. Fix with batch query or JOIN.

</details>

---

## Capacity Planning

<details>
<summary><b>9. How do you calculate requests per second from daily volume?</b></summary>

RPS = requests per day ÷ 86,400 seconds

</details>

<details>
<summary><b>10. If average load is 1,000 RPS, what should you design for?</b></summary>

Peak load, typically 2-5x average. Design for 2,000-5,000 RPS capacity.

</details>

<details>
<summary><b>11. Why keep headroom (not run at 100% capacity)?</b></summary>

Handle unexpected spikes, allow for instance failures, prevent performance degradation. Never run at 100%.

</details>

---

## Incident Management

<details>
<summary><b>12. What's the first priority during an incident?</b></summary>

Restore service. Mitigate impact. Root cause analysis comes after service is restored.

</details>

<details>
<summary><b>13. Why blameless post-mortems?</b></summary>

Focus on systems, not people. Blaming individuals discourages reporting. Enable organizational learning.

</details>

<details>
<summary><b>14. Should you alert on symptoms or causes?</b></summary>

Symptoms (errors, latency). Causes (high CPU) might not affect users. Alert on what users experience.

</details>

---

## Technical Debt

<details>
<summary><b>15. Why do big rewrites often fail?</b></summary>

Underestimate effort, must maintain old system in parallel, lose embedded knowledge, new system has new bugs, projects get cancelled.

</details>

<details>
<summary><b>16. What's the strangler pattern?</b></summary>

Replace incrementally. Build new component, route some traffic, gradually migrate, retire old. Safer than big bang rewrite.

</details>

---

## Architecture Reviews

<details>
<summary><b>17. Why write down designs before review?</b></summary>

Forces clarity (fuzzy thinking becomes obvious), enables async review (reviewers can think before meeting), creates artifact (future reference).

</details>

<details>
<summary><b>18. What's bikeshedding?</b></summary>

Spending disproportionate time on trivial issues while ignoring important ones. Recognize and redirect to what matters.

</details>

---

## Cost Optimization

<details>
<summary><b>19. What's the difference between reserved and spot instances?</b></summary>

Reserved: commitment for 1-3 years, 30-60% discount, guaranteed capacity. Spot: spare capacity, 60-90% discount, can be terminated anytime.

</details>

<details>
<summary><b>20. Why is egress expensive?</b></summary>

Cloud providers charge ~$0.09/GB for data leaving their network. Keeps data in their cloud. Consider CDN, compression.

</details>

---

## Security Fundamentals

<details>
<summary><b>21. What's defense in depth?</b></summary>

Layering multiple security controls. If one fails, others still protect you.

</details>

<details>
<summary><b>22. Why can't you immediately revoke a JWT?</b></summary>

JWTs are self-contained, verified by signature not database. Valid until expiry. Need blocklist (adds state) or short expiry with refresh tokens.

</details>

<details>
<summary><b>23. How do you prevent SQL injection?</b></summary>

Parameterized queries (prepared statements). User input is treated as data, not SQL code.

</details>

---

## Scoring

| Score | Assessment |
|-------|------------|
| 19-23 | Excellent understanding. Senior-level thinking. |
| 14-18 | Good grasp. Review weak spots. |
| 9-13 | Reread the chapters you missed. |
| < 9 | Go through all chapters again. |

---

[Review flashcards](flashcards.md) | [Continue to Level 9](../09-vibecoding-masterclass/README.md)
