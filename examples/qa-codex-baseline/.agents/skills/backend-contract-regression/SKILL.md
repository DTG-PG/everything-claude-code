---
name: backend-contract-regression
description: Use when the current repo is a backend API, service, admin backend, gateway, or job scheduler. Reads code and diff to identify interface contract changes, schema and migration risk, async task impact, affected callers, and the smallest justified regression scope.
origin: QA Codex Baseline
---

# Backend Contract Regression

Use this skill when QA is looking at a backend repo instead of a client repo.

Typical triggers:

- API service feature delivery
- hotfix in a backend repo
- response field or parameter changes
- database schema or migration changes
- queue, callback, scheduler, or worker logic changes
- config, auth, rate limit, or gray release changes

## Goal

Turn backend code changes into a short QA decision:

- what backend surface changed
- which callers may be affected
- whether compatibility is at risk
- what must be validated now
- what can be checked later
- what should be automated

## Workflow

### 1. Identify backend repo type

Classify the current repo first:

- API service
- gateway or BFF
- admin backend
- job scheduler or worker
- shared backend library

### 2. Read the change through a backend QA lens

Inspect these areas when present:

- endpoint definitions
- request and response models
- auth, permission, rate limiting
- database schema, migrations, indexes
- cache logic
- message queue, callback, scheduler, retry logic
- feature flags and environment-dependent branches
- logging, metrics, and error codes

### 3. Map affected callers

Do not stop at “backend changed”.

List the likely affected callers:

- Android
- iOS
- Windows
- macOS
- Web
- internal services
- admin systems

Only include a caller when source evidence or a clear dependency supports it.

### 4. Produce minimal regression scope

Split into:

- `P0` must verify now
- `P1` should verify if time allows
- `P2` can defer or monitor

Backend QA usually prioritizes:

- backward compatibility
- data consistency
- idempotency and retry behavior
- critical error handling
- rollback safety

### 5. Recommend automation

Prefer:

- API contract tests
- integration tests
- migration verification
- queue or worker result checks
- smoke checks for key downstream callers

## Output Template

```text
BACKEND REGRESSION REPORT

Repo Type:
- API / Gateway / Admin Backend / Worker / Shared Backend

Change Summary:
- ...

Direct Backend Impact:
- interfaces:
- schema:
- async jobs:
- config:
- auth and permissions:

Affected Callers:
- caller: reason

Risk Level:
- P0 / P1 / P2

Minimal Regression Scope:
- P0:
- P1:
- P2:

Automation Candidates:
- item: layer + value

Evidence vs Inference:
- Confirmed by source:
- Inferred:
```

## Guardrails

- Do not reduce backend testing to page-level UI checks
- Do not assume response compatibility without checking models or serialization
- Do not ignore rollback, dirty data, duplicate consumption, or stale cache risk
- Do not pull every caller into scope without evidence
