# Security Fundamentals

Security isn't optional foundation concepts every system designer must understand.

---

## Security Mindset

Security is not a feature you add at the end. It's a property of the system that must be considered from the start.

**Defense in depth:** Multiple layers of security. No single point of failure.

**Principle of least privilege:** Grant minimum access needed. Reduce blast radius.

**Assume breach:** Design assuming attackers will get in. Limit what they can do.

---

## Authentication vs. Authorization

### Authentication

**Who are you?** Verifying identity.

- Username/password
- Multi-factor authentication (MFA)
- OAuth/OIDC
- API keys
- Certificates

### Authorization

**What can you do?** Verifying permissions.

- Role-based access control (RBAC)
- Attribute-based access control (ABAC)
- Access control lists (ACLs)
- Policy engines

**Authentication comes first.** Can't authorize unknown users.

---

## Common Authentication Methods

### Session-Based

Traditional web authentication.

```
1. User logs in with credentials
2. Server creates session, stores in database/Redis
3. Server returns session ID in cookie
4. Client sends cookie with each request
5. Server validates session
```

**Pros:** Can revoke instantly, works with cookies.
**Cons:** Server must store sessions, scaling requires shared storage.

### Token-Based (JWT)

Stateless authentication.

```
1. User logs in with credentials
2. Server returns signed JWT
3. Client sends JWT in Authorization header
4. Server validates signature (no database lookup)
```

**Pros:** Stateless, scales easily.
**Cons:** Can't revoke until expiry (unless using blocklist).

### JWT Structure

```
header.payload.signature

Header: {"alg": "HS256", "typ": "JWT"}
Payload: {"sub": "user123", "exp": 1616098800}
Signature: HMAC(header + payload, secret)
```

**Never trust unverified JWTs.** Always verify signature.

### OAuth 2.0

Delegated authorization. "Login with Google."

```
1. User clicks "Login with Google"
2. Redirect to Google
3. User authenticates with Google
4. Google redirects back with authorization code
5. Your server exchanges code for access token
6. Use access token to get user info
```

**Use for:** Social login, third-party integrations.

---

## API Security

### API Keys

Simple identifier for applications.

```
GET /api/data
X-API-Key: abc123xyz
```

**Use for:** Server-to-server, identifying applications.
**Not for:** User authentication (no identity).

### Rate Limiting

Prevent abuse.

```
100 requests per minute per API key
10 requests per minute for login attempts
```

### Input Validation

Never trust input. Validate everything.

- Type checking
- Length limits
- Format validation
- Allowlist over blocklist

---

## Common Vulnerabilities

### SQL Injection

Malicious SQL in user input.

```sql
-- User input: ' OR '1'='1
SELECT * FROM users WHERE username='' OR '1'='1'
-- Returns all users!
```

**Prevention:** Parameterized queries, ORMs, input validation.

### XSS (Cross-Site Scripting)

Malicious script injected into page.

```html
<!-- User input: <script>steal(cookies)</script> -->
<div>Hello, <script>steal(cookies)</script></div>
```

**Prevention:** Output encoding, Content Security Policy (CSP).

### CSRF (Cross-Site Request Forgery)

Tricks user into making unwanted request.

```html
<!-- Evil site includes -->
<img src="https://bank.com/transfer?to=attacker&amount=1000">
```

**Prevention:** CSRF tokens, SameSite cookies.

### Broken Access Control

User can access/modify others' data.

```
GET /api/orders/1234  <!-- User sees their order -->
GET /api/orders/5678  <!-- User sees SOMEONE ELSE'S order -->
```

**Prevention:** Always verify ownership/permissions.

---

## Data Security

### Encryption at Rest

Data encrypted when stored.

- Database encryption
- Disk encryption
- Field-level encryption for sensitive data

### Encryption in Transit

Data encrypted when moving.

- HTTPS everywhere
- TLS 1.2/1.3
- mTLS for service-to-service

### Secrets Management

Don't hardcode secrets.

