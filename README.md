# Game Server Helm Charts

A collection of Helm charts for deploying various game servers on Kubernetes.

## 🚀 Quick Start

### Add the Helm Repository

```bash
helm repo add spensireli https://spensireli.github.io/charts
helm repo update
```

### Available Charts

| Chart | Description | Version | Status |
|-------|-------------|---------|--------|
| [vrising](./vrising/) | VRising game server | 0.2.0 | ✅ Active |
| [factorio](./factorio/) | Factorio game server | 0.1.0 | ✅ Active |
| [minecraft-bedrock](./minecraft/) | Minecraft Bedrock Edition server | 0.3.0 | ✅ Active |

## 🎮 Supported Game Servers

### VRising
- **Chart Name**: `vrising`
- **Game Type**: Survival/Action RPG
- **Ports**: 9876 (Game), 9877 (Query)
- **Persistent Storage**: ✅ Supported
- **Documentation**: [VRising Chart README](./vrising/README.md)

**Quick Install:**
```bash
helm install my-vrising spensireli/vrising
```

### Factorio
- **Chart Name**: `factorio-chart`
- **Game Type**: Factory Building/Automation
- **Port**: 34197 (UDP)
- **Persistent Storage**: ✅ Supported
- **Documentation**: [Factorio Chart README](./factorio/README.md)

**Quick Install:**
```bash
helm install my-factorio spensireli/factorio-chart
```

### Minecraft Bedrock Edition
- **Chart Name**: `minecraft-bedrock-chart`
- **Game Type**: Sandbox/Survival
- **Ports**: 19132 (Game), 19133 (IPv6)
- **Persistent Storage**: ✅ Supported
- **Documentation**: [Minecraft Bedrock Chart README](./minecraft/README.md)

**Quick Install:**
```bash
helm install my-minecraft spensireli/minecraft-bedrock-chart
```

## 📋 General Features

All charts in this repository provide:

- **Persistent Storage**: Save game data across pod restarts
- **Configurable Resources**: CPU and memory limits
- **Service Types**: LoadBalancer, NodePort, ClusterIP support
- **Environment Variables**: Easy server configuration
- **Health Checks**: Where supported by the game server
- **Security Context**: Proper pod security configurations

## 🔧 Common Usage Patterns

### Basic Installation
```bash
# Install any game server
helm install <release-name> spensireli/<chart-name>
```

### Custom Configuration
```bash
# Install with custom values file
helm install <release-name> spensireli/<chart-name> -f my-values.yaml

# Install with inline configuration
helm install <release-name> spensireli/<chart-name> \
  --set service.type=NodePort \
  --set persistence.size=50Gi
```

### Upgrading
```bash
helm repo update
helm upgrade <release-name> spensireli/<chart-name>
```

## 🚀 Automated Publishing

Charts are automatically packaged and published using GitHub Actions when changes are pushed to the repository.

## 📖 Repository Information

- **Repository URL**: https://spensireli.github.io/charts
- **Source Code**: [GitHub](https://github.com/spensireli/charts)
- **Chart Releaser**: Automated via GitHub Actions
- **Storage**: GitHub Pages

## 🤝 Contributing

We welcome contributions! Please:

1. Fork the repository
2. Create a new chart directory under the game name
3. Follow Helm chart best practices
4. Include comprehensive documentation
5. Test your chart thoroughly
6. Submit a pull request

### Adding a New Game Server

1. Create directory: `game-name/chart-name/` (e.g., `minecraft/minecraft-bedrock-chart/`)
2. Add chart files: `Chart.yaml`, `values.yaml`, `templates/`
3. Include comprehensive `README.md` with game-specific documentation
4. Update this main README to list the new chart
5. Ensure proper persistent storage configuration
6. Follow existing chart patterns for consistency

## 📝 License

This project is licensed under the MIT License - see individual chart directories for specific licensing information.
