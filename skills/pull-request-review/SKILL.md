---
name: pull-request-review
description: Reviews a pull request or code diff for correctness, security, regressions, missing tests, maintainability, and material performance issues. Use before merge or when asked to assess a proposed code change.
license: MIT
metadata:
  author: Commonset
  version: "1.0.0"
---

# Pull Request Review

Review the proposed change for material issues that could affect users, security, operations, or maintainability. Prefer a small number of specific, well-supported findings over broad commentary.

Do not spend review attention on style preferences unless they create a real correctness or maintenance problem.

## Review workflow

### 1. Understand the intended change

Read the pull request description, linked issue or requirement, and the changed files. Identify:

- the user-visible or operational behavior being changed
- the invariants that should remain unchanged
- relevant authorization, data, provider, or API boundaries
- the tests that are expected to protect the behavior

If the intent is unclear, state the ambiguity before judging the implementation.

### 2. Inspect the smallest relevant surrounding context

Do not review the diff in isolation when surrounding code determines behavior. Inspect the functions, models, migrations, templates, configuration, tests, or callers needed to answer whether the change is correct.

Avoid expanding into unrelated refactoring.

### 3. Check correctness and regressions

Look for concrete issues such as:

- incorrect branching, state transitions, or edge-case handling
- stale or inconsistent data after the change
- behavior that works on the happy path but fails on retries, duplicates, partial failure, or empty input
- backwards-incompatible API, schema, configuration, or persisted-data changes
- authorization checks that moved to a weaker boundary or can be bypassed
- mismatches between server-side enforcement and user-visible controls
- existing behavior unintentionally removed or broadened

When you identify a regression, explain the input or sequence that triggers it.

### 4. Check security and trust boundaries

Pay particular attention to:

- tenant or organization scoping
- authentication and authorization
- secrets, credentials, tokens, and sensitive values in logs or errors
- untrusted files, URLs, archives, templates, or provider content
- path traversal, injection, unsafe deserialization, and command execution
- permission widening or confused-deputy behavior
- audit records that omit important decisions or expose sensitive content
- fail-open behavior in security-critical paths

Distinguish capability from actual behavior. Do not report a security issue solely because an API or tool is available.

### 5. Check performance where it is material

Look for avoidable work that is likely to matter in the changed path, including:

- N+1 database queries
- repeated external API or LLM calls
- duplicate parsing, hashing, downloads, or storage operations
- unbounded reads or large payloads
- expensive work repeated when a prior result can safely be reused

Do not micro-optimize code that is already simple and bounded.

### 6. Check tests against user-visible behavior

Ask whether tests cover the behavior that could realistically break, including relevant positives and negatives.

For security or governance changes, consider:

- allowed case
- denied case
- near miss
- crafted direct request that bypasses UI controls
- cross-tenant or cross-scope access
- lifecycle transition before and after the change

A missing test is a finding when it leaves a meaningful regression unprotected, not merely because every branch lacks a dedicated assertion.

### 7. Report only actionable findings

Order findings by severity and importance. Each finding should include:

- **Title**: concrete problem
- **Severity**: Critical, High, Medium, Low
- **Evidence**: file and line, diff hunk, or precise code location
- **Trigger**: the input, state, or sequence that exposes the problem
- **Impact**: what breaks or becomes unsafe
- **Smallest fix**: the narrowest appropriate layer to correct it
- **Regression test**: what behavior should be locked down

If evidence is incomplete, label the concern as needing verification rather than stating it as fact.

## Output format

Use findings first:

```markdown
## Findings

### [High] Authorization can be bypassed by direct POST
- Evidence: `path/to/view.py:123`
- Trigger: ...
- Impact: ...
- Smallest fix: ...
- Regression test: ...
```

Then finish with:

```markdown
## Review summary
- Material findings: ...
- Tests reviewed: ...
- Residual uncertainty: ...
```

If there are no material findings, say `No material findings.` and briefly note meaningful test or context limitations.

## Review principles

- Review behavior, not formatting preferences.
- Prefer root-cause fixes over accumulating exceptions.
- Preserve existing security controls when fixing false positives; improve precision instead of weakening enforcement.
- Separate provider-specific behavior from core domain logic when practical.
- Prefer deterministic code over new LLM or external-service calls when deterministic logic solves the problem reliably.
- Avoid recommending new abstractions, services, caches, or asynchronous workflows unless the change actually requires them.
- Do not request unrelated cleanup as part of the current pull request.
