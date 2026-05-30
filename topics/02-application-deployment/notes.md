# Application Deployment

> Exam weight: 20%

## Core concepts and definitions

### Deployments and rolling updates

- A `Deployment` manages a `ReplicaSet` and ensures the desired number of Pod replicas are running.
- Rolling updates replace Pods gradually to ensure zero downtime.
- Key fields:
  - `spec.strategy.type`: `RollingUpdate` (default) or `Recreate`
  - `spec.strategy.rollingUpdate.maxUnavailable`: max Pods unavailable during update
  - `spec.strategy.rollingUpdate.maxSurge`: max extra Pods during update

### Deployment strategies

| Strategy | Description |
|---|---|
| Rolling update | Gradually replaces old Pods with new ones (default) |
| Recreate | Terminates all old Pods before starting new ones (causes downtime) |
| Blue/Green | Two identical environments; traffic is switched at once |
| Canary | Incrementally shifts traffic to a new version |

Blue/green and canary deployments are implemented in Kubernetes using multiple Deployments and Service label selectors or an Ingress controller.

### Helm

- Helm is the Kubernetes package manager.
- A **chart** is a Helm package containing all resource templates.
- Key commands:
  - `helm repo add` / `helm repo update` — manage chart repositories
  - `helm install` / `helm upgrade` — deploy or update a release
  - `helm rollback` — revert to a previous release
  - `helm uninstall` — remove a release
  - `helm list` — list installed releases

### Kustomize

- Kustomize allows customizing Kubernetes manifests without templating.
- Uses a `kustomization.yaml` file to define patches, overlays, and transformations.
- Built into `kubectl` (`kubectl apply -k ./overlays/prod`).
- Common use cases: environment-specific config (dev/staging/prod), adding labels, changing image tags.

## Key commands / examples

```bash
# Perform a rolling update by changing the image
kubectl set image deployment/my-deploy my-container=myapp:2.0

# Check rollout status
kubectl rollout status deployment/my-deploy

# Roll back to the previous revision
kubectl rollout undo deployment/my-deploy

# Roll back to a specific revision
kubectl rollout undo deployment/my-deploy --to-revision=2

# View rollout history
kubectl rollout history deployment/my-deploy

# Scale a deployment
kubectl scale deployment/my-deploy --replicas=5

# Install a Helm chart
helm install my-release bitnami/nginx

# Apply Kustomize overlays
kubectl apply -k ./overlays/prod
```

## Exam-focused reminders

- Know how to trigger a rolling update and how to roll back.
- Understand the `maxUnavailable` and `maxSurge` parameters.
- Be able to implement a basic canary or blue/green strategy using labels and selectors.
- Know the essential Helm commands (install, upgrade, rollback, list).
- Understand how `kustomization.yaml` works and how to apply it with `kubectl apply -k`.
