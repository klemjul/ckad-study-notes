# Application Environment, Configuration and Security

> Exam weight: 25%

## Core concepts and definitions

### ConfigMaps

- A `ConfigMap` stores non-confidential key-value configuration data.
- Can be consumed as environment variables, command-line arguments, or volume files.
- Changes to a mounted ConfigMap volume are reflected in the Pod without restart (eventual consistency).

### Secrets

- A `Secret` stores sensitive data (passwords, tokens, keys) in base64-encoded form.
- Types: `Opaque` (default), `kubernetes.io/dockerconfigjson`, `kubernetes.io/tls`, etc.
- Consumed the same way as ConfigMaps (env vars or volume mounts).
- Secrets are not encrypted at rest by default — use etcd encryption or an external vault.

### Resource management

- `requests` — minimum resources guaranteed to the container.
- `limits` — maximum resources the container can use.
- `LimitRange` — enforces default/min/max resource constraints for a namespace.
- `ResourceQuota` — limits the total resource consumption in a namespace.

### ServiceAccounts

- Every Pod runs with a ServiceAccount (default: `default`).
- ServiceAccount tokens are mounted automatically and used to authenticate with the API server.
- Use RBAC `Role`/`ClusterRole` and `RoleBinding`/`ClusterRoleBinding` to grant permissions.

### Security Context

- Defines security settings at the Pod or container level.
- Common fields:
  - `runAsUser` / `runAsGroup` — set the UID/GID for the process
  - `runAsNonRoot` — prevent running as root
  - `readOnlyRootFilesystem` — mount the root filesystem as read-only
  - `allowPrivilegeEscalation` — control whether a process can gain more privileges
  - `capabilities.add` / `capabilities.drop` — Linux capability management

### Authentication, authorization, and admission control

- **Authentication**: verifies who is making the request (certificates, tokens, OIDC).
- **Authorization (RBAC)**: determines what the authenticated user can do.
- **Admission controllers**: validate or mutate requests before they are persisted.

### Extending Kubernetes: CRDs and Operators

- A **CustomResourceDefinition (CRD)** extends the Kubernetes API with new resource types.
- An **Operator** is a controller that manages a CRD and encodes operational knowledge.

## Key commands / examples

```bash
# Create a ConfigMap from literal values
kubectl create configmap my-config --from-literal=key=value

# Create a Secret from literal values
kubectl create secret generic my-secret --from-literal=******

# Check resource usage in a namespace
kubectl describe resourcequota -n my-namespace

# View ServiceAccount details
kubectl describe serviceaccount default

# Create a Role and bind it
kubectl create role pod-reader --verb=get,list,watch --resource=pods
kubectl create rolebinding read-pods --role=pod-reader --serviceaccount=default:my-sa
```

ConfigMap consumed as environment variable:
```yaml
env:
  - name: APP_COLOR
    valueFrom:
      configMapKeyRef:
        name: my-config
        key: color
```

Security context example:
```yaml
securityContext:
  runAsNonRoot: true
  runAsUser: 1000
  readOnlyRootFilesystem: true
  capabilities:
    drop:
      - ALL
```

## Exam-focused reminders

- Know how to create and consume ConfigMaps and Secrets (env vars and volume mounts).
- Understand resource `requests` and `limits` and the impact of `LimitRange`/`ResourceQuota`.
- Be able to create a ServiceAccount, Role, and RoleBinding.
- Know the key `securityContext` fields and when to use them.
- Understand the difference between CRDs and Operators at a conceptual level.