**Do:**
- Use secrets manager (AWS Secrets Manager, HashiCorp Vault)
- Inject at runtime
- Rotate regularly
- Encrypt in storage

**Don't:**
- Commit to version control
- Log secrets
- Pass in URLs or query strings

### PII Handling

Personally Identifiable Information needs extra care.

- Minimize collection
- Encrypt sensitive fields
- Access logging
- Retention policies
- Right to deletion (GDPR)

---

## Network Security

### Firewalls and Security Groups

Restrict network access.

```
Web servers: Allow inbound 80, 443
App servers: Allow from web tier only
Database: Allow from app tier only
```

**Principle:** Minimum necessary network access.

### VPC and Private Networks

Keep internal services private.

```
Public subnet: Load balancer
Private subnet: Application servers
Private subnet: Databases

Database never directly accessible from internet
```

### TLS/mTLS

**TLS:** Server authenticated, connection encrypted.
**mTLS:** Both client and server authenticated.

Use mTLS for internal service-to-service communication.

---

## Security in Design

### Threat Modeling

Systematically identify threats.

**STRIDE framework:**
- **S**poofing: Pretending to be someone else
- **T**ampering: Modifying data
- **R**epudiation: Denying actions
- **I**nformation disclosure: Unauthorized access to data
- **D**enial of service: Making system unavailable
- **E**levation of privilege: Gaining unauthorized access

### Security Reviews

Before launch:
- Code review for security issues
- Dependency scanning (known vulnerabilities)
- Penetration testing
- Security architecture review

### Incident Response

Prepare for breaches:
- Detection and alerting
- Response procedures
- Communication plan
- Post-mortem process

---

## Common Mistakes

**Trusting client input.** Always validate server-side.

**Rolling your own crypto.** Use established libraries.

**Storing passwords in plain text.** Hash with bcrypt, argon2.

**Overly permissive access.** Grant minimum needed.

**No logging/monitoring.** Can't detect breaches.

**Secrets in code.** Use secrets management.

**Missing HTTPS.** All traffic should be encrypted.

---

## What An Experienced Senior Engineer Thinks About

**Security vs. usability.** Too many hoops and users work around. Balance.

**Compliance requirements.** GDPR, SOC2, HIPAA, PCI. Know what applies to you.

**Supply chain security.** Dependencies can have vulnerabilities. Scan and update.

**Zero trust architecture.** Don't trust internal network. Verify everything.

**Security culture.** Training, awareness, blameless reporting.

---

## Vibe Engineering Guide

When prompting about security:

**Less useful:**
> "Secure my application"

**More useful:**
> "Review security for my REST API:
> - JWT authentication with 24-hour expiry
> - PostgreSQL database with user data (email, password hash)
> - Redis session store
> - Runs on AWS in VPC
>
> What are the potential vulnerabilities? Should I use shorter JWT expiry with refresh tokens? How should I structure security groups?"

**For specific scenarios:**
> "I need to store credit card numbers for recurring billing. I know about PCI compliance. Can I store them encrypted in my database, or must I use a payment processor's vault?"

---

## Quick Check

<details>
<summary><b>What's the difference between authentication and authorization?</b></summary>

Authentication: verifying who you are (identity). Authorization: verifying what you can do (permissions). Authentication must happen first.

</details>

<details>
<summary><b>Why can't you revoke a JWT immediately?</b></summary>

JWTs are self-contained and verified by signature, not database lookup. Once issued, they're valid until expiry. Revocation requires a blocklist (adds state) or short expiry with refresh tokens.

</details>

<details>
<summary><b>What's defense in depth?</b></summary>

Multiple layers of security. If one fails, others protect. Examples: firewall + authentication + authorization + encryption + monitoring. No single point of failure.

</details>

<details>
<summary><b>How do you prevent SQL injection?</b></summary>

Parameterized queries (prepared statements) or ORMs. Never concatenate user input into SQL strings. Validate input.

</details>

---

Next: [Level 9: Vibecoding Masterclass](../09-vibecoding-masterclass/README.md)
