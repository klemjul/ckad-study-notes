# Application Observability and Maintenance

> Exam weight: 15%

## Core concepts and definitions

### Probes and health checks

Kubernetes supports three types of probes on containers:

| Probe | Purpose |
|---|---|
| `livenessProbe` | Restarts the container if it fails |
| `readinessProbe` | Removes the Pod from Service endpoints until it passes |
| `startupProbe` | Delays liveness/readiness probes during slow startup |

Probe mechanisms:
- `exec` — run a command inside the container
- `httpGet` — send an HTTP GET request
- `tcpSocket` — attempt a TCP connection
- `grpc` — gRPC health check

### Monitoring and logging

- `kubectl logs <pod>` — stream container logs
- `kubectl logs <pod> -c <container>` — logs from a specific container in a multi-container Pod
- `kubectl logs <pod> --previous` — logs from a previous (crashed) container
- `kubectl top pod` / `kubectl top node` — resource usage (requires Metrics Server)
- `kubectl describe` — detailed event and state information

### Debugging

- `kubectl exec -it <pod> -- sh` — open an interactive shell in a running container
- `kubectl debug` — create a debug container or copy a Pod for debugging
- `kubectl get events --sort-by=.lastTimestamp` — inspect cluster events
- Ephemeral containers can be added to a running Pod without restarting it for debugging.

### API deprecations

- Kubernetes deprecates API versions over time following the [deprecation policy](https://kubernetes.io/docs/reference/using-api/deprecation-policy/).
- Use `kubectl api-versions` to see available API versions in the current cluster.
- Use `kubectl convert` (via the `kubectl-convert` plugin) to migrate manifests to newer API versions.
- Check the Kubernetes release notes when upgrading to identify removed or deprecated APIs.

## Key commands / examples

```bash
# View logs for a Pod
kubectl logs my-pod

# Follow logs in real time
kubectl logs -f my-pod

# Get logs from a crashed container
kubectl logs my-pod --previous

# Open a shell in a running Pod
kubectl exec -it my-pod -- /bin/sh

# Check resource usage
kubectl top pods
kubectl top nodes

# Describe a Pod for events and status
kubectl describe pod my-pod

# List all cluster events sorted by time
kubectl get events --sort-by=.lastTimestamp
```

Liveness probe example:
```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 10
```

## Exam-focused reminders

- Know how to configure `livenessProbe`, `readinessProbe`, and `startupProbe`.
- Be familiar with all probe mechanisms (`exec`, `httpGet`, `tcpSocket`).
- Know how to retrieve logs and how to inspect crashed containers.
- Understand how to use `kubectl exec` and `kubectl debug` to troubleshoot.
- Be aware of API deprecation policy and how to detect deprecated API usage.
