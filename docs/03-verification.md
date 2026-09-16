# Verification Results

Verification results for the configured access controls and resource policies.

| Test | Observed result | Status |
|---|---|---|
| Initial configuration | Matched the requested local/public baseline | Passed |
| Admin group membership | Hajar only | Passed |
| Reader group | Steve THEVAL added; reader and kubeconfig roles assigned at AKS scope | Configured |
| IP configuration | 86.201.70.133/32 | Applied |
| Access from current allowed IP | Entra-authenticated Kubernetes calls succeeded | Passed |
| Different IP | Not tested | Pending |
| Managed Entra / Azure RBAC | Both enabled | Applied |
| Administrator session | Ready node listed; namespace creation permission returned yes | Passed |
| Local accounts | disableLocalAccounts=true | Applied |
| Local admin credential retrieval | BadRequest explicitly stating static credentials are disallowed | Passed |
| Entra access after local accounts disabled | Node read and administrator permission checks succeeded | Passed |
| Existing Pods | All listed Pods Running and ready after changes | Passed |
| prod policies | Namespace, LimitRange and ResourceQuota created | Applied |
| Default resource injection | 100m/128Mi requests, 500m/256Mi limits | Passed |
| Per-container maximum | 2 CPU limit rejected against maximum 1 CPU | Passed |
| Namespace quota | Total limits.cpu=5 rejected against budget 4 | Passed |
| Admission test persistence | No Pods in prod after server-side dry runs | Passed |
| Reader write/Secret rejection | Separate reader-session test not executed | Pending |
| Non-member rejection | Separate identity unavailable | Pending |
| Historical certificate invalidation | No rotation performed | Pending assessment |
| Workload Identity | OIDC and Workload Identity enabled; application authentication flow documented | Documented |

The successful administrator session also has powerful Azure management roles. Do not interpret this single-session test as proof that only the two groups can access the cluster. Audit inherited roles and existing bindings, then test separate identities.

## Supporting Evidence

See [Screenshots](../screenshots/README.md) for configuration and verification images.
