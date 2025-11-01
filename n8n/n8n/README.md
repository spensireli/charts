# n8n Helm Chart

[n8n](https://n8n.io) is a free and open workflow automation tool that allows you to connect any app with any other app and create powerful workflows.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.0+
- PersistentVolume provisioner support in the underlying infrastructure (if persistence is enabled)

## Installing the Chart

To install the chart with the release name `my-n8n`:

```bash
helm install my-n8n ./n8n
```

## Uninstalling the Chart

To uninstall/delete the `my-n8n` deployment:

```bash
helm delete my-n8n
```

## Configuration

The following table lists the configurable parameters of the n8n chart and their default values.

### General Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `replicaCount` | Number of n8n replicas | `1` |
| `image.repository` | n8n image repository | `n8nio/n8n` |
| `image.pullPolicy` | Image pull policy | `IfNotPresent` |
| `image.tag` | Image tag (overrides appVersion) | `""` |
| `imagePullSecrets` | Image pull secrets | `[]` |
| `nameOverride` | Override chart name | `""` |
| `fullnameOverride` | Override full name | `""` |

### Service Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `service.type` | Kubernetes service type | `ClusterIP` |
| `service.port` | Service port | `5678` |

### Ingress Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `ingress.enabled` | Enable ingress | `false` |
| `ingress.className` | Ingress class name | `""` |
| `ingress.annotations` | Ingress annotations | `{}` |
| `ingress.hosts` | Ingress hosts configuration | See values.yaml |
| `ingress.tls` | Ingress TLS configuration | `[]` |

### n8n Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `n8n.encryptionKey` | Encryption key for credentials (REQUIRED) | `""` |
| `n8n.webhookUrl` | Webhook URL for external access | `""` |
| `n8n.basicAuth.enabled` | Enable basic authentication | `false` |
| `n8n.basicAuth.user` | Basic auth username | `""` |
| `n8n.basicAuth.password` | Basic auth password | `""` |
| `n8n.timezone` | Timezone | `America/New_York` |
| `n8n.genericTimezone` | Generic timezone | `America/New_York` |

### Persistence Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `persistence.enabled` | Enable persistent storage | `true` |
| `persistence.storageClass` | Storage class | `""` |
| `persistence.accessMode` | Access mode | `ReadWriteOnce` |
| `persistence.size` | Storage size | `10Gi` |
| `persistence.existingClaim` | Use existing PVC | `""` |

### Resource Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `resources.limits.cpu` | CPU limit | `1000m` |
| `resources.limits.memory` | Memory limit | `1024Mi` |
| `resources.requests.cpu` | CPU request | `250m` |
| `resources.requests.memory` | Memory request | `512Mi` |

### Autoscaling Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `autoscaling.enabled` | Enable HPA | `false` |
| `autoscaling.minReplicas` | Minimum replicas | `1` |
| `autoscaling.maxReplicas` | Maximum replicas | `3` |
| `autoscaling.targetCPUUtilizationPercentage` | Target CPU % | `80` |

### PostgreSQL Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `postgresql.enabled` | Enable PostgreSQL | `false` |
| `postgresql.auth.username` | PostgreSQL username | `n8n` |
| `postgresql.auth.password` | PostgreSQL password | `n8n` |
| `postgresql.auth.database` | PostgreSQL database | `n8n` |

## Example Configurations

### Basic Installation

```bash
helm install my-n8n ./n8n \
  --set n8n.encryptionKey="$(openssl rand -base64 32)"
```

### With Ingress and TLS

Create a `values-prod.yaml`:

```yaml
n8n:
  encryptionKey: "your-secure-encryption-key"
  webhookUrl: "https://n8n.example.com/"
  basicAuth:
    enabled: true
    user: "admin"
    password: "secure-password"

ingress:
  enabled: true
  className: "nginx"
  annotations:
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
    nginx.ingress.kubernetes.io/proxy-body-size: "64m"
  hosts:
    - host: n8n.example.com
      paths:
        - path: /
          pathType: Prefix
  tls:
    - secretName: n8n-tls
      hosts:
        - n8n.example.com

persistence:
  enabled: true
  size: 20Gi
```

Then install:

```bash
helm install my-n8n ./n8n -f values-prod.yaml
```

### With PostgreSQL Database

```yaml
n8n:
  encryptionKey: "your-secure-encryption-key"

postgresql:
  enabled: true
  auth:
    username: n8n
    password: "secure-db-password"
    database: n8n
  primary:
    persistence:
      enabled: true
      size: 20Gi
```

### With External PostgreSQL

```yaml
n8n:
  encryptionKey: "your-secure-encryption-key"

postgresql:
  enabled: false

env:
  - name: DB_TYPE
    value: "postgresdb"
  - name: DB_POSTGRESDB_HOST
    value: "postgres.example.com"
  - name: DB_POSTGRESDB_PORT
    value: "5432"
  - name: DB_POSTGRESDB_DATABASE
    value: "n8n"
  - name: DB_POSTGRESDB_USER
    value: "n8n"
  - name: DB_POSTGRESDB_PASSWORD
    valueFrom:
      secretKeyRef:
        name: postgres-secret
        key: password
```

## Important Notes

### Encryption Key

The encryption key is **critical** for n8n to function properly. It's used to encrypt credentials and sensitive data.

**Generate a secure key:**

```bash
openssl rand -base64 32
```

**Never change this key after installation**, as it will make your existing encrypted credentials unreadable.

### Persistence

It's **strongly recommended** to enable persistence. Without it, you'll lose all workflows, credentials, and execution history when the pod restarts.

### Webhooks

If you plan to use webhooks, you must set `n8n.webhookUrl` to the external URL where n8n is accessible:

```yaml
n8n:
  webhookUrl: "https://n8n.example.com/"
```

### Security

For production deployments:

1. Enable basic authentication or use an external authentication proxy
2. Use HTTPS/TLS with ingress
3. Set a strong encryption key
4. Consider enabling PostgreSQL for better performance
5. Set appropriate resource limits

## Troubleshooting

### Check Pod Status

```bash
kubectl get pods -l app.kubernetes.io/name=n8n
```

### View Logs

```bash
kubectl logs -l app.kubernetes.io/name=n8n -f
```

### Access n8n Locally

```bash
kubectl port-forward svc/my-n8n 5678:5678
```

Then open http://localhost:5678

### Common Issues

**Pod stuck in Pending:** Check if PVC is bound:
```bash
kubectl get pvc
```

**Pod CrashLoopBackOff:** Check logs for configuration errors:
```bash
kubectl logs <pod-name>
```

## More Information

- [n8n Documentation](https://docs.n8n.io)
- [n8n GitHub](https://github.com/n8n-io/n8n)
- [n8n Community](https://community.n8n.io)
- [n8n Docker Hub](https://hub.docker.com/r/n8nio/n8n)

## License

This Helm chart is provided as-is. n8n itself is licensed under the [Sustainable Use License](https://docs.n8n.io/reference/license/).

