# Status Poller Helm Chart

[![Helm 3](https://img.shields.io/badge/Helm-3.0+-0f1689?logo=helm&logoColor=white)](https://helm.sh/)
[![Kubernetes 1.19+](https://img.shields.io/badge/Kubernetes-1.19+-326ce5?logo=kubernetes&logoColor=white)](https://kubernetes.io/)

A Helm chart for deploying the Lobsterpod Status Poller to Kubernetes.

---

## Quick Start

```bash
helm repo add status-poller https://the-saas-shop.github.io/lobsterpod-status-poller-helm
helm repo update
helm install poller status-poller/status-poller \
  --set secret.SOME_API_KEY=xxx
```

---

## Installation

### Prerequisites

- Kubernetes 1.19+
- Helm 3.0+

### From Helm Repository (Recommended)

```bash
# Add the repository
helm repo add status-poller https://the-saas-shop.github.io/lobsterpod-status-poller-helm
helm repo update

# Install the chart
helm install poller status-poller/status-poller \
  --set secret.SOME_API_KEY=xxx
```

<details>
<summary><b>Install from Local Clone (Development)</b></summary>

```bash
git clone https://github.com/the-saas-shop/lobsterpod-status-poller-helm.git
cd lobsterpod-status-poller-helm
helm install poller ./charts/status-poller \
  --set secret.SOME_API_KEY=xxx
```

</details>

<details>
<summary><b>Using an Existing Secret</b></summary>

Create a secret with your keys:

```bash
kubectl create secret generic poller-credentials \
  --from-literal=SOME_API_KEY=xxx \
  --from-literal=DATABASE_URL=postgres://...
```

Then install the chart:

```bash
helm install poller status-poller/status-poller \
  --set existingSecret=poller-credentials
```

</details>

<details>
<summary><b>With Custom Values File</b></summary>

```bash
helm install poller status-poller/status-poller -f my-values.yaml
```

</details>

---

## Configuration

| Parameter | Description | Default |
|---|---|---|
| `image.repository` | Container image repository | `""` |
| `image.tag` | Container image tag | `""` |
| `image.pullPolicy` | Image pull policy | `IfNotPresent` |
| `imagePullSecrets` | Image pull secrets | `[]` |
| `replicaCount` | Number of replicas | `1` |
| `existingSecret` | Name of an existing secret to use | `""` |
| `secret` | Secret key-value pairs (creates a K8s Secret) | `{}` |
| `env` | Plain (non-secret) environment variables | `{}` |
| `serviceAccount.create` | Create a service account | `true` |
| `serviceAccount.annotations` | Service account annotations | `{}` |
| `serviceAccount.name` | Service account name override | `""` |
| `podAnnotations` | Pod annotations | `{}` |
| `podLabels` | Additional pod labels | `{}` |
| `podSecurityContext` | Pod security context | *non-root, UID 1000* |
| `securityContext` | Container security context | *read-only rootfs, drop all caps* |
| `resources.requests.cpu` | CPU request | `50m` |
| `resources.requests.memory` | Memory request | `64Mi` |
| `resources.limits.cpu` | CPU limit | `200m` |
| `resources.limits.memory` | Memory limit | `128Mi` |
| `nodeSelector` | Node selector | `{}` |
| `tolerations` | Tolerations | `[]` |
| `affinity` | Affinity rules | `{}` |

See [values.yaml](charts/status-poller/values.yaml) for all available configuration options.

### Secrets

Secrets can be provided in two ways:

**1. Inline via `secret`** — each key becomes both a Kubernetes Secret entry and an environment variable on the container:

```yaml
secret:
  SOME_API_KEY: "my-key"
  DATABASE_URL: "postgres://user:pass@host/db"
```

**2. Existing Secret via `existingSecret`** — reference a pre-created Kubernetes Secret:

```yaml
existingSecret: "my-existing-secret"
```

> **Note:** When using `existingSecret`, the chart does not create a Secret resource. Your existing secret must contain the keys you need as environment variables.

---

## Security

The chart follows security best practices:

- All containers run as non-root (UID 1000)
- All capabilities are dropped
- Read-only root filesystem
- Seccomp profile enabled (RuntimeDefault)

---

## Uninstallation

```bash
helm uninstall poller
```

---

## Troubleshooting

<details>
<summary><b>Debug Commands</b></summary>

### Check pod status

```bash
kubectl get pods -l app.kubernetes.io/name=status-poller
```

### View logs

```bash
kubectl logs -l app.kubernetes.io/name=status-poller -f
```

### Describe the deployment

```bash
kubectl describe deployment -l app.kubernetes.io/name=status-poller
```

</details>
