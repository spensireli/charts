# Factorio Helm Chart

A Helm chart for deploying Factorio game servers on Kubernetes using the [factoriotools/factorio](https://hub.docker.com/r/factoriotools/factorio) Docker image.

## 🚀 Quick Start

### Add the Helm Repository

```bash
helm repo add spensireli https://spensireli.github.io/charts
helm repo update
```

### Install Factorio Server

```bash
# Basic installation
helm install my-factorio spensireli/factorio-chart

# With custom server name and password
helm install my-factorio spensireli/factorio-chart \
  --set factorio.serverName="My Awesome Factory" \
  --set factorio.gamePassword="secret123"

# With custom storage size
helm install my-factorio spensireli/factorio-chart \
  --set persistence.size=50Gi

# Multiple settings at once
helm install my-factorio spensireli/factorio-chart \
  --set factorio.serverName="Production Factory" \
  --set factorio.maxPlayers=20 \
  --set persistence.size=100Gi
```

## 📋 Configuration

### Core Settings

| Parameter | Description | Default |
|-----------|-------------|---------|
| `image.repository` | Factorio Docker image | `factoriotools/factorio` |
| `image.tag` | Image tag | `""` (uses appVersion) |
| `image.pullPolicy` | Image pull policy | `Always` |
| `replicaCount` | Number of replicas | `1` |
| `service.type` | Kubernetes service type | `LoadBalancer` |
| `service.port` | Factorio server port | `34197` |
| `service.protocol` | Service protocol | `UDP` |
| `service.annotations` | Service annotations | `{}` |

### Factorio Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `factorio.serverName` | Server name shown in browser | `""` |
| `factorio.serverDescription` | Server description | `""` |
| `factorio.gamePassword` | Password to join the game | `""` |
| `factorio.adminPassword` | Admin password | `""` |
| `factorio.username` | Factorio.com username | `""` |
| `factorio.token` | Factorio.com token | `""` |
| `factorio.saveName` | Save file to load | `""` |
| `factorio.public` | List server publicly | `false` |
| `factorio.lan` | Enable LAN discovery | `true` |
| `factorio.maxPlayers` | Maximum players (0 = unlimited) | `0` |
| `factorio.allowCommands` | Who can use commands | `"admins-only"` |
| `factorio.autosaveInterval` | Autosave interval (minutes) | `10` |
| `factorio.autosaveSlots` | Number of autosave slots | `5` |

### Storage & Resources

| Parameter | Description | Default |
|-----------|-------------|---------|
| `persistence.enabled` | Enable persistent storage | `true` |
| `persistence.size` | Storage size | `30Gi` |
| `persistence.storageClass` | Storage class | `""` (default) |
| `persistence.accessMode` | Access mode | `ReadWriteOnce` |
| `podSecurityContext.fsGroup` | File system group | `845` |
| `resources.limits.memory` | Memory limit | `4Gi` |
| `resources.limits.cpu` | CPU limit | `500m` |
| `resources.requests.memory` | Memory request | `4Gi` |

### Advanced

| Parameter | Description | Default |
|-----------|-------------|---------|
| `env` | Additional environment variables | `[]` |

## 🎮 Accessing Your Server

After installation, get the external IP:

```bash
kubectl get service my-factorio-factorio-chart
```

Connect to your Factorio server using:
- **Port**: 34197 (UDP)
- **Protocol**: UDP

## 🔧 Advanced Configuration

### Simple Command-Line Configuration

You can now easily configure Factorio settings directly via `--set` flags:

```bash
# Server configuration
helm install my-factorio spensireli/factorio-chart \
  --set factorio.serverName="My Awesome Factory" \
  --set factorio.serverDescription="Welcome to my factory!" \
  --set factorio.gamePassword="secret123" \
  --set factorio.maxPlayers=20

# Public server with Factorio.com integration
helm install my-factorio spensireli/factorio-chart \
  --set factorio.serverName="Public Factory" \
  --set factorio.public=true \
  --set factorio.username="your-factorio-username" \
  --set factorio.token="your-factorio-token"
```

### Using Custom Values File

Create a `values.yaml` file:

```yaml
# Factorio server configuration
factorio:
  serverName: "My Awesome Factory"
  serverDescription: "A factory built with love on Kubernetes"
  gamePassword: "secret123"
  adminPassword: "admin123"
  username: "your-factorio-username"
  token: "your-factorio-token"
  saveName: "my-factory-save"
  maxPlayers: 20
  public: true

# Custom storage configuration
persistence:
  enabled: true
  size: 100Gi
  storageClass: "fast-ssd"

# Service configuration with MetalLB
service:
  type: LoadBalancer
  annotations:
    metallb.universe.tf/ip-allocated-from-pool: homelab-lbs

# Resource configuration
resources:
  limits:
    cpu: 2000m
    memory: 8Gi
  requests:
    cpu: 1000m
    memory: 4Gi

# Security context
podSecurityContext:
  fsGroup: 845
```

Install with custom values:

```bash
helm install my-factorio spensireli/factorio-chart -f values.yaml
```

### Additional Environment Variables

For advanced configuration beyond the built-in `factorio.*` parameters, you can still use the `env` array:

```yaml
# Additional custom environment variables
env:
  - name: FACTORIO_CUSTOM_SETTING
    value: "custom-value"
  - name: ANOTHER_ENV_VAR
    value: "another-value"
```

For a complete list of supported environment variables, see the [factoriotools/factorio documentation](https://hub.docker.com/r/factoriotools/factorio).

### Using MetalLB LoadBalancer

For bare-metal Kubernetes clusters with MetalLB:

```yaml
service:
  type: LoadBalancer
  annotations:
    metallb.universe.tf/ip-allocated-from-pool: your-pool-name
```

### Using NodePort

For clusters without LoadBalancer support:

```yaml
service:
  type: NodePort
```

### Scaling to Zero

To temporarily stop the server without losing data:

```bash
helm upgrade my-factorio spensireli/factorio-chart --set replicaCount=0
```

To restart:

```bash
helm upgrade my-factorio spensireli/factorio-chart --set replicaCount=1
```

## 🔄 Upgrading

```bash
helm repo update
helm upgrade my-factorio spensireli/factorio-chart
```

## 🗑️ Uninstalling

```bash
helm uninstall my-factorio
```

**Note**: This will not delete persistent volume claims. Delete them manually if needed:

```bash
kubectl delete pvc my-factorio-factorio-chart-pvc
```

## 📖 Chart Information

- **Chart Version**: 0.1.0
- **App Version**: stable
- **Docker Image**: `factoriotools/factorio`
- **Supported Protocols**: UDP (default), TCP
- **Persistence**: Mounts to `/factorio` in container

## 🎯 Factorio Server Requirements

- **Port**: 34197 (UDP) - configurable
- **Storage**: Persistent volume for game saves and mods
- **Resources**: Recommended 4GB+ RAM, 500m+ CPU
- **Network**: LoadBalancer or NodePort service for external access
- **Security**: Runs with fsGroup 845 for proper file permissions

## 🎮 Game Features Supported

- **Save Management**: Load existing saves or create new ones
- **Mod Support**: Mount mod directories and configure mod portal access
- **Multiplayer**: Full multiplayer support with configurable player limits
- **Admin Controls**: Admin password and command configuration
- **Server Browser**: Public/private server listing configuration

## 🔒 Security Considerations

- Use strong passwords for game and admin access
- Consider using Kubernetes secrets for sensitive values:

```yaml
env:
  - name: FACTORIO_GAME_PASSWORD
    valueFrom:
      secretKeyRef:
        name: factorio-secrets
        key: game-password
```

## 🤝 Contributing

Feel free to submit issues and pull requests!

## 📚 Additional Resources

- [Factorio Official Website](https://factorio.com/)
- [factoriotools/factorio Docker Image](https://hub.docker.com/r/factoriotools/factorio)
- [Factorio Mod Portal](https://mods.factorio.com/)
