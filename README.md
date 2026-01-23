# Nodeeye Helm Charts

Official Helm charts for [Nodeeye](https://nodeeye.ews.ng) - AI-powered Kubernetes observability.

## Usage

```bash
helm repo add nodeeye https://charts.nodeeye.ews.ng
helm repo update
```

## Available Charts

| Chart | Description |
|-------|-------------|
| [nodeeye-agent](./charts/nodeeye-agent) | Nodeeye agent for Kubernetes cluster monitoring |

## Installing the Nodeeye Agent

### Prerequisites

1. A Kubernetes cluster (1.19+)
2. Helm 3.0+
3. An agent token from your [Nodeeye dashboard](https://app.nodeeye.ews.ng)

### Quick Start

```bash
# Add the Nodeeye helm repository
helm repo add nodeeye https://charts.nodeeye.ews.ng
helm repo update

# Install the agent
helm install nodeeye-agent nodeeye/nodeeye-agent \
  --namespace nodeeye \
  --create-namespace \
  --set agentToken="YOUR_AGENT_TOKEN"
```

### Configuration

See [values.yaml](./charts/nodeeye-agent/values.yaml) for all configurable options.

| Parameter | Description | Default |
|-----------|-------------|---------|
| `agentToken` | **Required.** Agent token from Nodeeye dashboard | `""` |
| `controlPlaneUrl` | WebSocket URL for control plane | `wss://api.nodeeye.ews.ng/api/v1/agent/ws` |
| `image.repository` | Agent image repository | `ghcr.io/nodeeye/nodeeye-agent` |
| `image.tag` | Agent image tag | Chart appVersion |
| `resources.requests.cpu` | CPU request | `50m` |
| `resources.requests.memory` | Memory request | `64Mi` |
| `resources.limits.cpu` | CPU limit | `200m` |
| `resources.limits.memory` | Memory limit | `256Mi` |

### Self-Hosted Installation

For self-hosted Nodeeye deployments:

```bash
helm install nodeeye-agent nodeeye/nodeeye-agent \
  --namespace nodeeye \
  --create-namespace \
  --set agentToken="YOUR_AGENT_TOKEN" \
  --set controlPlaneUrl="ws://your-server:8080/api/v1/agent/ws"
```

### Upgrading

```bash
helm repo update
helm upgrade nodeeye-agent nodeeye/nodeeye-agent --namespace nodeeye
```

### Uninstalling

```bash
helm uninstall nodeeye-agent --namespace nodeeye
```

## License

Apache License 2.0 - See [LICENSE](./LICENSE) for details.
