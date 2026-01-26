# Nodeeye Agent Helm Chart

This Helm chart deploys the Nodeeye agent into your Kubernetes cluster to collect observability data and push it to the Nodeeye control plane.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.2.0+
- An agent token from your Nodeeye dashboard

## Installation

### Quick Install

```bash
# Add the Nodeeye Helm repository
helm repo add nodeeye https://charts.nodeeye.io
helm repo update

# Install the agent
helm install nodeeye-agent nodeeye/nodeeye-agent \
  --namespace nodeeye \
  --create-namespace \
  --set agentToken="your-agent-token-here"
```

### Install from Local Chart

```bash
helm install nodeeye-agent ./helm/nodeeye-agent \
  --namespace nodeeye \
  --create-namespace \
  --set agentToken="your-agent-token-here"
```

## Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `agentToken` | **Required.** Agent authentication token | `""` |
| `controlPlaneUrl` | Control plane WebSocket URL | `wss://api.nodeeye.io/api/v1/agent/ws` |
| `clusterId` | Cluster identifier (auto-assigned if empty) | `""` |
| `image.repository` | Agent image repository | `ghcr.io/nodeeye/nodeeye-agent` |
| `image.tag` | Agent image tag | Chart appVersion |
| `agent.heartbeatInterval` | Heartbeat interval in seconds | `15` |
| `agent.snapshotInterval` | Snapshot interval in seconds | `10` |
| `agent.debug` | Enable debug logging | `false` |
| `resources.limits.cpu` | CPU limit | `200m` |
| `resources.limits.memory` | Memory limit | `256Mi` |
| `resources.requests.cpu` | CPU request | `50m` |
| `resources.requests.memory` | Memory request | `64Mi` |

### Using a values file

Create a `my-values.yaml`:

```yaml
agentToken: "ne_agent_xxxxxxxxxxxxx"
controlPlaneUrl: "wss://api.nodeeye.io/api/v1/agent/ws"

agent:
  heartbeatInterval: 15
  snapshotInterval: 10
  debug: false

resources:
  limits:
    cpu: 200m
    memory: 256Mi
  requests:
    cpu: 50m
    memory: 64Mi
```

Install with:

```bash
helm install nodeeye-agent ./helm/nodeeye-agent \
  --namespace nodeeye \
  --create-namespace \
  -f my-values.yaml
```

## What Gets Collected

The Nodeeye agent collects **read-only** information about your cluster:

- **Pods**: Status, events, logs
- **Nodes**: Status, conditions, resource usage
- **Deployments**: Configuration, replica status
- **Services**: Endpoints, selectors
- **Events**: Kubernetes events for correlation
- **ConfigMaps/Secrets**: Names only (no values)

The agent does **not**:
- Read secret values
- Modify any resources
- Send any PII or sensitive data

## Security

The agent runs with minimal permissions:
- Non-root user (UID 65534)
- Read-only root filesystem
- All capabilities dropped
- Read-only cluster access

## Uninstalling

```bash
helm uninstall nodeeye-agent --namespace nodeeye
```

## Troubleshooting

### Check agent status

```bash
kubectl get pods -n nodeeye -l app.kubernetes.io/name=nodeeye-agent
kubectl logs -n nodeeye -l app.kubernetes.io/name=nodeeye-agent
```

### Verify connectivity

```bash
kubectl exec -n nodeeye deploy/nodeeye-agent -- wget -q -O- http://localhost:8080/healthz
```

### Common Issues

1. **Agent not connecting**: Check that your `agentToken` is correct and the control plane URL is reachable.
2. **Permission errors**: Ensure the ClusterRole was created properly.
3. **Resource limits**: If the agent is OOMKilled, increase memory limits.
