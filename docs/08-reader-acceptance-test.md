# Reader Access Acceptance Test

## Purpose

Verify effective read-only access using the designated reader account, independently of the administrator session.

## Conditions

- Sign in as `stheval@simplonformations.co`.
- Use the authorized network and a separate authenticated session.
- Confirm membership in `aks-security-readers` and the cluster-scoped reader roles.

## Acceptance Criteria

| Operation | Expected result |
|---|---|
| List Pods in prod | Allowed |
| Create a Deployment in prod | Denied |
| Read Secrets in prod | Denied |

Record the account and results in screenshot evidence. Group membership alone does not establish effective permissions.

## Result

Reader membership and role assignments are configured. Independent reader-session validation remains outstanding.
