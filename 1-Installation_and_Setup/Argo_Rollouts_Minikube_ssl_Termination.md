
# SSL Termination Using Ingress Addon in Minikube

This guide demonstrates setting up SSL termination using the Ingress addon in Minikube, involving creating a TLS certificate, deploying a sample application, and configuring the Ingress for SSL termination.

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

### 3. Create a Self-Signed TLS Certificate
For this demonstration, we'll use a self-signed TLS certificate.

- **Generate a Private Key and Certificate**:

  ```bash
  openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=hello-world.info/O=hello-world.info"
  ```

- **Create a Secret in Kubernetes**:

  ```bash
  kubectl create secret tls tls-secret --key tls.key --cert tls.crt
  ```

### 4. Deploy a Sample Application
Deploy an example application:

```yaml
# example-app.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-app
spec:
  selector:
    matchLabels:
      app: example
  replicas: 1
  template:
    metadata:
      labels:
        app: example
    spec:
      containers:
      - name: http-server
        image: k8s.gcr.io/echoserver:1.4
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: example-app
spec:
  selector:
    app: example
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
```

Apply this configuration:

```bash
kubectl apply -f example-app.yaml
```

### 5. Create an Ingress Resource with TLS Configuration
Configure the Ingress to use the TLS certificate for SSL termination:

```yaml
# example-ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
spec:
  tls:
  - hosts:
    - hello-world.info
    secretName: tls-secret
  rules:
  - host: hello-world.info
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: example-app
            port:
              number: 80
```

Apply the Ingress resource:

```bash
kubectl apply -f example-ingress.yaml
```

### 6. Access the Application
- **Find Minikube IP**: 

  ```bash
  minikube ip
  ```

- **Update Hosts File**: Map the Minikube IP to `hello-world.info`.

- **Access in Browser**: Navigate to `https://hello-world.info` to see the application's response via HTTPS.

## Conclusion
This setup demonstrates SSL termination at the Ingress level in Minikube. It centralizes SSL termination, allowing for easier management of TLS settings and certificates.
