# Level 8 Flashcards

Quick reference for Senior Engineer thinking.

---

## System Design Principles

| Front | Back |
|-------|------|
| First principle of design? | Understand requirements before designing. |
| Why start simple? | Easier to understand, debug, operate. Add complexity only when needed. |
| What to optimize for first? | Correctness, then reliability, then performance. |

---

## Observability

| Front | Back |
|-------|------|
| Three pillars? | Logs (events), Metrics (numbers), Traces (paths through system) |
| RED method? | Rate, Errors, Duration  -  key service-level metrics |
| USE method? | Utilization, Saturation, Errors  -  resource-level metrics |
| Good alert properties? | Actionable, meaningful, not noisy |

---

## Performance Optimization

| Front | Back |
|-------|------|
| First rule of optimization? | Measure first. Don't guess where the problem is. |
| Why P99, not average? | Average hides outliers. P99 shows what 1% of users experience. |
| N+1 query problem? | Fetching list, then query per item. Use batch/JOIN instead. |
| When to NOT optimize? | Before measuring, cold paths, when complexity cost exceeds benefit. |

---

## Capacity Planning

| Front | Back |
|-------|------|
| RPS formula? | requests per day ÷ 86,400 |
| Peak vs average? | Design for peak, typically 2-5x average |
| Why load test? | Verify capacity assumptions. Find bottlenecks before production does. |
| Headroom purpose? | Handle unexpected spikes, allow for instance failures. |

---

## Incident Management

| Front | Back |
|-------|------|
| First priority in incident? | Restore service. Mitigate impact. Root cause comes after. |
| Why blameless post-mortems? | Focus on systems, not people. Enable learning. |
| Runbook purpose? | Enable anyone on-call to respond. Document steps and context. |
| Alert on symptoms or causes? | Symptoms. High CPU might not affect users. Errors do. |

---

## Technical Debt

| Front | Back |
|-------|------|
| Deliberate vs inadvertent debt? | Deliberate: knowingly take shortcut. Inadvertent: learned better after. |
| Why rewrites often fail? | Underestimate effort, must maintain old system, lose embedded knowledge. |
| Better than rewrite? | Strangler pattern: replace incrementally. |
| How much time for debt? | 10-20% of sprint capacity continuously. |

---

## Architecture Reviews

| Front | Back |
|-------|------|
| Why write it down? | Forces clarity, enables async review, creates record. |
| What's bikeshedding? | Spending time on trivial issues, ignoring important ones. |
| ADR? | Architecture Decision Record  -  documents what, why, alternatives, consequences |

---

## Cost Optimization

| Front | Back |
|-------|------|
| Reserved vs spot instances? | Reserved: commitment for discount. Spot: spare capacity, can be terminated. |
| Why egress expensive? | Keeps data in cloud. ~$0.09/GB leaving. |
| Right-sizing? | Match instance size to actual usage. Don't over-provision. |
| Storage lifecycle policies? | Auto-move old data to cheaper storage tiers. |

---

## Security Fundamentals

| Front | Back |
|-------|------|
| Defense in depth? | Multiple security layers. If one fails, others protect. |
| Authentication vs authorization? | Authentication: who are you. Authorization: what can you do. |
| SQL injection prevention? | Parameterized queries. User input treated as data, not code. |
| JWT revocation problem? | Can't revoke until expiry. Need blocklist or short expiry with refresh. |

---

[Continue to Level 9](../09-vibecoding-masterclass/README.md)
