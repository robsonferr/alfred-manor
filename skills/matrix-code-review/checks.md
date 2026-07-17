# Review checks (generic, project-agnostic)

The categories reviewers scan. Not tied to any project — adapt or extend for your stack. Each finding gets a severity: **🔴 Critical / 🟡 Important / 🟢 Suggestion**, and a category code below.

## SEC — Security
- Hardcoded secrets, credentials, tokens, connection strings
- Injection (SQL / command / template): unsanitised input reaching a sink
- Access control / authorization: missing or bypassable checks; privilege escalation; **multi-tenant / cross-account data leakage**; whether a guard **fails closed** (denies) vs **fails open** (leaks) when context is missing
- XSS / output encoding
- Insecure deserialization of untrusted input
- Missing input validation on external/user data

## COR — Correctness & bugs
- Edge cases: empty / null, boundary values, unexpected types
- Error handling: swallowed exceptions, unchecked results, missing cleanup
- Concurrency: race conditions, shared mutable state, lifetime/scope mismatches (e.g. per-request vs singleton)
- Data integrity: lost updates, partial writes, missing transactions
- Logic that contradicts the stated spec / acceptance criteria (when a spec is provided)

## PERF — Performance
- N+1 queries; missing pagination; unbounded result sets
- Blocking calls on hot paths; sync-over-async
- Repeated expensive work; missing memoization where it matters
- Large allocations / needless copies in loops

## ARCH — Architecture & structure
- Layering / boundary violations; wrong-direction dependencies
- Duplicated logic across files or modules
- Dead code paths left behind after the change
- Business logic in the wrong place (controllers / UI components)

## API — Contracts
- Breaking changes to a public API, response shape, or serialization
- Caller ↔ implementation contract mismatch (shape, type, nullability)
- Inconsistent response formats across endpoints

## QUAL — Code quality
- Debug / print statements, commented-out code
- Magic numbers, unclear names, dead config
- Over-engineering / needless complexity (KISS)

## TEST — Tests
- New logic shipped without tests; critical paths uncovered
- Tests that assert nothing meaningful

---

**Severity guide.** 🔴 = correctness/security defect that must be fixed before merge, or a broken acceptance criterion. 🟡 = real issue worth fixing but not a blocker. 🟢 = optional improvement / nit.

**Verdict.** *Approved* = zero 🔴. *Changes requested* = one or more 🔴.
