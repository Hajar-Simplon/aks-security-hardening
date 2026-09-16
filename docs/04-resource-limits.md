# Resource Limits in prod

`manifests/prod-resources.yaml` defines the namespace and two complementary controls:

| Control | Purpose | Values |
|---|---|---|
| LimitRange | Default requests | 100m CPU, 128Mi memory per container |
| LimitRange | Default limits | 500m CPU, 256Mi memory per container |
| LimitRange | Maximum limits | 1 CPU, 512Mi memory per container |
| ResourceQuota | Namespace request budget | 2 CPUs, 2Gi memory |
| ResourceQuota | Namespace limit budget | 4 CPUs, 4Gi memory |
| ResourceQuota | Pod count | 10 |

These are demonstration values. A quota does not reserve node capacity or resize existing workloads. The limit defaults apply to newly admitted containers that omit the relevant values.

Expected admission results:

1. The defaults test succeeds and returns injected resource values.
2. The 2-CPU container exceeds the 1-CPU per-container maximum and is rejected.
3. Five containers with a 1-CPU limit each exceed the namespace limit budget of 4 CPUs and are rejected by the quota. Each container individually satisfies the LimitRange.

Server-side dry runs exercise admission without creating the test Pods. Refer to `03-verification.md` for actual execution status.

References: [ResourceQuota](https://kubernetes.io/docs/concepts/policy/resource-quotas/), [LimitRange](https://kubernetes.io/docs/concepts/policy/limit-range/).
