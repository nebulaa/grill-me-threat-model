---
id: TM-017
title: Setup scripts lack cluster context guard
severity: informational
stride: Tampering
status: draft
response: mitigate
mitigation-state: non-mitigated
owner:
created: 2026-05-23
updated: 2026-05-23
training-surface: operator-risk
---

# Setup scripts lack cluster context guard

## Summary

Neither `setup-kubernetes-goat.sh` nor `platforms/kind-setup/setup-kind-cluster-and-goat.sh` validates cluster name, namespace existence for production markers, or prompts before apply. This is a **control gap** amplifying TM-001.

## Context

| Field | Value |
|-------|-------|
| Asset | Operator safety |
| Trust boundary | TB-6 |
| Entry point | EP-7 |
| Data flow | DF-1 |
| Component | setup scripts |

## Scenario

Automated CI or tired operator runs setup with no friction; TM-001 manifests deploy immediately.

## Attack vectors

- Human error (primary)
- CI misconfiguration

## Response

**Primary response:** mitigate

**Rationale:** Low direct exploitability; high value defense-in-depth with TM-001 controls.

**User decision:** *Pending walkthrough.*

## Existing controls

_No verified controls._

## Recommended actions

Agreed or proposed steps **not yet** implemented. Do not list here under Existing controls.

| # | Action | Owner | Tracking |
|---|--------|-------|----------|
| 1 | Merge with TM-001 context guard implementation |  | none |
| 2 | Add teardown confirmation symmetry |  | none |
| 3 | `kubectl config view --minify` in docs checklist |  | none |

## Implementation verification

| Check | Done | Evidence |
|-------|------|----------|
| Code / config implements control | ☐ | |
| `system-model.md` (or ADR) updated | ☐ | |
| Re-read paths after change | ☐ | |

## Residual risk

Cannot eliminate operator mistake entirely.

## Walkthrough log

| Date | Action |
|------|--------|
| 2026-05-23 | Created; mitigation-state: non-mitigated |
| 2026-05-23 | Format migrated to decision vs implementation (skill update) |
