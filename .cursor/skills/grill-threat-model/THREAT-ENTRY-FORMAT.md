# Threat Entry Format

Each threat is one markdown file: `threat-model/threats/TM-NNN-short-slug.md`.

## Template

```markdown
---
id: TM-001
title: Short descriptive title
severity: critical | high | medium | low | informational
stride: Spoofing | Tampering | Repudiation | Information disclosure | Denial of service | Elevation of privilege
status: draft | reviewed | accepted | mitigated | superseded
response: avoid | mitigate | accept | transfer
mitigation-state: non-mitigated | partially-mitigated | fully-mitigated
owner: optional
created: YYYY-MM-DD
updated: YYYY-MM-DD
---

# {Title}

## Summary

{2–4 sentences: what can go wrong, on which asset, through which entry point.}

## Context

| Field | Value |
|-------|-------|
| Asset | {from system-model} |
| Trust boundary | {boundary ID or name} |
| Entry point | {EP-ID or description} |
| Data flow | {DF-ID or "n/a"} |
| Component | {affected component} |

## Scenario

{Concrete attack story: actor → steps → impact.}

## Attack vectors

- {Vector 1 — e.g. "Unauthenticated POST /api/v1/orders"}
- {Vector 2}

## Framework mapping (optional)

| Framework | ID | Name |
|-----------|-----|------|
| MITRE ATT&CK | Txxxx | {technique} |
| CAPEC | CAPEC-xxx | {pattern} |
| CWE | CWE-xxx | {weakness} |

## Risk scoring (optional)

Use when severity is disputed.

### DREAD

| Dimension | Score (0–10) | Notes |
|-----------|--------------|-------|
| Damage | | |
| Reproducibility | | |
| Exploitability | | |
| Affected users | | |
| Discoverability | | |

### CVSS 4.0

| Metric | Value |
|--------|-------|
| Vector | {CVSS:4.0/AV:N/AC:L/...} |
| Score | {x.x} |
| Severity | {None/Low/Medium/High/Critical} |

Calculator: https://www.first.org/cvss/calculator/4.0#

## Response

**Primary response:** {avoid | mitigate | accept | transfer}

**Rationale:** {Why this response fits risk profile and maturity.}

**User decision:** {Record what the user chose in walkthrough; date.}

## Mitigations

| Control | Type | State | Evidence |
|---------|------|-------|----------|
| {e.g. MFA for admin} | Prevent | planned / implemented | {file:link, ADR, ticket} |

### Recommended actions (agent)

1. {Concrete step — e.g. "Add rate limiting on `POST /login` in `api/auth/routes.ts`"}
2. {Step 2}

### Mitigation references (optional)

- D3FEND: {technique ID or URL}
- OWASP: {cheat sheet name + URL}
- Microsoft category: {e.g. Authentication, Input Validation}

## Residual risk

{What remains after mitigations; required for accept/partial.}

## Walkthrough log

| Date | Action |
|------|--------|
| YYYY-MM-DD | Created from system model |
| YYYY-MM-DD | User chose mitigate; ticket ABC-123 |
```

## Rules

- **One threat per file** — do not combine unrelated STRIDE categories unless they share a single exploit chain.
- **Severity is stable** unless new evidence changes impact; document changes in walkthrough log.
- **Evidence column** — point to code, config, or ticket; never invent "implemented" without verification.
- **Superseded** — set `status: superseded` and `superseded-by: TM-NNN` when architecture refresh replaces a threat.
- **Link back** — ensure `THREAT-MODEL.md` index row stays in sync when severity or response changes.

## STRIDE quick reference

| Category | Question |
|----------|----------|
| Spoofing | Can an attacker pretend to be someone or something else? |
| Tampering | Can data be modified in transit or at rest without authorization? |
| Repudiation | Can actions be denied due to insufficient logging? |
| Information disclosure | Can data leak to unauthorized parties? |
| Denial of service | Can availability be degraded for legitimate users? |
| Elevation of privilege | Can a low-privilege actor gain higher privileges? |

Map each threat to **one primary** STRIDE category; note secondary categories in Summary if relevant.
