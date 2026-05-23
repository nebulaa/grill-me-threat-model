# Threat Entry Format

Each threat is one markdown file: `threat-model/threats/TM-NNN-short-slug.md`.

## Decision vs implementation

| Concept | Field / section | Describes |
|---------|-----------------|-----------|
| **Response decision** | `response` frontmatter | What the team plans: avoid, mitigate, accept, transfer |
| **Workflow** | `status` frontmatter | Review progress — **not** whether controls exist |
| **Implementation** | `mitigation-state` frontmatter | What is **verified today** in code and architecture |
| **As-is** | `## Existing controls` | Controls already present (with evidence) |
| **To-do** | `## Recommended actions` | Steps agreed but **not yet** implemented |

**Never** infer `mitigation-state` from `response` or from Recommended actions.

## Template

```markdown
---
id: TM-001
title: Short descriptive title
severity: critical | high | medium | low | informational
stride: Spoofing | Tampering | Repudiation | Information disclosure | Denial of service | Elevation of privilege
status: draft | reviewed | response-decided | closed | superseded
response: avoid | mitigate | accept | transfer | unset
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

- {Vector 1}
- {Vector 2}

## Framework mapping (optional)

| Framework | ID | Name |
|-----------|-----|------|
| MITRE ATT&CK | Txxxx | {technique} |
| CAPEC | CAPEC-xxx | {pattern} |
| CWE | CWE-xxx | {weakness} |

## Risk scoring (optional)

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

**User decision:** {What the user chose in walkthrough; date.}

## Existing controls

What is **already implemented** — verified in code, config, or architecture docs.

| Control | Addresses threat? | Evidence |
|---------|-------------------|----------|
| {e.g. TLS on public API} | partial / full / no | `{path:line}` or arch doc section |

If none: _No verified controls._

**Rules:** Every row needs a file path, manifest name, or `system-model.md` reference. Do not list planned or ticket-only work here.

## Recommended actions

What **should** be done — from agent walkthrough and/or user agreement. Not implemented until promoted to Existing controls.

| # | Action | Owner | Tracking |
|---|--------|-------|----------|
| 1 | {Concrete step — file or component to change} | {optional} | {ticket or "none"} |

### Mitigation references (optional)

- D3FEND: {technique ID or URL}
- OWASP: {cheat sheet name + URL}
- Microsoft category: {e.g. Authentication}

## Implementation verification

Fill when claiming progress toward `fully-mitigated`:

| Check | Done | Evidence |
|-------|------|----------|
| Code / config implements control | ☐ | |
| `system-model.md` (or ADR) updated | ☐ | |
| Re-read paths after change | ☐ | |

## Residual risk

{What remains given **Existing controls** only — required for accept or partial.}

## Walkthrough log

| Date | Action |
|------|--------|
| YYYY-MM-DD | Created; mitigation-state: non-mitigated |
| YYYY-MM-DD | User chose mitigate → response-decided; actions added to Recommended |
| YYYY-MM-DD | Control merged; system-model updated → fully-mitigated, status: closed |
```

## `status` values

| Status | When |
|--------|------|
| `draft` | Enumerated, not walkthrough-reviewed |
| `reviewed` | Discussed; response may still be `unset` |
| `response-decided` | `response` set; implementation may still be open |
| `closed` | Threat addressed per `response` **and** promotion gate met, **or** `accept` with documented residual risk |
| `superseded` | Replaced by architecture refresh |

Do **not** use `status: mitigated` — implementation belongs in `mitigation-state`.

## `mitigation-state` rules

| State | Set when |
|-------|----------|
| `non-mitigated` | Default at creation; no effective control |
| `partially-mitigated` | Existing control reduces but does not eliminate the scenario |
| `fully-mitigated` | Promotion gate passed (code + architecture + re-verify) |

**On walkthrough when user chooses mitigate:** set `response: mitigate`, `status: response-decided`, keep `mitigation-state` at current verified level (usually `non-mitigated`).

**After implementation:** move rows from Recommended actions → Existing controls, update `system-model.md`, then set `mitigation-state` and optionally `status: closed`.

## Rules

- **One threat per file** — do not combine unrelated STRIDE categories unless they share a single exploit chain.
- **Severity is stable** unless new evidence changes impact; document changes in walkthrough log.
- **Superseded** — set `status: superseded` and `superseded-by: TM-NNN` when architecture refresh replaces a threat.
- **Link back** — keep `THREAT-MODEL.md` index in sync: **Response** = decision, **Implementation** = `mitigation-state`.

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
