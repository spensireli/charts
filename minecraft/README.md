# Minecraft Bedrock Helm Chart

A Helm chart for deploying Minecraft Bedrock Edition servers on Kubernetes using the [itzg/minecraft-bedrock-server](https://hub.docker.com/r/itzg/minecraft-bedrock-server) Docker image.

## 🚀 Quick Start

### Add the Helm Repository

```bash
helm repo add spensireli https://spensireli.github.io/charts
helm repo update
```

### Install Minecraft Bedrock Server

```bash
# Basic installation
helm install my-minecraft spensireli/minecraft-bedrock-chart

# With custom server name and game mode
helm install my-minecraft spensireli/minecraft-bedrock-chart \
  --set minecraftServer.serverName="My Bedrock World" \
  --set minecraftServer.gameMode="creative"

# With custom storage size and max players
helm install my-minecraft spensireli/minecraft-bedrock-chart \
  --set persistence.size=50Gi \
  --set minecraftServer.maxPlayers=20

# Production setup with multiple settings
helm install my-minecraft spensireli/minecraft-bedrock-chart \
  --set minecraftServer.serverName="Production Server" \
  --set minecraftServer.difficulty="hard" \
  --set minecraftServer.maxPlayers=50 \
  --set persistence.size=100Gi \
  --set service.type=LoadBalancer
```

## 📋 Configuration

### Core Settings

| Parameter | Description | Default |
|-----------|-------------|---------|
| `image.repository` | Minecraft Bedrock Docker image | `itzg/minecraft-bedrock-server` |
| `image.tag` | Image tag | `"latest"` |
| `image.pullPolicy` | Image pull policy | `IfNotPresent` |
| `replicaCount` | Number of replicas (should always be 1) | `1` |
| `service.type` | Kubernetes service type | `LoadBalancer` |
| `service.port` | Minecraft server port | `19132` |
| `service.protocol` | Service protocol | `UDP` |

### Minecraft Server Configuration

| Parameter | Description | Default | Options |
|-----------|-------------|---------|---------|
| `minecraftServer.eula` | Accept Minecraft EULA | `"TRUE"` | `"TRUE"` (required) |
| `minecraftServer.serverName` | Server display name | `"Minecraft Bedrock Server"` | Any string |
| `minecraftServer.gameMode` | Game mode | `"survival"` | `survival`, `creative`, `adventure` |
| `minecraftServer.difficulty` | Difficulty level | `"normal"` | `peaceful`, `easy`, `normal`, `hard` |
| `minecraftServer.allowCheats` | Enable cheats | `"false"` | `"true"`, `"false"` |
| `minecraftServer.maxPlayers` | Maximum players | `10` | Number |
| `minecraftServer.levelName` | World name | `"Bedrock level"` | Any string |
| `minecraftServer.levelSeed` | World seed | `""` | Any string/number |
| `minecraftServer.levelType` | World type | `"DEFAULT"` | `DEFAULT`, `FLAT`, `LEGACY` |
| `minecraftServer.onlineMode` | Xbox Live authentication | `"true"` | `"true"`, `"false"` |
| `minecraftServer.whiteList` | Enable whitelist | `"false"` | `"true"`, `"false"` |
| `minecraftServer.motd` | Message of the day | `"Bedrock Dedicated Server"` | Any string |

### Advanced Server Settings

| Parameter | Description | Default | Options |
|-----------|-------------|---------|---------|
| `minecraftServer.serverPort` | IPv4 server port | `19132` | Port number |
| `minecraftServer.serverPortv6` | IPv6 server port | `19133` | Port number |
| `minecraftServer.defaultPlayerPermissionLevel` | Default permission level | `"member"` | `visitor`, `member`, `operator` |
| `minecraftServer.texturepackRequired` | Require texture pack | `"false"` | `"true"`, `"false"` |
| `minecraftServer.forceGamemode` | Force game mode | `"false"` | `"true"`, `"false"` |
| `minecraftServer.playerIdleTimeout` | Idle timeout (minutes) | `30` | Number |
| `minecraftServer.maxThreads` | Maximum threads | `8` | Number |

### Persistence Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `persistence.enabled` | Enable persistent storage | `true` |
| `persistence.accessMode` | Volume access mode | `ReadWriteOnce` |
| `persistence.size` | Storage size | `1Gi` |
| `persistence.storageClass` | Storage class | `""` (default) |

### Service Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `service.type` | Service type | `LoadBalancer` |
| `service.port` | Service port | `19132` |
| `service.protocol` | Service protocol | `UDP` |

### Health Checks

| Parameter | Description | Default |
|-----------|-------------|---------|
| `livenessProbe.tcpSocket.port` | Liveness probe port | `19132` |
| `livenessProbe.initialDelaySeconds` | Initial delay for liveness | `120` |
| `livenessProbe.periodSeconds` | Liveness probe period | `20` |
| `readinessProbe.tcpSocket.port` | Readiness probe port | `19132` |
| `readinessProbe.initialDelaySeconds` | Initial delay for readiness | `30` |
| `readinessProbe.periodSeconds` | Readiness probe period | `10` |

## 🎮 Usage Examples

### Creative Server for Building

```yaml
# values-creative.yaml
minecraftServer:
  serverName: "Creative Build Server"
  gameMode: "creative"
  difficulty: "peaceful"
  allowCheats: "true"
  maxPlayers: 20
  
persistence:
  size: 10Gi
```

```bash
helm install creative-server spensireli/minecraft-bedrock-chart -f values-creative.yaml
```

### Survival Server with Whitelist

```yaml
# values-survival.yaml
minecraftServer:
  serverName: "Hardcore Survival"
  gameMode: "survival"
  difficulty: "hard"
  allowCheats: "false"
  maxPlayers: 10
  whiteList: "true"
  onlineMode: "true"
  
persistence:
  size: 50Gi
  storageClass: "fast-ssd"
```

```bash
helm install survival-server spensireli/minecraft-bedrock-chart -f values-survival.yaml
```

### Adventure Map Server

```yaml
# values-adventure.yaml
minecraftServer:
  serverName: "Adventure Map Server"
  gameMode: "adventure"
  difficulty: "normal"
  allowCheats: "false"
  maxPlayers: 15
  forceGamemode: "true"
  levelName: "Adventure World"
  
persistence:
  size: 25Gi
```

```bash
helm install adventure-server spensireli/minecraft-bedrock-chart -f values-adventure.yaml
```

## 🔧 Advanced Configuration

### Custom World Seed

```bash
helm install my-minecraft spensireli/minecraft-bedrock-chart \
  --set minecraftServer.levelSeed="123456789" \
  --set minecraftServer.levelName="Seeded World"
```

### Performance Tuning

```yaml
# High-performance setup
resources:
  requests:
    cpu: 2000m
    memory: 4Gi
  limits:
    cpu: 4000m
    memory: 8Gi

minecraftServer:
  maxThreads: 16
  maxPlayers: 50

persistence:
  size: 100Gi
  storageClass: "premium-ssd"
```

### Multiple Server Instances

```bash
# Server 1 - Creative
helm install creative spensireli/minecraft-bedrock-chart \
  --set minecraftServer.serverName="Creative Server" \
  --set minecraftServer.gameMode="creative" \
  --set service.port=19132

# Server 2 - Survival  
helm install survival spensireli/minecraft-bedrock-chart \
  --set minecraftServer.serverName="Survival Server" \
  --set minecraftServer.gameMode="survival" \
  --set minecraftServer.serverPort=19134 \
  --set service.port=19134
```

## 🌐 Networking

### Service Types

**LoadBalancer** (Recommended for cloud providers):
```yaml
service:
  type: LoadBalancer
```

**NodePort** (For bare metal or when LoadBalancer isn't available):
```yaml
service:
  type: NodePort
```

**ClusterIP** (Internal access only):
```yaml
service:
  type: ClusterIP
```

### Port Configuration

Minecraft Bedrock uses UDP protocol on the following ports:
- **19132**: IPv4 game port (default)
- **19133**: IPv6 game port (default)

Both ports are automatically configured and exposed by the chart.

## 💾 Data Persistence

The chart uses PersistentVolumeClaims to store world data at `/data` in the container. This ensures your world persists across pod restarts and updates.

### Backup Recommendations

1. **Regular PVC snapshots** (if supported by your storage provider)
2. **Volume backups** using tools like Velero
3. **File-level backups** by mounting the PVC to a backup pod

### Storage Classes

Different storage classes for different needs:

```yaml
# Fast NVMe for high-performance servers
persistence:
  storageClass: "premium-ssd"
  size: 100Gi

# Standard storage for casual servers  
persistence:
  storageClass: "standard"
  size: 10Gi

# Network storage for shared access
persistence:
  storageClass: "nfs"
  size: 50Gi
```

## 🔍 Monitoring and Troubleshooting

### View Server Logs

```bash
# Follow logs in real-time
kubectl logs -l app.kubernetes.io/name=minecraft-bedrock-chart -f

# Get logs from specific pod
kubectl logs <pod-name> -f
```

### Check Server Status

```bash
# Get pod status
kubectl get pods -l app.kubernetes.io/name=minecraft-bedrock-chart

# Describe pod for events
kubectl describe pod <pod-name>

# Check service and endpoints
kubectl get svc,endpoints
```

### Common Issues

**Pod stuck in Pending**: Check storage class and available storage
```bash
kubectl describe pvc <pvc-name>
```

**Can't connect to server**: Verify service type and external IP
```bash
kubectl get svc <service-name>
```

**Server won't start**: Check EULA acceptance and resource limits
```bash
kubectl logs <pod-name>
```

## 🔄 Upgrading

### Chart Updates

```bash
# Update repository
helm repo update

# Upgrade to latest chart version
helm upgrade my-minecraft spensireli/minecraft-bedrock-chart

# Upgrade with new values
helm upgrade my-minecraft spensireli/minecraft-bedrock-chart -f new-values.yaml
```

### Server Updates

The `itzg/minecraft-bedrock-server` image is regularly updated with the latest Minecraft versions. To update:

```bash
# Update to latest server version
helm upgrade my-minecraft spensireli/minecraft-bedrock-chart \
  --set image.tag="latest"
```

## 🤝 Contributing

We welcome contributions! Please see the main repository's contributing guidelines.

## 📝 License

This chart is licensed under the MIT License. See the main repository for details.

## 🔗 Links

- [Minecraft Bedrock Server Docker Image](https://hub.docker.com/r/itzg/minecraft-bedrock-server)
- [Chart Repository](https://github.com/spensireli/charts)
- [Minecraft Official Site](https://www.minecraft.net/)
