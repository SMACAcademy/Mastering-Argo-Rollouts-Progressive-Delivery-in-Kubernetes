
# Argo Rollouts with Prometheus Metrics Demo

This guide demonstrates setting up a simple scenario using Prometheus metrics for analyzing the performance of a web application deployment with Argo Rollouts.

## Prerequisites

- Kubernetes cluster
- Helm installed
- Kubectl installed
- Argo Rollouts installed in your cluster

## Step 1: Install Prometheus Using Helm

First, add the Prometheus Helm chart repository and install Prometheus:

```shell
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus prometheus-community/kube-prometheus-stack
```

This command installs Prometheus along with Grafana and alertmanager.

## Step 2: Deploy a Sample Application

Create a deployment file `webapp-deployment.yaml` for a simple web application:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: webapp
  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
        - name: webapp
          image: nginx
          ports:
            - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: webapp
spec:
  ports:
    - port: 80
      targetPort: 80
  selector:
    app: webapp
  type: ClusterIP
```

Deploy it using:

```shell
kubectl apply -f webapp-deployment.yaml
```

## Step 3: Expose Prometheus Metrics for the Application

Assuming your application already exposes Prometheus-compatible metrics on `/metrics` endpoint.

## Step 4: Create a ServiceMonitor

Enable Prometheus to discover and scrape your application's metrics, create a `ServiceMonitor`:

```yaml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: webapp-monitor
  labels:
    team: frontend
spec:
  selector:
    matchLabels:
      app: webapp
  endpoints:
    - port: web
```

Apply it with:

```shell
kubectl apply -f <filename>.yaml
```

## Step 5: Define an AnalysisTemplate for Argo Rollouts

Create an `AnalysisTemplate` for querying Prometheus:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: AnalysisTemplate
metadata:
  name: webapp-response-time
spec:
  metrics:
    - name: response-time
      interval: 5m
      provider:
        prometheus:
          address: http://prometheus-server.prometheus.svc.cluster.local
          query: |
            avg(http_request_duration_seconds{app="webapp"})
      successCondition: result < 0.5
      failureCondition: result >= 0.5
```

Apply this template:

```shell
kubectl apply -f analysis-template.yaml
```

## Step 6: Create a Rollout with Analysis

Define an Argo Rollout using the analysis template:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: webapp-rollout
spec:
  replicas: 2
  selector:
    matchLabels:
      app: webapp
  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
        - name: webapp
          image: nginx:latest
          ports:
            - containerPort: 80
  strategy:
    canary:
      steps:
        - setWeight: 50
        - pause: {duration: 10s}
        - analysis:
            templates:
              - templateName: webapp-response-time
        - setWeight: 100
```

Deploy the Rollout:

```shell
kubectl apply -f webapp-rollout.yaml
```

## Step 7: Monitor the Rollout

Monitor the progress of your rollout:

```shell
kubectl argo rollouts get rollout webapp-rollout --watch
```

This completes the setup for using Prometheus metrics in an Argo Rollouts deployment scenario.
