# Prompting for Architecture

Using system design knowledge to write better AI prompts, turning vague requests into precise specifications.

---

## The Core Idea

AI coding tools are excellent at writing code. They're less good at making architectural decisions.

**Your job:** Provide the architectural context, constraints, and requirements.

**AI's job:** Implement within that context.

The better your prompts specify architecture, the better the output. This is where your system design knowledge becomes a superpower.

---

## Why This Matters

Without architectural context:
- AI guesses your technology stack
- Defaults to in-memory storage
- Ignores scaling considerations
- Misses security requirements
- Produces generic, unmaintainable code

With architectural context:
- Code fits your existing patterns
- Appropriate technology choices
- Handles expected scale
- Includes necessary safeguards
- Production-ready from the start

---

## Anatomy of a Good Prompt

### 1. What You're Building

The feature, system, or component. Be specific.

```
Bad: "Build a user system"
Good: "Build user registration and authentication with email verification"
```

### 2. Technical Constraints

Technology stack, existing patterns, limitations.

```
- Language: TypeScript
- Framework: Express.js
- Database: PostgreSQL with Prisma
- Auth: JWT stored in httpOnly cookies
- Must work with existing User model
```

### 3. Functional Requirements

The behavior you need. Specific inputs and outputs.

```
- POST /register: email, password, name → creates user, sends verification email
- POST /verify-email: token → marks email verified
- POST /login: email, password → returns JWT if verified
- GET /me: returns current user (requires auth)
```

### 4. Non-Functional Requirements

Performance, security, consistency, reliability.

```
- Password hashed with bcrypt (cost 12)
- Rate limit: 5 login attempts per minute per IP
- Verification tokens expire in 24 hours
- All inputs validated with Zod
- Return appropriate HTTP status codes
```

---

## Example: Before and After

### Without System Design Knowledge

```
Build me a todo app backend
```

**Result:** Generic code, in-memory storage, no authentication, no error handling, random file structure, probably won't run.

### With System Design Knowledge

```
Build a todo app API with:

Data model:
- Users have many lists
- Lists have many items
- Items: id, title, description, completed (boolean), due_date, created_at

Technical requirements:
- Node.js with Express
- PostgreSQL with Prisma ORM
- JWT authentication (tokens in Authorization header)
- RESTful API design with consistent error format

Endpoints:
- GET /lists: returns user's lists (auth required)
- POST /lists: creates a list, returns created list
- GET /lists/:id/items: returns items for a list (must be owner)
- POST /lists/:id/items: creates item
- PATCH /items/:id: updates item (completed, title, etc.)
- DELETE /items/:id: soft delete

Non-functional:
- Validate all inputs (required fields, types)
- Return proper HTTP status codes (201 for creates, 404 for not found)
- Handle database errors gracefully (500 with generic message)
- Pagination for list endpoints (limit/offset query params)
```

**Result:** Structured, production-ready code matching your architecture.

---

## What to Include for Different Systems

### For APIs

- Endpoints (method, path, purpose)
- Request format (body, query params, headers)
- Response format (JSON structure)
- Authentication approach
- Authorization rules (who can access what)
- Validation requirements
- Error responses (format, status codes)
- Rate limiting

### For Database Work

- Data model (entities, fields, types)
- Relationships (one-to-many, many-to-many)
- Constraints (unique, not null, foreign keys)
- Expected query patterns (how data is accessed)
- Index requirements
- Transaction needs
- Soft delete vs hard delete

### For Distributed Systems

- Components and their responsibilities
- Communication (sync HTTP, async events, queues)
- Message formats
- Consistency requirements (strong, eventual)
- Failure handling (retries, fallbacks, circuit breakers)
- Idempotency requirements
- Ordering guarantees needed

### For Performance-Critical Work

- Expected load (requests/sec, data volume)
- Latency requirements (P50, P99)
- Resource constraints (memory, CPU)
- Caching strategy (what to cache, invalidation)
- What to optimize for (latency vs throughput)
- Acceptable trade-offs

---

## Prompt Templates

### API Endpoint

