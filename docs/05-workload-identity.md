# Application Authentication with Workload Identity

## Purpose

Replace a long-lived application key stored in a Kubernetes Secret with federated authentication. Authentication still uses credentials in the form of temporary tokens; the application no longer needs a static Azure key.

## Diagram

![AKS Workload Identity authentication flow](diagrams/workload-identity.svg)

[Editable draw.io diagram](diagrams/workload-identity.drawio)

The diagram uses an illustrative application ServiceAccount. It separates token exchange (authentication) from resource permissions (authorization).

## Authentication Flow

1. A Pod runs under a specific Kubernetes ServiceAccount.
2. Kubernetes projects a short-lived, signed ServiceAccount token into the Pod.
3. A federated credential on an Azure identity trusts the exact issuer, subject and audience.
4. The Azure Identity library exchanges the projected token with Microsoft Entra ID.
5. The application calls the Azure API with the resulting access token.
6. Azure evaluates the identity's roles at the target resource scope.

OIDC and Workload Identity are enabled on the existing cluster. Applications use this capability through the federation and ServiceAccount configuration below.

## Configuration Checklist

- Identify the application's actual Azure operations and resources.
- Inspect the existing application identity before deciding whether to reuse it.
- Configure federation with subject `system:serviceaccount:prod:app-azure` and audience `api://AzureADTokenExchange`, using this cluster's exact OIDC issuer.
- Annotate the ServiceAccount with `azure.workload.identity/client-id`.
- Set `serviceAccountName: app-azure` and label `azure.workload.identity/use: "true"` on the Pod template.
- Use an Azure Identity library supporting Workload Identity.
- Assign only the required permissions on the target resources. Reader is sufficient for a read-only demonstration, but not for resource deployment.
- Verify an allowed operation and a denied operation outside the assigned scope.
- Remove the application's old Secret references and revoke the old key at its source after successful migration.

This section explains the application authentication mechanism and its configuration on AKS.

Reference: [Microsoft Entra Workload ID](https://learn.microsoft.com/en-us/azure/aks/workload-identity-overview).
