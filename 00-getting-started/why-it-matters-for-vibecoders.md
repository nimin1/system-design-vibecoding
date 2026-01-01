# Why This Helps with AI Coding Tools

AI code generators have a specific limitation: they can write code, but they don't know your context. They don't know your expected scale, your infrastructure, or which trade-offs matter to you.

System design knowledge helps you provide that context.

---

## The Practical Problem

When you prompt an AI with something like "Build me a chat app," you get working code. It runs. It passes basic tests.

But it might:
- Store all messages in memory (gone on restart)
- Use a single database without considering read/write patterns
- Have no concept of users being offline
- Fall over if two people send messages at the same time

The AI built what you asked for. The problem is you didn't ask for the right things.

---

## What Changes With Knowledge

Here's the same request, but from someone who knows system design:

**Vague:**
> "Build me a chat app"

**Specific:**
> "Build a chat system with:
> - WebSocket connections for real-time messaging
> - PostgreSQL for message persistence
> - Redis pub/sub for distributing messages across server instances
> - Handling for when users go offline and come back
> - Consider that messages need to be ordered per conversation"

The second prompt tells the AI *what to actually build*, not just what the end result looks like.

---

## Things to Check in AI Output

When you know system design concepts, you can spot gaps in generated code:

| What to check | What it means |
|---------------|---------------|
| **Single points of failure** | Is there one component whose failure takes down everything? |
| **Data persistence** | Is data stored durably, or just in memory? |
| **Concurrency** | What happens if two requests hit the same data? |
| **Failure handling** | What happens when an external service is slow or down? |
| **Scale limits** | Where will this break if traffic increases? |

These aren't obscure concerns. They're the things that break when code leaves your laptop and meets real users.

---

## Common AI Gaps

From my experience, AI generated architectures tend to miss:

**Error handling at service boundaries.** The happy path works. The sad path is often missing.

**Caching.** AI implements features that query the database on every request. That's often fine for prototypes, but not for anything with traffic.

**Rate limiting.** Public APIs without rate limiting can be easily overwhelmed.

**Async processing.** Some things shouldn't block a request (sending emails, processing uploads). AI often makes these synchronous.

**Consistency concerns.** AI might use patterns that allow stale reads where you actually need up-to-date data.

Knowing these patterns helps you ask the right follow-up questions.

---

## A Mental Model

Think of AI as a competent developer who joined yesterday and doesn't know your codebase or your constraints.

If you told that developer "add a user profile feature," they'd ask clarifying questions: Where is user data stored? What should happen if the database is down? Is there caching? What about permissions?

AI doesn't ask those questions. You have to answer them preemptively in your prompts.

System design knowledge is what lets you know which questions matter.

---

## Quick Check

<details>
<summary><b>Why can't AI just figure out the right architecture?</b></summary>

AI doesn't have access to your specific context, your expected traffic, your infrastructure, your consistency requirements, your team's familiarity with certain technologies. Architecture decisions depend on these constraints. The AI only knows what you tell it.

</details>

<details>
<summary><b>What's the difference between a vague and specific prompt?</b></summary>

A vague prompt describes what the result should look like ("a chat app"). A specific prompt describes the architectural properties it should have ("persistent messages, real-time updates via WebSockets, handling for offline users").

</details>

<details>
<summary><b>What are common things AI-generated code misses?</b></summary>

Error handling at service boundaries, caching on read-heavy paths, rate limiting on public endpoints, async processing for slow operations, and proper handling of concurrent access to shared data.

</details>

---

Next: [How to Use This Repo](how-to-use-this-repo.md)
