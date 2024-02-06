
## Argo Rollout Manual Promotion Demonstration

### Step 1: Define the Rollout Configuration

Create a `rollout.yaml` file for your application deployment using Argo Rollouts with a canary strategy:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: canary-demo-app
spec:
  replicas: 4
  strategy:
    canary:
      steps:
      - setWeight: 25
      - pause: {}
      - setWeight: 50
      - pause: {}
      - setWeight: 75
      - pause: {}
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
```

### Step 2: Apply the Rollout Configuration

Deploy the Rollout:

```bash
kubectl apply -f rollout.yaml
```

### Step 3: Verify Initial Rollout Status

Check the status of your rollout:

```bash
kubectl argo rollouts get rollout canary-demo-app
```

### Step 4: Manually Promote to Next Step

Promote the rollout to proceed to the next step:

```bash
kubectl argo rollouts promote canary-demo-app
```

### Step 5: Monitor Rollout Status

Monitor the status with:

```bash
kubectl argo rollouts get rollout canary-demo-app --watch
```

### Step 6: Final Promotion

After satisfying with the new version's performance, complete the deployment process.

### Conclusion

This demonstrates the manual promotion process in Argo Rollouts, illustrating control over a new version's deployment with a canary strategy.