```
Create an API endpoint:

Purpose: [what it does]
HTTP: [METHOD] [/path]

Request:
- Auth: [none / JWT required / API key]
- Body: [fields with types]
- Query params: [optional filters]

Behavior:
1. [Step by step what it does]
2. [Include validation]
3. [Include error cases]

Response (success):
- Status: [201/200/etc]
- Body: [JSON structure]

Response (errors):
- [Condition]: [Status] [Message]

Additional requirements:
- [Rate limiting, caching, etc.]
```

### Database Schema

```
Design a database schema for [domain]:

Entities:
1. [Entity name]: [description]
   - [field]: [type] [constraints]
   
2. [Entity name]: [description]
   - [fields...]

Relationships:
- [Entity A] has many [Entity B]
- [Entity C] belongs to [Entity D]

Query patterns (optimize for these):
1. Get [X] by [field]
2. List all [Y] for a [Z]
3. Find [X] where [condition]

Suggest:
- Indexes
- Constraints
- Timestamps (created_at, updated_at)
```

### System Component

```
Implement [component name]:

Purpose: [what it does in the system]

Inputs:
- [How it receives work: API call, queue message, cron]
- [Format of input]

Processing:
1. [What it does with input]
2. [External calls it makes]
3. [Where it stores results]

Outputs:
- [What it produces]
- [Where/how it sends output]

Error handling:
- [Transient errors]: [retry with backoff]
- [Permanent errors]: [dead letter queue / alert]
- [External service down]: [circuit breaker / fallback]

Integration:
- Uses: [existing services/databases]
- Publishes events: [list events]
```

---

## Iterative Prompting

Don't expect perfection in one prompt. Iterate.

### First Prompt

Get the basic structure right.

```
Build [feature] with [core requirements]
```

### Follow-Up Prompts

Refine and extend.

```
"Add input validation using Zod"
"Add error handling for database connection failures"
"Add rate limiting: 100 requests/minute per user"
"Add caching with 5-minute TTL"
"Write unit tests for the service layer"
```

### Exploration Prompts

Use AI to think through problems.

```
"What edge cases should I consider?"
"What would break if we had 1000x more users?"
"What security vulnerabilities might exist?"
"How would you implement this differently for read-heavy load?"
```

---

## Common Mistakes

**Too vague.** "Build me an API" forces AI to guess everything.

**Assuming AI knows your context.** It doesn't know your existing code, database schema, or coding conventions.

**Not specifying non-functional requirements.** You get functionally correct but performance-unaware code.

**Accepting first output.** Always review and iterate. First attempt is rarely right.

**Copy-paste without reading.** AI makes subtle mistakes. Read generated code.

**Not providing examples.** For complex data transformations, example input → output clarifies.

---

## Vibe Engineering Guide

When using AI for system design:

**For design exploration:**
```
"I'm designing a system for [use case] with these requirements:
- [List requirements]
- [Scale expectations]

Questions:
1. What components would you suggest?
2. What are the main trade-offs I should consider?
3. What would be the simplest architecture that meets requirements?"
```

**For implementation:**
```
"I'm implementing [component] in my existing system:

Existing context:
- [Tech stack]
- [Related existing code/patterns]
- [Database schema relevant pieces]

Requirements for this component:
- [Specific requirements]

Generate the [specific thing] following our existing patterns."
```

---

## Quick Check

<details>
<summary><b>What makes a prompt architecturally aware?</b></summary>

Specifying technical constraints (language, database, frameworks), data model, API design, error handling, and non-functional requirements (performance, security, consistency).

</details>

<details>
<summary><b>Why iterate on prompts?</b></summary>

First output is rarely perfect. Follow-up prompts refine: add validation, error handling, edge cases, performance considerations. Build up complexity incrementally.

</details>

<details>
<summary><b>What's the most important non-functional requirement people forget?</b></summary>

Often error handling and failure scenarios. People specify the happy path but not what happens when things go wrong.

</details>

<details>
<summary><b>When should you provide examples in prompts?</b></summary>

For complex data transformations, specific output formats, or when your requirements are hard to describe in words. Example input → output clarifies intent.

</details>

---

Next: [Validating AI Output](02-validating-output.md)
