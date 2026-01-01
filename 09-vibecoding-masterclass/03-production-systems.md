# Building Production Systems

Putting AI-assisted development together for systems that actually run in production.

---

## The Reality Check

AI can write code. But production systems are more than code.

**Production means:**
- Runs 24/7 without you watching
- Handles errors gracefully
- Can be debugged when things go wrong
- Scales under load
- Stays secure
- Can be deployed and updated safely

AI helps with the code. You ensure it's production-ready.

---

## The Workflow

### 1. You: Understand What You're Building

Before any prompts:
- What problem are you solving?
- Who uses it and how?
- What are the constraints (scale, budget, timeline)?
- What can go wrong?

### 2. You: Design the Architecture

Before implementation:
- What are the components?
- How do they communicate?
- What data is stored where?
- What are the failure modes?
- What are the trade-offs you're accepting?

### 3. You + AI: Implement Components

With architecture clear:
- Prompt AI for specific components
- Provide context and constraints
- Validate output against your design

### 4. You: Validate and Test

AI output requires verification:
- Does it match requirements?
- Does it handle errors?
- Does it follow your patterns?
- Does it pass tests?

### 5. You + AI: Iterate and Refine

First pass isn't perfect:
- Add missing error handling
- Fix edge cases
- Improve performance
- Clean up and standardize

---

## What You Handle vs. What AI Handles

### You Handle

**Strategic decisions:**
- What to build
- Why this approach
- Trade-offs to accept
- Risk assessment

**Architecture:**
- Component boundaries
- Data model design
- API contracts
- Integration patterns

**Quality:**
- Code review
- Testing strategy
- Security review
- Performance requirements

**Operations:**
- Deployment strategy
- Monitoring strategy
- Incident response
- Documentation priorities

### AI Handles (With Your Guidance)

**Implementation:**
- Boilerplate code
- CRUD operations
- Standard patterns
- Library usage

**Documentation:**
- Code comments
- API documentation
- Test cases
- Runbooks (you review)

**Exploration:**
- Refactoring suggestions
- Alternative approaches
- Error case enumeration
- Edge case identification

---

## Starting a New Project

### Phase 1: Requirements and Design

**Don't touch code yet.** Think through:

```
What are we building?
  [User-facing feature or internal system]
  
Who are the users?
  [Personas, usage patterns]
  
What's the expected scale?
  [Users, requests/sec, data volume]
  
What are the critical requirements?
  [Latency, availability, consistency, security]
  
What can we compromise on?
  [Trade-offs we're willing to accept]
```

### Phase 2: Architecture Sketch

Draw the boxes and arrows:

```
Components:
  [List major components]
  
Data flow:
  [How data moves through system]
  
Storage:
  [What databases, why]
  
External dependencies:
  [Third-party services, APIs]
  
Failure modes:
  [What can break, what happens]
```

### Phase 3: Foundation Setup

Use AI for scaffolding with your architecture:

```
Set up a [framework] project with:

Structure:
- [Your preferred file organization]

Database:
- Schema for [entities]
- Migrations

Authentication:
- [Your auth approach]

Base patterns:
- [Error handling pattern]
- [Logging pattern]
- [Response format]
```

### Phase 4: Feature Implementation

Implement one feature at a time:

1. Design the feature
2. Prompt AI with specific requirements
3. Validate output
4. Test
5. Move to next feature

### Phase 5: Production Hardening

Before deploying:
- Complete error handling
- Add observability
- Security review
- Load testing
- Documentation

---

## Production Checklist

### Reliability

- [ ] Error handling on all external calls (database, APIs, queues)
- [ ] Timeouts configured (no infinite waits)
- [ ] Retry logic with backoff for transient failures
- [ ] Circuit breakers for failing dependencies
- [ ] Graceful degradation for non-critical features
- [ ] Health check endpoint (for load balancer)
- [ ] Readiness check (actually ready to serve)

### Observability

- [ ] Structured logging (JSON, not plain text)
- [ ] Request ID for tracing (propagate through system)
- [ ] Key metrics exposed (latency, errors, throughput)
- [ ] Error logging with context (not just stack trace)
- [ ] Alerting configured (with runbooks)
- [ ] Dashboard for key health indicators

### Security

- [ ] Input validation on all endpoints
- [ ] Authentication on protected endpoints
- [ ] Authorization checks (not just auth)
- [ ] No secrets in code or logs
- [ ] HTTPS only (redirect HTTP)
- [ ] Rate limiting
- [ ] Security headers (CORS, CSP, etc.)
- [ ] Dependencies updated (no known vulnerabilities)

### Performance

- [ ] Database indexes on queried columns
- [ ] No N+1 queries
- [ ] Caching where beneficial
- [ ] Pagination for list endpoints
- [ ] Connection pooling
- [ ] Load tested for expected traffic

### Operations

- [ ] Deployment process automated and tested
- [ ] Database migrations work forward and back
- [ ] Rollback plan exists and tested
- [ ] Configuration externalized (env vars, secrets manager)
- [ ] Documentation for common operations
- [ ] On-call runbook for common issues

### Data

- [ ] Backup strategy defined
- [ ] Data retention policy
- [ ] PII handling compliant
- [ ] Audit logging for sensitive operations

---

