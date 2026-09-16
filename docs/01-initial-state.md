# Initial State Assessment

The existing cluster `aks-hmezouar-aks-eval` in `hmezouarRG` was inspected from WSL before hardening.

| Check | Initial observation |
|---|---|
| Power / provisioning | Running / Succeeded |
| Node | Ready |
| Authentication | Local accounts available; no managed Entra profile |
| Kubernetes RBAC | Enabled |
| API endpoint | Public, with no authorized IP range configured |
| Container Insights | No omsagent add-on |
| Managed Prometheus | Disabled |
| AKS diagnostic settings | None |
| Workloads | All listed Pods Running and ready |
| PostgreSQL PVC | Bound, 16Gi |
| prod / quotas / LimitRanges | Absent |

The baseline was assessed before implementing the access controls.

Argo CD, PostgreSQL, backup components, OIDC, Workload Identity and Key Vault integration were already present and were preserved. Kubernetes metrics-server is distinct from Azure Monitor and remains installed.

The inventory does not establish that every subscription user had Kubernetes access; the local credential used during the initial check had administrator permissions.
