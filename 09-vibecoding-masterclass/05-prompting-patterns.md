# Prompting Patterns for System Design

Specific patterns for getting better results from AI coding tools.

---

## The Problem

AI tools are good at writing code. But without context, they produce generic solutions.

**Vague prompt:**
> "Build me an API"

**Result:** A basic CRUD API with minimal error handling, no authentication, default everything.

---

## The Solution: Context Stacking

Layer context to guide the AI toward production-quality output:

1. **What**  -  The feature
2. **How**  -  Technical approach
3. **Constraints**  -  Scale, performance, security
4. **Patterns**  -  Existing code patterns to follow
5. **Validation**  -  How to verify correctness

---

## Pattern 1: The Architecture Prompt

For designing system structure:

```
Design [system name]:

Requirements:
- [Functional requirement 1]
- [Functional requirement 2]

Constraints:
- Scale: [users, requests, data size]
- Latency: [acceptable response time]
- Consistency: [strong/eventual]

Tech stack:
- [Database]
- [Cache]
- [Message queue]

Output:
- High-level architecture diagram (ASCII)
- Data model
- API endpoints
- Key trade-offs
```

**Example:**
```
Design an order management system:

Requirements:
- Create orders with multiple items
- Process payments
- Track order status

Constraints:
- 1000 orders/minute peak
- 99.9% availability
- Strong consistency for payments

Tech stack:
- PostgreSQL for orders
- Redis for caching
- Kafka for events

Output the architecture diagram, database schema, and API design.
```

---

## Pattern 2: The Implementation Prompt

For writing specific code:

```
Implement [component]:

Context:
- This is part of [larger system]
- It will be called by [what]
- It depends on [dependencies]

Requirements:
- [Specific behavior 1]
- [Specific behavior 2]

Error handling:
- [Error case 1] → [How to handle]
- [Error case 2] → [How to handle]

Existing patterns to follow:
- [Pattern or example from codebase]

Testing:
- [What should be tested]
```

**Example:**
```
Implement the payment processing function:

Context:
- Called by the order service after order validation
- Uses Stripe SDK for payment processing
- Results are stored in PostgreSQL

Requirements:
- Accept order_id, amount, payment_method_token
- Create idempotent Stripe PaymentIntent
- Store result in database within transaction

Error handling:
- Stripe timeout → Return pending, queue for reconciliation
- Card declined → Return failure with reason
- Database error → Rollback, return error

Follow existing pattern from user_service.py for error handling.

Include unit tests for success and each error case.
```

---

## Pattern 3: The Debugging Prompt

For understanding and fixing issues:

```
I have this issue:

Code:
[Relevant code snippet]

Expected behavior:
[What should happen]

Actual behavior:
[What's happening]

Environment:
- [Relevant details]

What I've tried:
- [Attempt 1]
- [Attempt 2]

Help me:
1. Understand why this is happening
2. Find the root cause
3. Fix it properly (not just workaround)
```

---

## Pattern 4: The Review Prompt

For validating AI-generated code:

```
Review this code for:

[Code snippet]

Check for:
- [ ] Correct error handling
- [ ] Security issues (injection, auth)
- [ ] Performance problems (N+1 queries, missing indexes)
- [ ] Edge cases not handled
- [ ] Missing validation

Suggest specific improvements.
```

---

## Pattern 5: The Trade-off Prompt

For making design decisions:

```
I need to choose between:

Option A: [Description]
Option B: [Description]

Context:
- [Current situation]
- [Scale requirements]
- [Team capabilities]

Compare:
- Performance
- Complexity
- Operational overhead
- Cost
- Migration path

Recommend one and explain why for my context.
```

---

## Anti-Patterns

### Too Vague

❌ "Make it better"
❌ "Add caching"
❌ "Handle errors"

### Too Prescriptive

❌ "Write exactly this code: [copy of code]"

### No Context

❌ "Build a microservice" (for what? what scale?)

### Assuming AI Knows Your Codebase

❌ "Use the existing patterns" (AI doesn't know them unless you provide them)

---

## Building Complexity Gradually

Start simple, layer on requirements:

**Round 1:** Basic functionality
```
Create a REST endpoint for user registration:
- POST /users
- Accept email and password
- Return user ID
```

**Round 2:** Add validation
```
Now add validation:
- Email must be valid format
- Password: 8+ chars, 1 number, 1 uppercase
- Return specific error messages
```

**Round 3:** Add error handling
```
Add proper error handling:
- Duplicate email → 409 Conflict
- Database errors → 500 with reference ID
- Log errors with context
```

**Round 4:** Add security
```
Add security:
- Hash password with bcrypt
- Rate limit: 10 attempts per minute per IP
- Add CSRF protection
```

---

## Template: Full Feature Request

```
## Feature: [Name]

### Context
[Brief description of where this fits in the system]

### Requirements
Functional:
- [What it should do]

Non-functional:
- Scale: [expected load]
- Latency: [requirements]
- Security: [considerations]

### Technical Approach
- Database: [table changes]
- API: [endpoints]
- Integration: [other services]

### Implementation Details
[Specific behaviors, edge cases, error handling]

### Testing
- Unit tests: [what to test]
- Integration tests: [what to verify]

### Success Criteria
[How we know it's working]
```

---

## For Interviews

Use AI to practice:

```
I'm preparing for a system design interview.

Scenario: Design a [system name]

Play the role of interviewer:
1. Ask me clarifying questions about requirements
2. Challenge my design choices
3. Ask about specific components in depth
4. Point out potential issues
5. Ask about trade-offs

Start with: "Design a distributed cache system. You have 45 minutes. How would you begin?"
```

---

## Quick Check

<details>
<summary><b>What's wrong with "Build me an API"?</b></summary>

Too vague. No context about what kind of API, scale, error handling, authentication, patterns to follow. Result will be generic and require significant rework.

</details>

<details>
<summary><b>Why build complexity gradually?</b></summary>

Easier to verify each step is correct. Easier to course-correct early. AI outputs are more reliable for focused tasks.

</details>

---

Return to [AI-Assisted Development README](README.md)
