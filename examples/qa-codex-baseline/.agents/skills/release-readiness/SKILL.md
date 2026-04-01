---
name: release-readiness
description: Use before release, hotfix rollout, or test exit to turn code changes, test evidence, and known issues into a structured 可发布 / 有条件发布 / 阻塞发布 recommendation with explicit risk statements.
origin: QA Codex Baseline
---

# Release Readiness

Use this skill when a release needs a decision, not just more raw test output.

## Goal

Convert scattered evidence into a release recommendation that a QA lead can defend:

- `可发布`
- `有条件发布`
- `阻塞发布`

## Inputs to Prefer

Gather as many of these as are available:

- changed scope or PR summary
- build status
- lint / typecheck / unit / integration / E2E results
- manual regression results
- open defects
- workaround or rollback options
- affected platforms

If some are missing, list the missing evidence before making the call.

## Decision Rules

### 可发布

Use only when:

- no unresolved release blockers remain
- critical paths were verified for affected platforms
- known issues are low risk or already accepted

### 有条件发布

Use when release is possible but depends on explicit conditions, such as:

- a known issue is accepted with workaround
- only part of the affected matrix was covered
- a low-confidence area needs monitoring after release

### 阻塞发布

Use when:

- a core path is broken or unverified
- a critical regression is open
- release risk is high and there is no acceptable mitigation

## Output Template

```text
RELEASE READINESS REPORT

Decision:
- 可发布 / 有条件发布 / 阻塞发布

Scope Reviewed:
- ...

Evidence Summary:
- build:
- tests:
- manual verification:
- open defects:

Blocking Issues:
- ...

Known Risks:
- risk: impact + likelihood + mitigation

Platform Readiness:
- Android:
- iOS:
- Windows:
- macOS:
- Web:

Release Conditions:
- ...

Recommendation:
- short conclusion
```

## Guardrails

- Do not say 可发布 without checking affected critical paths
- Do not hide unverified areas
- Distinguish blocker, accepted risk, and unknown risk
- Prefer short, decision-oriented language over long narration
