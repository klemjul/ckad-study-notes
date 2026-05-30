# Services and Networking

> Exam weight: 20%

## Core concepts and definitions

### Services

A `Service` provides a stable network endpoint for a set of Pods selected by labels.

| Service type | Description |
|---|---|
| `ClusterIP` | Default; accessible only within the cluster |
| `NodePort` | Exposes the service on each node's IP at a static port |
| `LoadBalancer` | Provisions an external load balancer (cloud provider) |
| `ExternalName` | Maps the service to an external DNS name |

- Services use label selectors to route traffic to matching Pods.
- DNS format: `<service>.<namespace>.svc.cluster.local`

### Ingress

- An `Ingress` resource routes external HTTP/HTTPS traffic to Services based on rules (host, path).
- Requires an **Ingress controller** to be installed (e.g. nginx, Traefik, HAProxy).
- Supports TLS termination via a Secret containing a certificate.

```yaml
# Example Ingress rule
spec:
  rules:
    - host: myapp.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: my-service
                port:
                  number: 80
```

### Network Policies

- A `NetworkPolicy` resource controls traffic flow between Pods and namespaces.
- By default, all traffic is allowed between Pods in a cluster.
- Once a `NetworkPolicy` selects a Pod, all traffic not explicitly allowed is denied.
- Requires a CNI plugin that supports `NetworkPolicy` (e.g. Calico, Cilium).

Policy types:
- `Ingress` — controls incoming traffic to selected Pods
- `Egress` — controls outgoing traffic from selected Pods

Selectors used in policies:
- `podSelector` — match Pods by labels within the same namespace
- `namespaceSelector` — match Pods from specific namespaces
- `ipBlock` — match traffic from/to a CIDR range

## Key commands / examples

```bash
# Expose a Deployment as a ClusterIP Service
kubectl expose deployment my-deploy --port=80 --target-port=8080

# Expose a Deployment as a NodePort
kubectl expose deployment my-deploy --type=NodePort --port=80

# Get service details
kubectl get svc my-service -o wide

# Check DNS resolution from inside a Pod
kubectl exec -it my-pod -- nslookup my-service.default.svc.cluster.local

# Apply a NetworkPolicy
kubectl apply -f network-policy.yaml
```

NetworkPolicy example (deny all ingress, allow from specific namespace):
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-from-frontend
  namespace: backend
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes:
    - Ingress
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              name: frontend
```

## Exam-focused reminders

- Know the four Service types and when to use each.
- Understand how DNS works for Services within a cluster.
- Be able to create an Ingress rule with path-based and host-based routing.
- Know how to write a `NetworkPolicy` to restrict ingress/egress for a set of Pods.
- Remember: a NetworkPolicy requires a compatible CNI plugin to be enforced.
