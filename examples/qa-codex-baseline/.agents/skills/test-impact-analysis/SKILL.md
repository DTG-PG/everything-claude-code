---
name: test-impact-analysis
description: Use when a feature, PR, build, or hotfix needs fast source-based test triage. Works for both frontend and backend repos: reads diff and code, identifies affected modules, platforms or callers, and outputs a risk-based minimal regression scope for QA.
origin: QA Codex Baseline
---

# Test Impact Analysis

Use this skill when the team needs to decide what to test before spending manual effort.

Typical triggers:

- A new feature is ready for test
- A hotfix lands close to release
- A large refactor needs test scope reduction
- QA only has the PR, branch, or source code and must triage quickly

## Goal

Turn source changes into a short, evidence-based testing decision:

- what changed
- what can break
- which platforms or callers are affected
- what must be tested now
- what can be deferred
- what is worth automating

## Workflow

### 1. Gather evidence

Read, in this order when available:

1. current diff or PR
2. changed files list
3. entry points and call chain
4. related tests
5. requirement or bug description

Do not guess test scope before reading the changed code.

### 2. Classify the change

Classify the change into one or more buckets:

- UI only
- business logic
- shared engine / core library
- API / backend contract
- database / schema / migration
- async jobs / queue / scheduler
- config / feature flag / environment
- permission / system integration
- file import / export
- payment / quota / membership
- scan / camera / media
- desktop shell integration
- web compatibility

Shared engine and contract changes usually deserve higher priority than isolated UI copy changes.

### 3. Map impact target

If this is a client repo, assess impact separately for:

- Android
- iOS
- Windows
- macOS
- Web

Never mark all platforms as required by default. State the reason for each affected platform.

If this is a backend repo, assess impact separately for:

- direct interfaces or jobs
- database and state changes
- affected callers
- rollback or compatibility risk

### 4. Produce minimal regression scope

Split the scope into:

- `P0` must test now
- `P1` should test if time allows
- `P2` can defer or cover later

Prefer the smallest scope that still protects release risk.

### 5. Identify automation candidates

Recommend automation only when the case is:

- high frequency
- stable enough to maintain
- reusable across products
- expensive to repeat manually

## Output Template

```text
TEST IMPACT REPORT

Change Summary:
- ...

Affected Modules:
- module: reason

Platform Impact:
- Android: yes/no + reason
- iOS: yes/no + reason
- Windows: yes/no + reason
- macOS: yes/no + reason
- Web: yes/no + reason

Or, for backend repos:

Caller Impact:
- Android / iOS / PC / macOS / Web / internal service: yes/no + reason

Contract and Data Risk:
- interface:
- schema:
- async jobs:
- config:

Risk Level:
- P0 / P1 / P2

Minimal Regression Scope:
- P0:
- P1:
- P2:

Automation Candidates:
- item: value + suggested layer

Evidence vs Inference:
- Confirmed by source:
- Inferred:
```

## Guardrails

- Do not output a giant full test case document unless explicitly asked
- Do not treat every changed file as equally risky
- Do not hide uncertainty; mark it clearly
- If build, test, or PR metadata is missing, say what is missing and continue with the best current judgment
