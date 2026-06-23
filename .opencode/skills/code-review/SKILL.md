---
name: code-review
description: Use when reviewing code for quality, security, performance, maintainability, and adherence to standards. Covers PR reviews, code analysis, identifying bugs, anti-patterns, and providing actionable feedback. Trigger keywords: code review, PR review, pull request, code quality, security review, performance review, anti-pattern, refactor, technical debt, linting, static analysis.
---

# Code Review

## Goal
Ensure code quality, security, and maintainability through a systematic review that identifies problems, suggests improvements, and validates conformance to project standards.

## Review process

### 1. Pre-review context
Before starting, verify:
- [ ] Is the PR description clear and complete?
- [ ] Is the PR linked to a user story or ticket?
- [ ] Do tests pass in CI?
- [ ] Has coverage not decreased?
- [ ] No merge conflicts?

### 2. Review checklist

#### Functional correctness
- [ ] Does the code do what the user story requires?
- [ ] Are all acceptance criteria satisfied?
- [ ] Are edge cases handled (null, undefined, boundary values)?
- [ ] Is error handling appropriate?
- [ ] No regressions introduced?

#### Code quality
- [ ] Is the code readable and self-documenting?
- [ ] Are variable/function names clear and descriptive?
- [ ] Do functions have a single responsibility?
- [ ] No duplication (DRY)?
- [ ] Acceptable cyclomatic complexity (<10 per function)?
- [ ] No dead or commented-out code?
- [ ] Comments only where truly necessary (the why, not the what)?

#### Security (OWASP Top 10)
- [ ] Input validated and sanitized?
- [ ] No secrets or credentials in code?
- [ ] SQL injection prevented (parameterized queries)?
- [ ] XSS prevented?
- [ ] Authorization verified for every endpoint?
- [ ] Sensitive data not logged?
- [ ] Secure dependencies (no known vulnerabilities)?

#### Performance
- [ ] No N+1 queries?
- [ ] Expensive operations optimized?
- [ ] Caching used appropriately?
- [ ] No unnecessary re-renders (frontend)?
- [ ] Memory leaks prevented?

#### Tests
- [ ] Tests cover the acceptance criteria?
- [ ] Tests for edge cases?
- [ ] Tests for error cases?
- [ ] No fragile tests (order-dependent, timing-dependent)?
- [ ] Mocks used appropriately?

#### Architecture and design
- [ ] Does the code respect the defined architecture?
- [ ] No layer boundary violations?
- [ ] Dependencies flowing in the correct direction?
- [ ] Patterns used consistently?

## Feedback format

### Comment severity

```
🔴 BLOCKING: Critical bug, security issue, serious architecture violation
   -> Must be fixed before merge

🟡 IMPORTANT: Quality problem, performance issue, missing test
   -> Should be fixed or explained

🟢 SUGGESTION: Optional improvement, style, readability
   -> Welcome but not blocking
```

### Review comment template

```markdown
**[BLOCKING/IMPORTANT/SUGGESTION]** - [Category]

**Problem**: [Clear description of the problem]

**Impact**: [What could go wrong, or why it matters]

**Proposed solution**:
```suggested_code```

**Reference**: [Link to best practice, doc, or project standard]
```

### Example of constructive feedback

```markdown
🔴 BLOCKING - Security

**Problem**: The endpoint `/api/users/:id` does not verify that the authenticated
user is allowed to access the requested profile. An authenticated user with id=1
can access `/api/users/2`.

**Impact**: Insecure Direct Object Reference (IDOR) - user data breach.

**Proposed solution**:
```typescript
// Add authorization check
if (req.user.id !== params.id && !req.user.roles.includes('admin')) {
  throw new ForbiddenError('Cannot access other users data');
}
```

**Reference**: OWASP A01:2021 Broken Access Control
```

## Anti-patterns to always flag

### Backend
```typescript
// ❌ Unbounded query (DoS risk)
const allUsers = await db.query('SELECT * FROM users');

// ✓ With pagination
const users = await db.query('SELECT * FROM users LIMIT $1 OFFSET $2', [limit, offset]);

// ❌ Hardcoded secret
const apiKey = 'sk-1234abcd...';

// ✓ From environment variable
const apiKey = process.env.EXTERNAL_API_KEY;
if (!apiKey) throw new Error('EXTERNAL_API_KEY not configured');

// ❌ Generic catch that hides errors
try { ... } catch (e) { console.log('error'); }

// ✓ Specific handling with structured logging
try { ... } catch (e) {
  if (e instanceof DomainError) { ... }
  logger.error({ err: e, context: 'operation-name' }, 'Unexpected error');
  throw e;
}
```

### Frontend
```tsx
// ❌ useEffect with side effects not cleaned up
useEffect(() => {
  const sub = eventEmitter.on('event', handler);
  // missing: return () => eventEmitter.off('event', handler);
});

// ❌ State update on unmounted component
useEffect(() => {
  fetchData().then(data => setState(data)); // can leak
  // ✓ use AbortController or isMounted flag
});
```

## Review summary

At the end of the review, produce a summary:

```markdown
## Review Summary

**Outcome**: Approved | Approved with suggestions | Changes requested | Rejected

**Blocking issues**: [N]
**Important issues**: [N]
**Suggestions**: [N]

**Strengths**:
- ...

**Areas for improvement**:
- ...

**Notes for next cycle**:
- ...
```

## Handoff to the SDLC loop

- If `Changes requested`: the cycle returns to the appropriate dev (frontend or backend)
- If `Approved`: code passes to the Tester for final validation
- If critical bugs found: create tickets and assign priority

## Final review checklist
- [ ] All checklist aspects verified
- [ ] Feedback written constructively and specifically
- [ ] Every comment has a clear severity
- [ ] Solutions proposed for blocking issues
- [ ] Review summary produced
- [ ] Final decision communicated (Approved / Changes Requested)
