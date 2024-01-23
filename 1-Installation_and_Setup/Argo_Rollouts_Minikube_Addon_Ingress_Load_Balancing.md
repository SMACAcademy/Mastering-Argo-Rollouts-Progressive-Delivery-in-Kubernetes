
# Minikube Ingress with Load Balancing Example

This guide demonstrates load balancing using Minikube's Ingress by creating two versions of an application and managing traffic distribution between them.

## Prerequisites
- **Minikube**: Ensure Minikube is installed and running.
- **kubectl**: Install `kubectl` to interact with the cluster.

## Steps

### 1. Start Minikube
Ensure Minikube is running:

```bash
minikube start
```

### 2. Enable the Ingress Addon
Enable the Ingress addon:

```bash
minikube addons enable ingress
```

### 3. Deploy Two Versions of an Application
Create two deployments for different versions of your app.

**Deployment 1 (Version 1):**

```yaml
# version1.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-version1
spec:
  selector:
    matchLabels:
      app: web
      version: v1
  replicas: 2
  template:
    metadata:
      labels:
        app: web
        version: v1
    spec:
      containers:
      - name: http-server
        image: hashicorp/http-echo
        args:
          - "-text=version1"
        ports:
        - containerPort: 5678
```

**Deployment 2 (Version 2):**

```yaml
# version2.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-version2
spec:
  selector:
    matchLabels:
      app: web
      version: v2
  replicas: 2
  template:
    metadata:
      labels:
        app: web
        version: v2
    spec:
      containers:
      - name: http-server
        image: hashicorp/http-echo
        args:
          - "-text=version2"
        ports:
        - containerPort: 5678
```

Apply these configurations:

```bash
kubectl apply -f version1.yaml
kubectl apply -f version2.yaml
```

### 4. Create Services for Each Version
Expose each deployment with a Kubernetes Service:

```yaml
# service-version1.yaml
apiVersion: v1
kind: Service
metadata:
  name: service-version1
spec:
  selector:
    app: web
    version: v1
  ports:
    - protocol: TCP
      port: 80
      targetPort: 5678
```

```yaml
# service-version2.yaml
apiVersion: v1
kind: Service
metadata:
  name: service-version2
spec:
  selector:
    app: web
    version: v2
  ports:
    - protocol: TCP
      port: 80
      targetPort: 5678
```

Apply these services:

```bash
kubectl apply -f service-version1.yaml
kubectl apply -f service-version2.yaml
```

### 5. Create an Ingress Resource for Load Balancing
Define an Ingress resource to balance traffic:

```yaml
# ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: web-ingress
spec:
  rules:
  - http:
      paths:
      - path: /v1
        pathType: Prefix
        backend:
          service:
            name: service-version1
            port:
              number: 80
      - path: /v2
        pathType: Prefix
        backend:
          service:
            name: service-version2
            port:
              number: 80
```

Apply the Ingress resource:

```bash
kubectl apply -f ingress.yaml
```

### 6. Access the Application
- **Find Minikube IP**: 

  ```bash
  minikube ip
  ```

- **Update Hosts File**: Map the Minikube IP to a test domain, like `example.com`.

- **Test Load Balancing**: Access `http://example.com/v1` and `http://example.com/v2` in your browser. You should see responses from version 1 and version 2 of the app, respectively.

## Conclusion
This setup demonstrates basic load balancing with Minikube's Ingress, routing traffic to different application versions based on URL path. It's fundamental in various deployment strategies like blue-green deployments, A/B testing, and canary releases.
