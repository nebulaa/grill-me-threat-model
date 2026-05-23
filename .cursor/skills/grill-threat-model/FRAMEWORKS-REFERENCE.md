# Frameworks Reference

Read when enumerating threats, scoring risk, or drafting mitigations. Not required for every threat.

## Adam Shostack — Four questions

1. What are we working on?
2. What can go wrong?
3. What are we going to do about it?
4. Did we do a good job?

Maps to phases in SKILL.md (system model → threats → walkthrough → summary).

## STRIDE

| Letter | Threat | Example |
|--------|--------|---------|
| S | Spoofing | Stolen session cookie |
| T | Tampering | Modified request body |
| R | Repudiation | Missing audit log |
| I | Information disclosure | IDOR on `/users/{id}` |
| D | Denial of service | Unbounded expensive query |
| E | Elevation of privilege | IDOR granting admin role |

## DREAD (optional ranking)

| Factor | Question |
|--------|----------|
| Damage | How bad? |
| Reproducibility | How repeatable? |
| Exploitability | How much effort? |
| Affected users | How many impacted? |
| Discoverability | How easy to find? |

## VAST (awareness)

- **Application threat models** — process/architecture view (this skill’s default)
- **Operational threat models** — attacker-centric DFD for infrastructure

Use operational modeling when the session focuses on cloud/network ops rather than app code.

## PASTA (awareness)

Risk-centric, seven-stage methodology (GitLab-style). This skill compresses PASTA into: scope → technical model → threat ID → risk treatment → validation.

## Enumeration sources

| Source | Use for |
|--------|---------|
| [MITRE ATT&CK](https://attack.mitre.org/) | TTPs, technique IDs |
| [CAPEC](https://capec.mitre.org/) | Attack patterns |
| [CWE](https://cwe.mitre.org/) | Software weaknesses |

## CVSS 4.0

Quantify vulnerabilities when stakeholders need comparable scores.

- Calculator: https://www.first.org/cvss/calculator/4.0#
- Metric groups: Base, Threat, Environmental, Supplemental

## Mitigation libraries

| Resource | URL |
|----------|-----|
| MITRE D3FEND | https://d3fend.mitre.org/ |
| OWASP Cheat Sheet Series | https://cheatsheetseries.owasp.org/index.html |
| Microsoft Threat Modeling mitigations | https://learn.microsoft.com/en-us/azure/security/develop/threat-modeling-tool-mitigations |

### Microsoft mitigation categories

Auditing and Logging · Authentication · Authorization · Communication Security · Configuration Management · Cryptography · Exception Management · Input Validation · Sensitive Data · Session Management

## Threat responses

| Response | When |
|----------|------|
| Avoid | Remove feature or exposure |
| Mitigate | Implement controls |
| Accept | Document residual risk |
| Transfer | Vendor, insurance, shared responsibility |

## Reporting categories

**Implementation** (`mitigation-state`) — verified in code and architecture only:

| State | Meaning |
|-------|---------|
| Non-mitigated | No effective control in repo today |
| Partially mitigated | Some control exists; threat still realistic |
| Fully mitigated | Control verified in code/config **and** documented in `system-model.md` |

**Response decision** (`response`) — separate from implementation. A threat can be `response: mitigate` with `mitigation-state: non-mitigated` while work is outstanding.

**Recommended actions** vs **Existing controls** — planned fixes never count toward Fully mitigated until promoted after the promotion gate (see SKILL.md).

Aggregate **implementation** counts in `THREAT-MODEL.md` Summary for stakeholder review. Track **response open** separately for engineering backlog.
