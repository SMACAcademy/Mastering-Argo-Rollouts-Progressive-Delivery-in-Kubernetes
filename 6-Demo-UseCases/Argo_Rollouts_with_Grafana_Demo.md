
# Argo Rollouts with Grafana or Helm Applications Demo

This guide demonstrates how to set up a basic canary deployment using Argo Rollouts with Grafana as an example application, managed via Helm in Kubernetes.

## Prerequisites

- A Kubernetes cluster
- `kubectl` configured for your cluster
- Argo Rollouts installed in your Kubernetes cluster
- Helm installed on your local machine

## Steps

### 1. **Install Grafana via Helm**

Add and update the Grafana Helm chart repository:

```bash
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```

Install Grafana using Helm:

```bash
helm install grafana grafana/grafana
```

### 2. **Create a Rollout for Grafana**

Create a Rollout resource for Grafana with a canary strategy. Example `grafana-rollout.yaml`:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: grafana-rollout
spec:
  replicas: 3
  selector:
    matchLabels:
      app: grafana
  template:
    metadata:
      labels:
        app: grafana
    spec:
      containers:
      - name: grafana
        image: grafana/grafana:latest
        ports:
        - containerPort: 3000
  strategy:
    canary:
      steps:
      - setWeight: 25
      - pause: {duration: 30s}
      - setWeight: 50
      - pause: {duration: 30s}
      - setWeight: 100
```

Apply with `kubectl`:

```bash
kubectl apply -f grafana-rollout.yaml
```

### 3. **Monitor the Canary Rollout**

Monitor the status of the canary deployment:

```bash
kubectl argo rollouts get rollout grafana-rollout --watch
```

### 4. **Update Grafana and Observe the Rollout**

Update Grafana by modifying the Rollout YAML, then reapply and monitor the rollout process.

### 5. **Integrate with Prometheus and Grafana for Monitoring (Optional)**

For advanced setups, integrate Prometheus for metrics collection and use Grafana for visualization.

### 6. **Automated Rollbacks (Optional)**

Configure Argo Rollouts for automated rollbacks based on metrics indicating issues with the new version.

## Conclusion

This demo provides a basic overview of using Argo Rollouts with a Helm-charted application like Grafana. For comprehensive setups, including monitoring and automated rollbacks, further configuration and integration are required.