## Handling Complexity

### When to Break Down

Some features are too complex for a single prompt:

**Break down:**
- Multi-step workflows
- Complex state machines
- Distributed transactions
- Performance-critical algorithms

**Approach:**
1. Design the overall flow first
2. Implement each component separately
3. Integrate and test
4. Review the integration especially carefully

### When to Write It Yourself

AI is less reliable for:
- Security-critical code (auth, encryption, access control)
- Financial calculations
- Complex business logic with many edge cases
- Performance-critical hot paths
- Novel algorithms

For these: understand the problem deeply, implement carefully, test thoroughly. AI can help explore, but don't blindly copy.

### When AI Excels

AI reliably helps with:
- Boilerplate code
- CRUD operations
- Standard patterns (strategy, factory, etc.)
- Test generation
- Documentation
- Refactoring
- Library usage examples

---

## Maintaining AI-Generated Code

### The Fundamental Rule

**Code AI writes is code you maintain.**

You will debug it, update it, extend it. If you don't understand it, you can't maintain it.

### Practices

**Understand every line.** Walk through the code. Understand what it does and why.

**Make it consistent.** Refactor AI output to match your patterns. Same error handling, same naming, same structure.

**Test it.** Tests are documentation. Tests catch regressions. Tests enable refactoring.

**Document decisions.** Why this approach? What trade-offs? Document for future you.

**Simplify when possible.** AI sometimes over-engineers. If simpler works, use simpler.

---

## The Skill Differential

Two developers using the same AI tools produce very different results:

**Developer A:**
```
"Build me a chat app"
```

**Result:** 
- Generic code that maybe works
- In-memory storage
- No authentication
- No message persistence
- No error handling
- No WebSocket reconnection
- Unusable in production

**Developer B:**
```
"Build a chat system with:
- WebSocket connections for real-time
- JWT authentication
- Message persistence to PostgreSQL
- Redis pub/sub for multi-server
- Reconnection handling with message sync
- Rate limiting on messages
"
```

**Result:**
- Structured code matching production requirements
- Proper patterns
- Real-time with fallbacks
- Scalable architecture
- Production-ready starting point

**The difference is system design knowledge.**

---

## Real-World Example: Building an API

### Step 1: You Design

```
API for task management:

Entities:
- Users (id, email, name, password_hash)
- Projects (id, owner_id, name, created_at)
- Tasks (id, project_id, title, description, status, assignee_id, due_date)

Relationships:
- User has many Projects (as owner)
- Project has many Tasks
- User can be assigned to Tasks (in any project they're member of)

Key behaviors:
- Users can only see their own projects
- Project owner can invite members
- Tasks can be assigned to project members
```

### Step 2: Prompt AI for Schema

```
Create PostgreSQL schema with Prisma:

Tables:
- users: id (uuid), email (unique), name, password_hash, created_at
- projects: id, owner_id (fk), name, created_at
- project_members: project_id, user_id, role (owner/member), joined_at
- tasks: id, project_id (fk), title, description, status (enum), 
         assignee_id (nullable fk), due_date, created_at, updated_at

Include:
- Proper indexes (foreign keys, common query columns)
- Constraints
- Cascade deletes where appropriate
```

### Step 3: Validate and Iterate

Check AI output:
- Are indexes correct?
- Are constraints right?
- Any missing columns?
- Are the relationships correct?

Fix issues, then continue.

### Step 4: Prompt for Endpoints

One endpoint at a time:

```
Create endpoint POST /projects:

Auth: JWT required
Input: { name: string }
Behavior:
1. Validate input (name required, max 100 chars)
2. Create project with owner_id = current user
3. Add owner to project_members with role 'owner'
4. Return created project

Response 201: { id, name, created_at }
Response 400: { error: "validation failed", details: [...] }
Response 401: { error: "unauthorized" }

Follow existing error handling pattern from codebase.
```

### Step 5: Review and Test

- Check error handling
- Check authorization
- Run tests
- Check it integrates with existing code

---

## Quick Check

<details>
<summary><b>What's your role vs AI's role?</b></summary>

You: requirements, architecture, validation, testing strategy, security. AI: implementation within your architecture, boilerplate, documentation, exploration.

</details>

<details>
<summary><b>When should you write code yourself instead of using AI?</b></summary>

Security-critical code, performance-critical code, complex business logic, financial calculations, novel algorithms - areas where mistakes are costly and patterns aren't well-established.

</details>

<details>
<summary><b>What makes the difference between good and bad AI-assisted development?</b></summary>

System design knowledge. Understanding architecture lets you guide AI with specific, contextual prompts and validate output against proper patterns.

</details>

<details>
<summary><b>Why understand AI-generated code?</b></summary>

Because you'll maintain it. Debug it. Extend it. If you don't understand it, you can't maintain it. Treat it like code from a new team member.

</details>

---

## Summary

AI is a powerful implementation tool. But implementation is only part of building systems.

**Your skills:**
- Understanding requirements
- Designing architecture
- Evaluating trade-offs
- Ensuring security
- Planning for scale
- Maintaining reliability

These skills let you guide AI effectively and build systems that actually work in production.

AI amplifies your capabilities. The more you know, the more it amplifies.

---

Next: [Interview Framework](04-interview-framework.md)
