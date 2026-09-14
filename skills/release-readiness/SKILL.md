---
name: release-readiness
description: Assesses whether a software change is ready to ship across data, configuration, security, observability, rollout, rollback, and smoke coverage. Use before deployment, release approval, or production rollout.
license: MIT
metadata:
  author: Commonset
  version: "1.0.0"
---

# Release Readiness

Assess whether a change can be deployed safely and recovered if something goes wrong. Base the decision on the actual change, deployment environment, and available evidence rather than a generic checklist.

The goal is not to prove that a release is risk-free. The goal is to make blocking risks, verification steps, and rollback assumptions explicit before production changes begin.

## Review workflow

### 1. Establish release scope

Identify:

- code and configuration being released
- services, workers, scheduled jobs, or clients affected
- schema or persisted-data changes
- external systems or providers involved
- deployment order when more than one component changes
- user-visible behavior expected after release

If the deployment mechanism or environment is unknown, state that limitation instead of inventing one.

### 2. Check data and schema safety

For migrations or persisted-data changes, verify:

- forward migration behavior
- compatibility between old and new application versions during rollout
- defaults, nullability, indexes, constraints, and backfills
- locking or long-running operations on realistic data volumes
- whether rollback requires a reverse migration or data restoration
- whether destructive transformations preserve a recovery path

Treat "migration applies successfully" and "release is operationally safe" as separate questions.

### 3. Check configuration and secrets

Identify new or changed:

- environment variables
- credentials or provider configuration
- feature flags
- URLs, callback origins, or allowed-host settings
- queues, buckets, databases, or external resources

Verify that missing security-critical configuration fails safely and that secrets do not appear in logs, audit events, error messages, or generated artifacts.

### 4. Check external dependencies and failure modes

For provider APIs, databases, queues, storage, webhooks, or other remote systems, ask:

- What happens on timeout, rate limit, retry, duplicate delivery, or partial failure?
- Is the operation idempotent where retries are possible?
- Are expensive or rate-limited calls duplicated unnecessarily?
- Can the release degrade safely if the dependency is unavailable?
- Is the changed behavior observable enough to diagnose provider-specific failures?

Do not require speculative infrastructure for failure modes the release does not introduce.

### 5. Check security and authorization

Verify the release does not unintentionally change:

- tenant or organization boundaries
- roles, grants, or approval authority
- authentication or token lifecycle
- access to sensitive artifacts
- enforcement around untrusted inputs
- audit coverage for important decisions

For security-critical changes, make sure failure behavior is explicit and appropriately fail-closed.

### 6. Check observability

Confirm that a production operator can answer:

- Did the deployment succeed?
- Is the new path being exercised?
- Are errors, latency, or external failures increasing?
- Can a specific failed operation be traced without exposing secrets?
- Is there a useful health, smoke, log, metric, or audit signal for the changed behavior?

Operational logs and audit records have different purposes; do not use one as a substitute for the other when the distinction matters.

### 7. Check rollout and rollback

Make the operational path explicit:

- deployment sequence
- pre-deploy step, if any
- post-deploy verification
- condition that should stop or roll back the release
- rollback mechanism
- data recovery requirement when rollback alone is insufficient

A rollback plan that only says "revert the code" is incomplete when the release changes schema, external state, or irreversible data.

### 8. Check production smoke coverage

Choose the smallest smoke test that proves the changed workflow works in the real environment without causing unsafe side effects.

Prefer checks that validate the user-visible behavior and important boundaries rather than only process health.

Examples:

- page or API path responds under production routing
- authorization permits the intended actor and denies an unintended actor
- a provider connection can perform the exact changed operation
- a migration-dependent workflow reads and writes the expected shape
- a reversible test event appears in logs or audit history

### 9. Make the release decision

Use one of these states:

- **Ready**: no known blocker remains; required verification and rollback path are credible.
- **Ready with follow-ups**: safe to release, with non-blocking work that should be tracked separately.
- **Not ready**: a material issue should be resolved before deployment.

Do not use a numeric readiness score.

## Output format

```markdown
# Release readiness

## Decision
Status: Ready / Ready with follow-ups / Not ready
Reason: ...

## Blocking items
- ...

## Release plan
1. ...
2. ...

## Verification
- ...

## Rollback and recovery
- Rollback trigger: ...
- Code/config rollback: ...
- Data/external-state recovery: ...

## Non-blocking follow-ups
- ...

## Unknowns
- ...
```

Omit empty sections where that improves clarity, except `Decision` and `Unknowns`.

## Principles

- Prefer the smallest release plan that safely proves the change.
- Separate code rollback from data recovery.
- Do not call a release ready when a required migration, configuration value, credential, or external resource is still unknown.
- Do not create process for its own sake. A low-risk change with no schema, configuration, or external-system impact should have a proportionate release plan.
- When a bug previously reached production or Preview, require a regression check that would have detected the failure.
