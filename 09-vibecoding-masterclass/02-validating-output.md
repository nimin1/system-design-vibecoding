# Validating AI Output

Checking that AI-generated code actually works, follows good practices, and won't cause production incidents.

---

## Why Validate

AI coding tools produce plausible-looking code. But plausible isn't the same as correct.

**Common issues:**
- Code runs but has edge case bugs
- Missing or inadequate error handling
- Security vulnerabilities
- Performance problems at scale
- Doesn't follow your existing patterns
- Subtle logic errors
- Outdated or deprecated approaches

Your system design knowledge helps you catch these issues before they reach production.

---

## The Validation Mindset

**Don't trust. Verify.**

AI-generated code should be treated like code from a new team member who doesn't know your codebase. It might be correct, but needs review.

**Questions to ask:**
- Does it do what I asked?
- What could go wrong?
- How does it handle failures?
- Will it scale?
- Does it fit our patterns?

---

## Checklist: API Endpoints

When AI generates an API endpoint, check:

### HTTP Semantics
- [ ] Correct HTTP method? (GET for reads, POST for creates, PUT/PATCH for updates, DELETE for deletes)
- [ ] Proper status codes? (201 for creates, 204 for no content, 404 for not found, not 200 for everything)
- [ ] Idempotent methods are idempotent? (PUT, DELETE safe to retry)

### Input Handling
- [ ] Input validation present? (required fields, types, ranges)
- [ ] Validation errors return 400 with helpful message?
- [ ] Size limits on inputs? (prevent huge payloads)

### Authentication & Authorization
- [ ] Auth check present where needed?
- [ ] Checking authorization, not just authentication? (Can this user access this resource?)
- [ ] Consistent with your auth patterns?

### Error Handling
- [ ] What happens if database fails?
- [ ] What happens if external service is down?
- [ ] Errors logged appropriately?
- [ ] No sensitive info in error responses?

### Response Format
- [ ] Consistent with your other endpoints?
- [ ] Includes relevant data (not more, not less)?
- [ ] Pagination for lists?

---

## Checklist: Database Code

When AI generates database-related code:

### Query Efficiency
- [ ] N+1 queries? (Fetching list, then querying per item - very common AI mistake)
- [ ] Missing indexes on queried columns?
- [ ] Selecting only needed columns? (Not SELECT *)
- [ ] Appropriate use of JOINs vs. multiple queries?

### Data Integrity
- [ ] Transactions for related updates?
- [ ] Constraints match your data model?
- [ ] Handling concurrent modifications?

### Resource Management
- [ ] Connections properly pooled?
- [ ] Connections released after use?
- [ ] Cursors closed?

### Security
- [ ] SQL injection possible? (User input concatenated into queries)
- [ ] Parameterized queries used?

### ORM-Specific
- [ ] Lazy loading causing N+1?
- [ ] Eager loading appropriate relations?
- [ ] ORM generating sensible queries? (Check actual SQL)

---

## Checklist: Async/Distributed Code

When AI generates async or distributed system code:

### Messaging
- [ ] Messages acknowledged after processing, not before?
- [ ] Dead letter queue for failed messages?
- [ ] Idempotent message handlers? (Same message twice = same result)

### External Calls
- [ ] Timeouts on all external calls?
- [ ] Retry logic with exponential backoff?
- [ ] Circuit breaker for failing services?

### State Consistency
- [ ] What happens on partial failure?
- [ ] Rollback or compensation logic?
- [ ] Eventual consistency handled in the application?

### Error Handling
- [ ] Transient vs permanent error distinction?
- [ ] Appropriate retry for transient errors?
- [ ] Alert/escalation for permanent errors?

---

## Checklist: Security

Security issues are easy to miss because the code "works":

### Input/Output
- [ ] Input sanitized? (XSS, injection prevention)
- [ ] Output encoded appropriately?
- [ ] File uploads validated and sandboxed?

### Authentication
- [ ] All protected endpoints actually protected?
- [ ] Token validation complete? (Signature, expiry, issuer)
- [ ] Secrets not hardcoded?

### Data Protection
- [ ] PII handled appropriately?
- [ ] Encryption where needed?
- [ ] Sensitive data not logged?

