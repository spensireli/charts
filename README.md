# Game Server Helm Charts

A collection of Helm charts for deploying various game servers on Kubernetes.

## 🚀 Quick Start

### Add the Helm Repository

```bash
helm repo add spensir https://spensir.github.io/charts
helm repo update
```

### Available Charts

| Chart | Description | Version | Status |
|-------|-------------|---------|--------|
| [vrising](./vrising/) | VRising game server | 0.2.0 | ✅ Active |

## 🎮 Supported Game Servers

### VRising
- **Chart Name**: `vrising`
- **Game Type**: Survival/Action RPG
- **Ports**: 9876 (Game), 9877 (Query)
- **Persistent Storage**: ✅ Supported
- **Documentation**: [VRising Chart README](./vrising/README.md)

**Quick Install:**
```bash
helm install my-vrising spensir/vrising
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
helm install <release-name> spensir/<chart-name>
```

### Custom Configuration
```bash
# Install with custom values file
helm install <release-name> spensir/<chart-name> -f my-values.yaml

# Install with inline configuration
helm install <release-name> spensir/<chart-name> \
  --set service.type=NodePort \
  --set persistence.size=50Gi
```

### Upgrading
```bash
helm repo update
helm upgrade <release-name> spensir/<chart-name>
```

## 🚀 Automated Publishing

Charts are automatically packaged and published using GitHub Actions when changes are pushed to the repository.

## 📖 Repository Information

- **Repository URL**: https://spensir.github.io/charts
- **Source Code**: [GitHub](https://github.com/spensir/charts)
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

1. Create directory: `game-name/`
2. Add chart files: `Chart.yaml`, `values.yaml`, `templates/`
3. Include `README.md` with game-specific documentation
4. Update this main README to list the new chart
5. Ensure proper persistent storage configuration

## 📝 License

This project is licensed under the MIT License - see individual chart directories for specific licensing information.
