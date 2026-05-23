---
id: TM-014
title: AWS CDK EC2 path expands blast radius
severity: medium
stride: Information disclosure
status: draft
response: mitigate
mitigation-state: partially-mitigated
owner:
created: 2026-05-23
updated: 2026-05-23
training-surface: operator-risk
---

# AWS CDK EC2 path expands blast radius

## Summary

`platforms/aws-setup/aws-kind-ec2-cdk` provisions EC2 with security group rules for ports 1230–1234 and runs full goat setup via user-data. Documented for remote labs but conflicts with **local-only** primary scope — operators may expose vulnerable services to AWS network if `whitelist_ip` is misconfigured.

## Context

| Field | Value |
|-------|-------|
| Asset | EC2 instance; cloud account |
| Trust boundary | TB-1 (cloud VPC) |
| Entry point | EP-9 |
| Data flow | DF-1 via SSH/bootstrap |
| Component | `aws_kind_ec2_cdk_stack.py`, `user_data.sh` |

## Scenario

Operator deploys CDK stack with overly broad `whitelist_ip` (e.g. `0.0.0.0/0` typo). Internet reaches goat port-forwards on EC2; combined with privileged pods, AWS account compromise chain.

## Attack vectors

- Misconfigured SG ingress
- Stale Ubuntu 16.04 AMI with unpatched packages
- SSH key exposure

## Framework mapping (optional)

| Framework | ID | Name |
|-----------|-----|------|
| CWE | CWE-284 | Improper Access Control |

## Response

**Primary response:** mitigate

**Rationale:** For local scope, steer operators to KIND; harden CDK docs and defaults when AWS path used for workshops.

**User decision:** *Pending walkthrough.*

## Existing controls

Verified at architecture baseline or in this repo. Planned work is under **Recommended actions** only.

| Control | Addresses threat? | Evidence |
|---------|-------------------|----------|
| KIND path documented as primary local setup in guide | partial | `guide/docs/how-to-run/kind.md` @ baseline `723a0db478f050d173d23b4ce5044b65bce0bdd0` |

## Recommended actions

Agreed or proposed steps **not yet** implemented. Do not list here under Existing controls.

| # | Action | Owner | Tracking |
|---|--------|-------|----------|
| 1 | Banner in `aws-kind-ec2-cdk/README`: not for production; narrow SG |  | none |
| 2 | **Local scope:** mark AWS path remote workshop optional |  | none |
| 3 | CDK validate `whitelist_ip` not `0.0.0.0/0` |  | none |
| 4 | Update AMI to supported LTS (replace xenial in stack) |  | none |

## Implementation verification

| Check | Done | Evidence |
|-------|------|----------|
| Code / config implements control | ☐ | |
| `system-model.md` (or ADR) updated | ☐ | |
| Re-read paths after change | ☐ | |

## Residual risk

Cloud-deployed labs remain inherently exposed to network threats.

## Walkthrough log

| Date | Action |
|------|--------|
| 2026-05-23 | Created; mitigation-state: partially-mitigated |
| 2026-05-23 | Format migrated to decision vs implementation (skill update) |
