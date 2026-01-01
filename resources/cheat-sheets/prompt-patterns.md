# Prompt Patterns for System Design

Quick reference for getting useful responses from AI coding tools.

---

## Architecture Prompts

**Basic:**
```
I'm designing [system type]:
- [Functional requirement 1]
- [Functional requirement 2]

Constraints:
- Scale: [users, requests, data size]
- Latency: [requirements]

What are the main components and how do they connect?
```

**With trade-offs:**
```
I'm choosing between [Option A] and [Option B] for [use case]:
- Current situation: [context]
- Team familiarity: [what we know]
- Scale: [expected load]

Compare complexity, performance, and operational overhead.
```

---

## Implementation Prompts

**API endpoint:**
```
Create endpoint [METHOD] [path]:

Auth: [requirements]
Input: [fields and validation]
Behavior: [step by step what it does]
Error cases: [what can go wrong and how to handle]

Response format: [expected output]
```

**Database query:**
```
I need to query [table] with [X] million rows:
- Filter by: [columns]
- Sort by: [column]
- Return: [fields]

Current indexes: [list]
What index should I add? Show me the query and explain the execution plan.
```

---

## Debugging Prompts

**Performance issue:**
```
This query/endpoint is slow:
[code or query]

Expected: [X]ms
Actual: [Y]ms

What I've measured:
- Database time: [X]
- External calls: [X]
- Processing: [X]

What should I look at?
```

**Consistency issue:**
```
Users sometimes see stale data:
- Architecture: [primary + replicas, caching layer, etc.]
- When it happens: [after writes, specific operations]
- What we see: [old data, missing data]

Where might consistency be breaking?
```

---

## Scaling Prompts

**Capacity planning:**
```
Current: [X] requests/day, [Y] data size
Growing: [rate]
Limit: [current bottleneck]

How long until we need to scale? What are the options?
```

**Bottleneck analysis:**
```
System slows down at [X] load:
- CPU: [usage]
- Memory: [usage]
- Database connections: [usage]
- Response time: [how it degrades]

What's the likely bottleneck?
```

---

## Review Prompts

**Code review:**
```
Review this code for:
[code]

Check:
- Error handling
- Security issues
- Performance at scale
- Edge cases

What's wrong or missing?
```

**Architecture review:**
```
Here's my design for [system]:
[description or diagram]

Constraints: [requirements]
Expected scale: [numbers]

What failure modes am I missing? What would break at 10x scale?
```

---

## Anti-patterns

**Too vague:**
- "Make it faster"
- "Add caching"
- "Handle errors"

**Missing context:**
- "Build a microservice" (for what?)
- "Design a database" (what data? what queries?)

**Assuming AI knows your system:**
- "Use our existing patterns" (AI doesn't know them)
- "Connect to our services" (AI doesn't know what they are)

---

## General Pattern

```
1. What I'm building: [specific description]
2. Current state: [what exists, what I've tried]
3. Constraints: [scale, latency, consistency, team]
4. Specific question: [what I need to know]
```

The more context, the better the response.

---

Return to [Resources](../README.md)
