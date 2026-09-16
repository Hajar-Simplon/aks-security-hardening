# Deployment Procedure

## Purpose

Reproduce the security configuration on an existing AKS cluster. Use the target environment's resource names, identities and public outbound address.

## Prerequisites

- An operational AKS cluster with Kubernetes RBAC enabled.
- Permission to update the cluster, manage the required Entra groups and assign roles.
- Identified administrator and reader accounts.
- A confirmed authorized public outbound IP address.
- An inventory of existing applications, permissions and persistent resources.

## Implementation Sequence

| Step | Action | Expected result |
|---|---|---|
| 1 | Inspect cluster health, authentication, networking and existing workloads | Baseline and dependencies established |
| 2 | Create or reuse administrator and reader security groups; verify membership | Explicit separation of access |
| 3 | Assign Cluster User to both groups, RBAC Cluster Admin to administrators and RBAC Reader to readers at AKS scope | Group-based permissions configured |
| 4 | Set the API authorized IP range to the approved public address | API access restricted to the approved network |
| 5 | Enable managed Entra authentication and Azure RBAC; verify administrator login | Identity-based administration works |
| 6 | Disable local accounts and verify static credential retrieval is denied | New local administrator credentials unavailable |
| 7 | Review prod and apply the resource policy manifest only where required | Namespace defaults and budgets configured |
| 8 | Test default injection and requests exceeding container and namespace limits | Valid requests accepted; excessive requests rejected |
| 9 | Validate reader, non-member and outside-network access separately | Effective permissions and network restrictions verified |
| 10 | Check existing workloads and record screenshot evidence | Operational impact assessed |

The reference deployment already contains prod, container-defaults and prod-budget. Reuse these objects when verifying the existing environment.

## Application Authentication

See [Workload Identity](05-workload-identity.md) for the secretless authentication flow and application configuration.

## Recovery

See [Change Plan](06-change-plan.md) for impacts and recovery options. Correct access configuration through an authorized management account. Preserve existing workloads and persistent data.
