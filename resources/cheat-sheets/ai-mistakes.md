# Common AI Mistakes

What AI coding tools often get wrong. Know these so you can catch them.

---

## Error Handling

**What AI does:** Returns values without null checks. Assumes data exists.

**What's wrong:** If data doesn't exist, it crashes or returns wrong results.

**What you need:** Check for null/undefined. Handle "not found" cases explicitly.

---

## N+1 Queries

**What AI does:** Loops that make database calls inside - fetching a list, then querying for each item separately.

**What's wrong:** 100 items = 101 queries. Slow.

**What you need:** Single query with JOIN, or batch fetch all needed data at once.

This is one of the most common AI mistakes. Always check loops that contain database or API calls.

---

## Missing Timeouts

**What AI does:** Makes external API calls without timeout configuration.

**What's wrong:** If external service hangs, your request hangs forever.

**What you need:** Every external call needs a timeout. Default to 5-30 seconds depending on the operation.

---

## Hardcoded Secrets

**What AI does:** Puts API keys, passwords, or tokens directly in code.

**What's wrong:** Secrets in code end up in git history forever.

**What you need:** Environment variables or secrets manager. Never commit secrets.

---

## Status Codes

**What AI does:** Returns 200 OK for everything, including errors.

**What's wrong:** Clients can't tell success from failure programmatically.

**What you need:**
- 201 for created
- 400 for bad input
- 404 for not found
- 500 for server errors

---

## SQL Injection

**What AI does:** Concatenates user input into SQL query strings.

**What's wrong:** Attackers can inject malicious SQL.

**What you need:** Parameterized queries. User input should never be part of the SQL string directly.

---

## Scalability Blindness

**What AI does:** Loads all data into memory, filters in application code.

**What's wrong:** Works with 100 records, crashes with 1 million.

**What you need:** Filter, sort, and paginate in the database query, not in code.

AI doesn't think about scale unless you tell it to.

---

## Missing Retries

**What AI does:** Makes a single attempt to call external services.

**What's wrong:** Transient failures (network blip, temporary overload) cause permanent errors.

**What you need:** Retry with exponential backoff for transient failures. Distinguish retryable from permanent errors.

---

## Ignoring Concurrency

**What AI does:** Read-modify-write without transactions or atomic operations.

**What's wrong:** Two requests at the same time both read the same value, both write their update, one overwrites the other.

**What you need:** Atomic operations, transactions, or optimistic locking.

---

## Default Everything

**What AI does:** Uses default database settings, default pool sizes, default TTLs.

**What's wrong:** Defaults are for demos, not production.

**What you need:** Configuration appropriate for your scale. Tune pool sizes, timeouts, cache TTLs.

---

## How to Use This

When reviewing AI output, check for:

1. **Null/error returns** - Are they handled?
2. **Loops with I/O** - N+1 problem?
3. **External calls** - Timeouts? Retries?
4. **User input** - Validated? Parameterized?
5. **Status codes** - Correct for the situation?
6. **Configuration** - Hardcoded? Production-ready?
7. **Scale** - What if 1000x more data/requests?

---

Return to [Resources](../README.md)
