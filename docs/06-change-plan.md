# Change Plan and Operational Impact

## Scope

Reuse `aks-hmezouar-aks-eval` in `hmezouarRG`. Preserve existing workloads, volumes and Azure resources. Infrastructure provisioning and CI/CD are outside the project scope.

## Prerequisites

- Baseline configuration: assessed and recorded before implementation.
- Azure management and role-assignment permissions: inventoried before changes.
- Entra group creation permission: demonstrated by successful group creation.
- Public outbound IP: detected after confirmation that the workstation used Simplon's network.
- Working kubelogin: standalone v0.2.19 used to resolve Snap isolation from Azure CLI.
- Reader identity: Steve THEVAL, verified and added to the reader group. A separate non-member test account remains required.

## Applied Sequence

1. Save the initial AKS configuration and workload inventory.
2. Create the administrator and reader security groups. Add Hajar to administrators.
3. Assign the two groups their cluster-scoped kubeconfig and Kubernetes roles.
4. Apply the authorized IP range.
5. Enable managed Entra authentication using the administrator group.
6. Enable Azure RBAC for Kubernetes.
7. Retrieve a fresh user kubeconfig and verify Entra administrator access.
8. Disable local accounts; confirm new local admin credential retrieval fails and Entra access still works.
9. During initial implementation, verified that prod was absent and created it with its resource policies. The namespace now exists; subsequent checks reuse it.
10. Exercise admission with server-side dry runs and verify existing Pods remain ready.

## Impact and Recovery

| Change | Expected impact | Recovery approach |
|---|---|---|
| IP restriction | API clients outside the allowed address lose connectivity | Correct the CIDR through Azure management access |
| Entra integration | Human clients need refreshed kubeconfig and Entra authentication | Repair group or role configuration; Entra integration cannot simply be disabled |
| Azure RBAC | User actions depend on role assignments and scope | Correct the specific assignment; avoid broad subscription grants |
| Local accounts disabled | New static credentials cannot be retrieved | An authorized emergency procedure may temporarily re-enable them; document and close the exception |
| Certificate rotation | Could invalidate historical clients and affect the reused cluster | Assess consumers and schedule separately; not performed here |
| Namespace limits | Requests exceeding policies are rejected | Review requirements, then change only the affected policy values |

Do not delete `hmezouarRG`, the cluster's managed node resource group, or existing databases and backup resources as cleanup for this exercise.
