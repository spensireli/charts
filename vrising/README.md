# VRising Helm Chart

A Helm chart for deploying VRising game servers on Kubernetes.

## 🚀 Quick Start

### Add the Helm Repository

```bash
helm repo add spensireli https://spensireli.github.io/charts
helm repo update
```

### Install VRising Server

```bash
# Basic installation
helm install my-vrising spensireli/vrising

# With custom server name
helm install my-vrising spensireli/vrising \
  --set vrising.env.SERVERNAME="My Awesome VRising Server"

# With custom storage sizes
helm install my-vrising spensireli/vrising \
  --set vrising.persistence.server.size=20Gi \
  --set vrising.persistence.world.size=10Gi
```

## 📋 Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `vrising.env.SERVERNAME` | Published server name | `"My VRising Server"` |
| `vrising.env.WORLDNAME` | World name | `"world1"` |
| `vrising.env.TZ` | Timezone | `"UTC"` |
| `vrising.env.GAMEPORT` | Game UDP port | `"9876"` |
| `vrising.env.QUERYPORT` | Query UDP port | `"9877"` |
| `vrising.env.LOGDAYS` | Log file retention days | `"30"` |
| `vrising.env.BRANCH` | Server version branch | `""` (latest) |
| `vrising.persistence.enabled` | Enable persistent storage | `true` |
| `vrising.persistence.server.size` | Server data storage size | `10Gi` |
| `vrising.persistence.world.size` | World data storage size | `5Gi` |
| `service.type` | Kubernetes service type | `LoadBalancer` |

## 🎮 Accessing Your Server

After installation, get the external IP:

```bash
kubectl get service my-vrising-vrising
```

Connect to your VRising server using:
- **Game Port**: 9876 (UDP)
- **Query Port**: 9877 (UDP)

## 🔧 Advanced Configuration

### Using Custom Values File

Create a `values.yaml` file:

```yaml
vrising:
  env:
    SERVERNAME: "My Custom VRising Server"
    TZ: "America/New_York"
    LOGDAYS: "7"
  persistence:
    server:
      size: 50Gi
      storageClass: "fast-ssd"
    world:
      size: 20Gi
      storageClass: "fast-ssd"

service:
  type: NodePort

resources:
  limits:
    cpu: 2000m
    memory: 4Gi
  requests:
    cpu: 1000m
    memory: 2Gi
```

Install with custom values:

```bash
helm install my-vrising spensireli/vrising -f values.yaml
```

### Using Existing Persistent Volume Claims

```bash
helm install my-vrising spensireli/vrising \
  --set vrising.persistence.server.existingClaim=my-server-pvc \
  --set vrising.persistence.world.existingClaim=my-world-pvc
```

## 🔄 Upgrading

```bash
helm repo update
helm upgrade my-vrising spensireli/vrising
```

## 🗑️ Uninstalling

```bash
helm uninstall my-vrising
```

**Note**: This will not delete persistent volume claims. Delete them manually if needed:

```bash
kubectl delete pvc my-vrising-vrising-server
kubectl delete pvc my-vrising-vrising-world
```

## 📖 Chart Information

- **Chart Version**: 0.2.0
- **App Version**: latest
- **Docker Image**: `trueosiris/vrising`
- **Supported Environment Variables**: TZ, SERVERNAME, WORLDNAME, GAMEPORT, QUERYPORT, LOGDAYS, BRANCH

## 🎯 VRising Server Requirements

- **Ports**: 9876 (Game), 9877 (Query) - both UDP
- **Storage**: Persistent volumes for server files and world data
- **Resources**: Recommended 2GB+ RAM, 1+ CPU cores
- **Network**: LoadBalancer or NodePort service for external access

## 🤝 Contributing

Feel free to submit issues and pull requests!
