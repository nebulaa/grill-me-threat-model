# Hardened operator profile (local lab)

This directory documents **operator safety** controls that coexist with the intentionally vulnerable default manifests. Use for workshops on shared laptops or security-conscious local setup.

## Principles

1. **Dedicated cluster only** — KIND cluster `kubernetes-goat-cluster`; separate `KUBECONFIG`.
2. **Never apply setup to shared contexts** — verify `kubectl config current-context` before install.
3. **Localhost-only access** — port-forward `127.0.0.1`, not `0.0.0.0`.
4. **Disposable environment** — VM or dedicated machine; no sensitive host data.
5. **Pin images** — prefer `image@sha256:...` or build from `infrastructure/`.

## Checklist

- [ ] Create KIND cluster via `platforms/kind-setup/setup-kind-cluster-and-goat.sh`
- [ ] Confirm context: `kubernetes-goat-cluster`
- [ ] Run `access-kubernetes-goat.sh` (after TM-004 fix: localhost bind only)
- [ ] Run `teardown-kubernetes-goat.sh` when finished
- [ ] Do not use `--insecure` unless required and understood

## Pedagogy vs safety

| Control | Default lab (teaching) | Hardened operator profile |
|---------|------------------------|---------------------------|
| Privileged / hostPath pods | Enabled | Run only in VM; optional reduced manifests |
| superadmin cluster-admin | Installed | Optional `--with-superadmin` (proposed) |
| SSRF proxy | Vulnerable | NetworkPolicy optional |
| NodePort 30003 | Exposed | ClusterIP optional |
| Redis AUTH | None | `requirepass` optional |
| Image tags | `:latest` | Digest-pinned |

Manifest overrides may be added here in future PRs; threat entries reference this profile under **Recommended actions** (not **Existing controls**) until verified in code and `system-model.md`.

Threat file format: `.cursor/skills/grill-threat-model/THREAT-ENTRY-FORMAT.md` — separates `response` (decision) from `mitigation-state` (verified implementation).