### Access Control
- [ ] Authorization checks present?
- [ ] No IDOR vulnerabilities? (Insecure Direct Object Reference)
- [ ] Rate limiting present?

---

## Common AI Mistakes

### Overly Optimistic Error Handling

AI often assumes the happy path. It returns values without null checks, doesn't handle "not found" cases, and assumes all operations succeed.

**What to check:** Every data fetch could return null. Every external call could fail. Add explicit handling.

### N+1 Query Problem

Very common in AI-generated code. It fetches a list, then loops through and makes a separate query for each item.

**What to check:** Loops containing database or API calls. Should be a single query with JOINs or batch fetching.

### Missing Edge Cases

AI generates code for the common case:

**What it misses:**
- Empty lists or results
- User doesn't exist
- Database timeout
- Null values in data
- Concurrent modifications

### Ignoring Existing Patterns

If your codebase uses specific patterns, AI might not follow them:
- Different error response format
- Different logging approach  
- Different file structure
- Different naming conventions

**What to check:** Generated code matches your codebase conventions.

### Scalability Blindness

AI doesn't think about scale unless prompted. It loads entire tables into memory, filters in application code, doesn't paginate.

**What to check:** What happens with 1000x more data? Where are the queries filtering?

### Hardcoded Values

AI puts secrets, configuration, and magic numbers directly in code.

**What to check:** Any hardcoded strings that look like keys, passwords, or configuration. Should be environment variables or configuration.

---

## Testing AI Output

Don't just read the code. Run it. Test it.

### Unit Tests

Does the function do what it should?

Ask AI to write tests too: "Write unit tests for this code"

Then review the tests - are they testing the right things?

### Edge Case Testing

- Empty inputs
- Null values
- Very large inputs
- Invalid data types
- Unicode/special characters
- Boundary conditions

### Error Path Testing

- What happens when database is down?
- What happens with invalid auth?
- What happens with concurrent requests?

### Integration Testing

Does it work with your real services?

- Connect to actual database
- Call actual external APIs (in staging)
- Test with realistic data

### Load Testing

For critical paths:
- Does it handle expected load?
- How does it degrade under overload?
- Memory leaks over time?

---

## Using AI to Critique Itself

After getting code, follow up with critique prompts:

```
"What are the edge cases I should handle in this code?"
```

```
"What security vulnerabilities might exist in this code?"
```

```
"What would break if this handled 1000x more traffic?"
```

```
"How should I test this code? What test cases would you write?"
```

```
"What's wrong with this code? Review it critically."
```

AI can find issues in its own output when asked directly.

---

## When to Be Extra Skeptical

Be more careful reviewing AI code for:

- **Security-sensitive code:** Authentication, authorization, payments
- **Performance-critical paths:** High-traffic endpoints, hot loops
- **Complex business logic:** Domain-specific rules, financial calculations
- **Distributed system logic:** Concurrency, consistency, failure handling
- **Production data access:** Migrations, data processing

In these areas: review more carefully, test more thoroughly, or write it yourself.

---

## Red Flags to Watch For

🚩 No error handling at all
🚩 Catches errors but ignores them silently
🚩 Uses deprecated libraries/APIs
🚩 Hardcoded configuration values
🚩 SQL with string concatenation
🚩 Overly complex for the problem
🚩 Missing input validation
🚩 Returns 200 for all responses
🚩 No logging or observability
🚩 Stores secrets in code

---

## Quick Check

<details>
<summary><b>What's a common API mistake AI makes?</b></summary>

Using 200 for all responses, missing error handling, no input validation, not checking authorization (just authentication), N+1 queries when fetching related data.

</details>

<details>
<summary><b>What's a common database mistake AI makes?</b></summary>

N+1 queries (fetching a list then querying per item), missing indexes on queried columns, no transaction handling for related updates, selecting all columns when only a few needed.

</details>

<details>
<summary><b>How can you use AI to improve its own output?</b></summary>

Ask follow-up critique questions: "What edge cases should I handle?", "What security issues might exist?", "What would break at scale?", "Write tests for this code."

</details>

<details>
<summary><b>When should you be most skeptical of AI output?</b></summary>

Security-sensitive code (auth, payments), performance-critical paths, complex business logic, distributed system code, anything touching production data.

</details>

---

Next: [Building Production Systems](03-production-systems.md)
