# Access Hardening

## Applied Configuration

- API authorized range: `86.201.70.133/32`.
- This IPv4 is the observed outbound address of the Simplon site connection. Static allocation has not been verified; an address change requires an authorized-range update.
- Managed Microsoft Entra authentication enabled.
- Azure RBAC for the Kubernetes API enabled.
- Administrator group: `aks-security-admins` (`9b959e6f-cb54-4d47-bf68-aee1f4cbcdcb`). Membership verified as Hajar MEZOUAR only.
- Reader group: `aks-security-readers` (`da537464-935f-4322-9c2d-8fb5bd984ea8`). Membership verified: Steve THEVAL (`stheval@simplonformations.co`).

The designated reader account resolves to Entra object ID `e7cbea33-8e5d-4a20-9823-d1281d600761`. Membership was added and verified through Microsoft Graph via Azure CLI. No direct role assignment to the individual account was added by this project.

| Group | Roles at the AKS resource scope |
|---|---|
| Administrators | Azure Kubernetes Service Cluster User Role; Azure Kubernetes Service RBAC Cluster Admin |
| Readers | Azure Kubernetes Service Cluster User Role; Azure Kubernetes Service RBAC Reader |

Cluster User permits retrieval of the user kubeconfig. The RBAC roles authorize Kubernetes operations. The reader role does not grant access to Secrets.

## Verification and Remaining Work

The Entra login successfully listed the Ready node, returned `yes` for namespace creation permission, and listed all existing Pods as Running and ready. These checks use the administrator account, which also holds broad Azure management permissions; they do not independently prove that group membership is the only access path.

Pending checks:

- Reader membership is configured. Verify effective permissions using the designated account.
- Test read-only behavior using a separate session authenticated as the designated reader.
- Test an account outside both groups from the allowed network.
- Test the same authorized identity from a different outbound IP.
- Review inherited IAM permissions, Kubernetes bindings and historical certificate consumers.
- Record screenshots of the applied configuration and tests.

See `03-verification.md` for final observed results, including local-account status.

## Recovery

Correct a mistaken IP range through Azure Resource Manager using an authorized account. Do not automatically open the API to all addresses. Entra integration cannot simply be disabled to restore the original cluster. Repair group IDs and role assignments using Azure management access if necessary.

Disabling local accounts prevents new local credential retrieval. Previously issued certificates require a separate rotation assessment. Certificate rotation has not been performed because this reused cluster has existing consumers; it may be disruptive.

References: [AKS authorization](https://learn.microsoft.com/en-us/azure/aks/entra-id-authorization), [IP filtering](https://learn.microsoft.com/en-us/azure/aks/api-server-authorized-ip-ranges), [local accounts](https://learn.microsoft.com/en-us/azure/aks/local-accounts), [kubelogin installation](https://azure.github.io/kubelogin/install.html).
