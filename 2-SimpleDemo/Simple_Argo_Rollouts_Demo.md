
# Deploying a Sample Application with Argo Rollouts and Dashboard Navigation

## Step 1: Create a Sample Application with Argo Rollouts

First, define a Rollout resource for your sample application. Here's an example configuration you can use:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: example-rollout
spec:
  replicas: 3
  selector:
    matchLabels:
      app: example
  template:
    metadata:
      labels:
        app: example
    spec:
      containers:
      - name: rollouts-demo
        image: argoproj/rollouts-demo:blue
        ports:
        - containerPort: 8080
  strategy:
    canary:
      steps:
      - setWeight: 20
      - pause: {duration: 10s}
      - setWeight: 40
      - pause: {duration: 10s}
      - setWeight: 60
      - pause: {duration: 10s}
      - setWeight: 80
      - pause: {duration: 10s}
```

Deploy the application:

```shell
kubectl apply -f rollout-sample.yaml
```

## Step 2: View the Rollout Status

Check the status with the Argo Rollouts CLI:

```shell
kubectl argo rollouts get rollout example-rollout --watch
```

## Step 3: Access the Argo Rollouts Dashboard

Start the dashboard:

```shell
kubectl argo rollouts dashboard
```

Navigate the dashboard to view and manage your Rollouts.

### Advanced Operations

- **Pause/Resume Rollout**: Control the deployment process interactively through the dashboard.
- **Promote/Fail a Canary**: Manually promote a canary version to stable or fail it to rollback.

Deploying with Argo Rollouts and using the dashboard facilitates sophisticated deployment strategies and provides a graphical interface for easy management.
