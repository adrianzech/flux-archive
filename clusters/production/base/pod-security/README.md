# Pod Security Standards

Pod Security Standards are enforced via namespace labels in `namespaces.yaml`.

## Security Levels

### Privileged
Unrestricted policy for system-level workloads that require privileged access.
- `metallb-system`
- `csi-proxmox`

### Baseline
Minimally restrictive policy for common containerized workloads.
- `media`
- `auth`
- `traefik`
- `cert-manager`
- `cnpg-system`
- `checkmk-system`
- `flux-system`

### Restricted
Heavily restricted policy following current Pod hardening best practices.
- Applied as audit/warn mode for most namespaces
- Helps identify potential security improvements

## Labels Applied

Each namespace includes:
- `pod-security.kubernetes.io/enforce`: Active enforcement level
- `pod-security.kubernetes.io/audit`: Audit level (logs violations)
- `pod-security.kubernetes.io/warn`: Warning level (shows warnings)

## References
- [Kubernetes Pod Security Standards](https://kubernetes.io/docs/concepts/security/pod-security-standards/)
- [Pod Security Admission](https://kubernetes.io/docs/concepts/security/pod-security-admission/)
