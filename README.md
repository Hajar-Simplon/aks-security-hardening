# Azure Kubernetes Service Security Hardening

This project documents the security hardening of an existing **Azure Kubernetes Service (AKS)** cluster. It covers network access restrictions for the Kubernetes API, user authentication through **Microsoft Entra ID**, group-based authorization, resource limits, and application authentication without static credentials.

The project uses an existing test environment in a training center subscription. Changes are introduced incrementally, with documented prerequisites, expected effects, verification steps, and recovery procedures.

## Purpose and Objectives

The scenario starts with a publicly accessible cluster whose administrative access needs to be controlled, and an application that uses a static key to access Azure. The objective is to establish explicit access permissions, reduce unnecessary privileges, and demonstrate the outcome of each change.

The main objectives are to:

- Verify the initial cluster configuration and obtain trainer approval before applying security changes.
- Restrict access to the Kubernetes API to the training center's public outbound IP address.
- Enable Microsoft Entra ID authentication and Azure RBAC authorization for Kubernetes.
- Separate human access into administrator and reader groups.
- Test permitted and denied operations, and close historical local-account access paths.
- Apply `ResourceQuota` and `LimitRange` policies in the `prod` namespace.
- Explain how **Microsoft Entra Workload ID** replaces static application credentials.
- Document the change sequence, operational impact, verification results, and recovery options.

## Environment

| Component | Configuration |
|---|---|
| Platform | Microsoft Azure, training center subscription |
| Resource group | `hmezouarRG` |
| Existing cluster | `aks-hmezouar-aks-eval` |
| Region | France Central |
| Observed node pool | One `Standard_D2_v3` node |
| Working environment | Bash in WSL, launched from PowerShell |
| Tools | Azure CLI, kubectl, Git; kubelogin for the Entra ID phase |

The cluster already hosts Argo CD, PostgreSQL, and backup components. Changes must account for these dependencies and preserve existing resources, services, and data.

## Project Status

### Initial Configuration Verified

Checks performed from WSL confirmed the following baseline:

| Check | Observed result |
|---|---|
| Cluster state | `Running`, provisioning successful |
| Kubernetes node | `Ready` |
| Authentication | Local accounts available; Entra ID integration not enabled |
| Kubernetes RBAC | Enabled |
| Kubernetes API | Public, with no authorized IP range restrictions |
| Azure cluster monitoring | Container Insights absent, Managed Prometheus disabled, no AKS diagnostic settings |
| Existing workloads | All listed Pods were `Running` with their containers ready |
| PostgreSQL storage | Persistent volume claim `Bound` |
| `prod` namespace | Absent at the time of verification |
| Resource quotas and LimitRanges | None returned by the inventory |

OIDC and Workload Identity are already enabled at cluster level. Application identity configuration and permissions still need to be reviewed for this scenario.

These results describe the environment at the time of verification. They do not constitute full functional testing of applications or backups. Verification outputs are stored locally; submission evidence still needs to be added to this repository.

### Next Steps

1. Obtain trainer approval of the initial configuration and add the supporting screenshots.
2. Prepare Entra ID groups, role assignments, and the authorized public IP address.
3. Apply network filtering, Entra ID authentication, and authorization changes incrementally.
4. Verify administrator, reader, and non-member access.
5. Configure and test resource limits in `prod`.
6. Document application authentication with Workload Identity.
7. Complete the change plan and execution evidence.

**Security hardening has not yet been applied as part of this project. Trainer approval of the initial configuration is pending.**

## Target Access Model

| Identity | Intended access |
|---|---|
| Administrator group | Kubernetes cluster administration |
| Reader group | Read permitted resources, without modification or access to Secrets |
| Users outside these groups | No operational access to Kubernetes resources granted by this project |
| Application | A suitable dedicated or existing Azure identity, with permissions limited to its requirements |

Human access to the API must also satisfy the IP restriction. Existing technical identities and inherited Azure permissions must be reviewed: access to the subscription alone does not determine effective Kubernetes permissions.

## Scope

### In Scope

- Security configuration of the existing AKS cluster.
- Authentication, authorization, and access tests.
- Kubernetes resource governance in `prod`.
- Application authentication without static keys.
- Documentation of changes, impacts, and observed results.

### Out of Scope

- Infrastructure provisioning with Terraform, Bicep, or other infrastructure-as-code tools.
- Building a CI/CD pipeline.
- Redesigning or deleting existing applications.
- Implementing a complete highly available production architecture.

Optional extensions include a group and role diagram and a script that guides the operator through the changes step by step.

## Planned Deliverables

- Initial-state assessment and target configuration documentation.
- A change procedure with intermediate checks and recovery measures.
- Kubernetes manifests for resource limits.
- An access test matrix with observed results.
- Screenshots of applied configurations and executed tests.
- An explanation of Workload Identity and its configuration.

Only verified outcomes will be presented as completed. Personal study and oral-exam preparation notes are maintained separately from this repository.

## Sensitive Information

Kubeconfig files, tokens, private keys, and files containing secrets must not be committed. Evidence must be reviewed before publication to avoid exposing authentication credentials. The `.gitignore` file excludes local `kubeconfig*` files.

## References

- [Microsoft Entra ID authentication for AKS](https://learn.microsoft.com/en-us/azure/aks/entra-id-control-plane-authentication)
- [Azure RBAC authorization for the Kubernetes API](https://learn.microsoft.com/en-us/azure/aks/entra-id-authorization)
- [API server authorized IP ranges](https://learn.microsoft.com/en-us/azure/aks/api-server-authorized-ip-ranges)
- [Kubernetes ResourceQuota](https://kubernetes.io/docs/concepts/policy/resource-quotas/)
- [Kubernetes LimitRange](https://kubernetes.io/docs/concepts/policy/limit-range/)
- [Microsoft Entra Workload ID on AKS](https://learn.microsoft.com/en-us/azure/aks/workload-identity-overview)
