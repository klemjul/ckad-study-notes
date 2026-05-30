# Application Design and Build

> Exam weight: 20%

## Core concepts and definitions

### Container images

- A container image is a read-only package that contains everything needed to run an application (code, runtime, libraries, config).
- Images are built from a `Dockerfile` using `docker build` or `podman build`.
- Images are stored in registries (Docker Hub, GCR, ECR, etc.).
- Use specific image tags (e.g. `nginx:1.27`) rather than `latest` to ensure reproducibility.

Key Dockerfile instructions:
- `FROM` — base image
- `COPY` / `ADD` — copy files into the image
- `RUN` — execute commands during build
- `CMD` / `ENTRYPOINT` — default command when container starts
- `ENV` — set environment variables
- `EXPOSE` — document the port the container listens on

### Workload resources

| Resource | Use case |
|---|---|
| `Deployment` | Stateless applications with rolling updates |
| `StatefulSet` | Stateful applications requiring stable network identity and storage |
| `DaemonSet` | Run one Pod per node (log collection, monitoring agents) |
| `Job` | Run a task to completion (batch workloads) |
| `CronJob` | Schedule a Job periodically (cron syntax) |

### Multi-container Pod design patterns

- **Sidecar**: a helper container that extends the main container (e.g. log shipper, proxy).
- **Init container**: runs to completion before the main container starts; used for setup tasks.
- **Adapter**: transforms output of the main container for external consumers.
- **Ambassador**: proxies network traffic to/from the main container.

### Volumes

- **Ephemeral volumes** (`emptyDir`, `configMap`, `secret`, `downwardAPI`): exist only for the lifetime of the Pod.
- **Persistent volumes** (PVC/PV): exist independently of the Pod lifecycle; backed by external storage.

## Key commands / examples

```bash
# Build and tag an image
docker build -t myapp:1.0 .

# Run a Pod with a specific image
kubectl run mypod --image=myapp:1.0

# Create a CronJob
kubectl create cronjob my-cron --image=busybox --schedule="*/5 * * * *" -- echo hello

# Check a Pod's volumes
kubectl describe pod <pod-name>
```

## Exam-focused reminders

- Know how to write a basic `Dockerfile` and build an image.
- Be able to choose the right workload resource (Deployment vs DaemonSet vs Job vs CronJob).
- Understand init containers and sidecar containers and when to use each.
- Know the difference between ephemeral and persistent storage.
- Practice creating PVC/PV manifests and mounting them in Pods.
