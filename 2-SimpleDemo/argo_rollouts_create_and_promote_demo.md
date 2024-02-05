# Argo Rollouts: Create and Promote Demonstration

This document provides a step-by-step demonstration of the "Create" and "Promote" operations within Argo Rollouts, using a simple Canary deployment strategy as an example.

## Prerequisites

- Kubernetes, kubectl, and Argo Rollouts installed and configured.
- Access to a Kubernetes cluster for deploying resources.

## Step 1: Define the Rollout

Create a YAML file named `canary-rollout.yaml` with the following content:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: demo-rollout
  namespace: default
spec:
  replicas: 4
  selector:
    matchLabels:
      app: demo-app
  template:
    metadata:
      labels:
        app: demo-app
    spec:
      containers:
      - name: demo-container
        image: nginx:1.19.0
        ports:
        - containerPort: 80
  strategy:
    canary:
      steps:
      - setWeight: 25
      - pause: {duration: 30s}
      - setWeight: 50
      - pause: {duration: 30s}
      - setWeight: 75
      - pause: {duration: 30s}
```

This defines a Canary strategy for deploying an Nginx server.

## Step 2: Create the Rollout

Apply the rollout definition:

```bash
kubectl apply -f canary-rollout.yaml
```

This command initiates the deployment process according to the defined Canary strategy.

## Step 3: Monitor the Rollout Status

Watch the rollout's progress:

```bash
kubectl argo rollouts get rollout demo-rollout --watch
```

This provides live updates on the rollout status, showing its progression through the Canary steps.

## Step 4: Promote the Rollout

Manually promote the rollout to the next step or complete it:

```bash
kubectl argo rollouts promote demo-rollout
```

This moves the rollout to its next step or completes it, depending on its current stage.

## Conclusion

This demonstration has shown the basic operations for deploying and managing a Canary rollout strategy with Argo Rollouts. Adjust the YAML definition as needed for your specific deployment requirements.

