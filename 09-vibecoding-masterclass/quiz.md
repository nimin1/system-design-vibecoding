# Level 9 Quiz

Test your understanding of AI-assisted development.

---

## Prompting

<details>
<summary><b>1. What makes a prompt "architecturally aware"?</b></summary>

Specifying technical constraints (language, database), data model, API design, error handling, non-functional requirements (performance, security).

</details>

<details>
<summary><b>2. What's the CARD prompting framework?</b></summary>

Context (system, scale), Architecture (components, patterns), Requirements (functional, non-functional), Details (errors, edge cases, tests).

</details>

<details>
<summary><b>3. Why build complexity gradually?</b></summary>

Easier to verify each step. AI outputs are more reliable for focused tasks. Easier to course-correct.

</details>

---

## Validation

<details>
<summary><b>4. What should you check for in AI-generated API code?</b></summary>

Correct status codes, input validation, error handling, authentication, rate limiting consideration.

</details>

<details>
<summary><b>5. What database issues should you look for?</b></summary>

N+1 queries, missing indexes, no transactions where needed, SQL injection, connection handling.

</details>

<details>
<summary><b>6. How can you use AI to improve its own output?</b></summary>

Ask follow-ups: "What edge cases?", "What could fail?", "Security concerns?", "Write tests for this."

</details>

---

## Production

<details>
<summary><b>7. What's your role vs AI's role?</b></summary>

You: requirements, architecture, validation, testing strategy, security. AI: implementation within your guidance, boilerplate.

</details>

<details>
<summary><b>8. When should you write code yourself?</b></summary>

Security-critical code, performance-critical code, complex business logic, novel algorithms.

</details>

---

## Interviews

<details>
<summary><b>9. What should you do first in a system design interview?</b></summary>

Clarify requirements. Ask about functional requirements, scale, and constraints.

</details>

<details>
<summary><b>10. Why explain your thinking out loud?</b></summary>

Interviewer evaluates thought process, not just final design.

</details>

<details>
<summary><b>11. What are the phases of a system design interview?</b></summary>

Requirements (5-10 min) → Estimation (5 min) → High-level design (10-15 min) → Deep dive (15-20 min) → Wrap-up (5 min).

</details>

---

## Overall

<details>
<summary><b>12. What's the key takeaway?</b></summary>

AI tools need architectural guidance. System design knowledge lets you provide that guidance and validate results. The better you understand systems, the better AI helps you build them.

</details>

---

## Scoring

| Score | Assessment |
|-------|------------|
| 10-12 | You've got this. Go build things. |
| 7-9 | Good understanding. Keep practicing. |
| < 7 | Review the material. |

---

[Review flashcards](flashcards.md) | [🏠 Home](../README.md)
