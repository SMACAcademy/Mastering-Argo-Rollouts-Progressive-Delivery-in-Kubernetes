
# Prometheus Metrics with Argo Rollouts Demo

This guide demonstrates how to set up Prometheus to capture metrics from a Kubernetes application and use those metrics in Argo Rollouts to manage deployment strategies.

## Prerequisites

- Kubernetes cluster
- Helm installed
- Kubectl installed
- Argo Rollouts installed in your cluster

## Step 1: Deploy a Sample Application

Deploy a simple application that exposes Prometheus metrics.

**simple-app-deployment.yaml:**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: simple-http-server
spec:
  replicas: 1
  selector:
    matchLabels:
      app: simple-http-server
  template:
    metadata:
      labels:
        app: simple-http-server
    spec:
      containers:
      - name: http-server
        image: mendhak/http-https-echo
        ports:
        - containerPort: 80
        env:
        - name: PORT
          value: "80"
---
apiVersion: v1
kind: Service
metadata:
  name: simple-http-server
spec:
  ports:
  - port: 80
    targetPort: 80
  selector:
    app: simple-http-server
```

Apply it:

```shell
kubectl apply -f simple-app-deployment.yaml
```

## Step 2: Install Prometheus

Install Prometheus using Helm.

```shell
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus prometheus-community/kube-prometheus-stack
```

## Step 3: Create a ServiceMonitor

Create a ServiceMonitor for Prometheus to scrape metrics from the sample application.

**simple-app-service-monitor.yaml:**

```yaml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: simple-http-server-monitor
spec:
  selector:
    matchLabels:
      app: simple-http-server
  endpoints:
  - port: http
    interval: 15s
```

Apply it:

```shell
kubectl apply -f simple-app-service-monitor.yaml
```

## Step 4: Define an AnalysisTemplate

Create an AnalysisTemplate for Argo Rollouts to use Prometheus metrics.

**http-response-time-analysis-template.yaml:**

```yaml
apiVersion: argoproj.io/v1alpha1
kind: AnalysisTemplate
metadata:
  name: http-response-time-check
spec:
  metrics:
  - name: response-time
    provider:
      prometheus:
        address: http://prometheus-operated.default.svc.cluster.local
        query: |
          histogram_quantile(0.99, sum(rate(http_request_duration_seconds_bucket{app="simple-http-server"}[5m])) by (le))
    successCondition: "result <= 0.3"
    failureCondition: "result > 0.3"
```

Apply this template:

```shell
kubectl apply -f http-response-time-analysis-template.yaml
```

## Step 5: Create a Rollout with Analysis

Use the analysis template in a Rollout strategy for the application.

**simple-http-server-rollout.yaml:**

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: simple-http-server-rollout
spec:
  replicas: 2
  selector:
    matchLabels:
      app: simple-http-server
  template:
    metadata:
      labels:
        app: simple-http-server
    spec:
      containers:
      - name: http-server
        image: mendhak/http-https-echo
        ports:
        - containerPort: 80
  strategy:
    canary:
      steps:
      - setWeight: 50
      - pause: {duration: 15s}
      - analysis:
          templates:
          - templateName: http-response-time-check
      - setWeight: 100
```

Apply the Rollout:

```shell
kubectl apply -f simple-http-server-rollout.yaml
```

## Step 6: Monitor the Rollout

Monitor the rollout status and analysis results:

```shell
kubectl argo rollouts get rollout simple-http-server-rollout --watch
```

This completes our guide on integrating Prometheus metrics with Argo Rollouts for intelligent deployment strategies.
