---
name: cross-platform-regression
description: Use when a product family spans Android, iOS, Windows, macOS, and Web, but the current repo may represent only one platform or one shared layer. First determine current repo scope, then expand to sibling-platform follow-up checks only when evidence justifies it.
origin: QA Codex Baseline
---

# Cross-Platform Regression

Use this skill to avoid three common QA failures:

1. treating every platform as equal on every change
2. testing every feature on every platform for every release
3. assuming the current repo automatically represents every platform repo

## Goal

Generate a small but defensible regression matrix for the current repo first, and only then add justified sibling-platform follow-up checks.

## Repo Scope Rule

Start by identifying what the current repo actually is:

- Android-only repo
- iOS-only repo
- Windows or macOS desktop repo
- Web repo
- backend or service repo
- shared core or protocol repo

Default rule:

- current repo direct impact first
- sibling-platform follow-up only with evidence

Do not turn a single-platform repo into a fake all-platform regression plan.

## Default Capability Model

Start from reusable capability blocks instead of page lists:

- install / update / startup
- login / guest mode
- permission request
- file import
- scan / capture / media input
- core processing flow
- export / save / share
- payment / entitlement / quota
- error handling / recovery
- performance / crash smoke

Then add platform-specific concerns only where needed.

## Workflow

### 1. Determine current repo scope

Ask first:

- what platform or layer does this repo serve?
- is this repo self-contained or shared by multiple products?
- does the diff touch a shared contract, shared format, or shared backend behavior?

### 2. Separate direct vs sibling-platform risk

Ask:

- is the change only in the current repo?
- is the change in shared core logic?
- is the change only in one shell or adapter?
- does it touch a system API or OS behavior?
- does it alter file system, permission, or rendering behavior?
- does it change a shared file format, protocol, quota rule, or membership rule?

### 3. Build a minimal matrix

Organize output into:

- `P0` smoke
- `P1` focused regression
- `P2` deferred checks

For each item, state:

- capability
- platform
- why this matters now

For sibling platforms, list them separately as follow-up only if evidence supports them.

### 4. Reduce manual duplication

If the same core logic is reused across products or platforms:

- recommend one representative manual check
- recommend the best automation layer
- avoid repeating low-value checks everywhere

## Output Template

```text
REPO-SCOPED REGRESSION MATRIX

Current Repo Type:
- Android / iOS / Windows / macOS / Web / Backend / Shared Core

P0 Smoke:
- capability | platform | reason

P1 Focused Regression:
- capability | platform | reason

P2 Deferred:
- capability | platform | reason

Sibling Platform Follow-up:
- platform or repo | reason | evidence level

Shared Risks:
- ...

Platform-Specific Risks:
- ...

Suggested Automation:
- item | layer | value
```

## Guardrails

- Do not generate a fake “full coverage” matrix
- Do not treat desktop and web as interchangeable
- Do not ignore permission, file path, and export behavior in tool apps
- Do not pull sibling platform repos into scope without evidence
- Prefer a smaller matrix with explicit reasoning
