# Kubernetes Setup with Minikube and Kubectl

This guide provides a step-by-step walkthrough for setting up a Kubernetes cluster using **Minikube** and **Kubectl**. It covers Kubernetes abstraction layers, essential commands, and CLI usage.

---

## Table of Contents
1. [Kubernetes Layers of Abstraction](#layers-of-abstraction)
2. [Prerequisites](#prerequisites)
3. [CLI Tools Overview](#cli-tools-overview)
4. [Cluster Setup and Basic Commands](#basic-commands)
5. [Working with Pods and Deployments](#working-with-pods-and-deployments)
6. [Editing Deployments](#editing-deployments)
7. [Managing Configuration Files](#managing-configuration-files)
8. [Managing Secrets](#managing-secrets)
9. [Retrieving Deployment Information](#retrieving-information)
10. [Mongo Express Setup](#mongo-express-setup)
11. [Mongo Express Service](#mongo-express-service)
12. [Namespace](#namespace)
13. [Ingress](#ingress)

---

## 1. Kubernetes Layers of Abstraction <a name="layers-of-abstraction"></a>

Kubernetes provides multiple layers of abstraction to manage containerized applications, organized from the highest to the lowest level:
- **Deployment**: Manages replica sets and ensures a defined number of pods are running.
- **ReplicaSet**: Maintains a stable set of pod replicas at all times.
- **Pods**: The smallest deployable unit, consisting of one or more containers.
- **Containers**: Lightweight, encapsulated environments for running applications.

---

## 2. Prerequisites <a name="prerequisites"></a>

Ensure **Minikube** and **Kubectl** are installed on your system.

```bash
brew update
brew install minikube
brew install kubectl
```

### Verify Installation

Check if **Minikube** and **Kubectl** are installed correctly:

```bash
file $(which minikube)
file $(which kubectl)
```

---

## 3. CLI Tools Overview <a name="cli-tools-overview"></a>

- **Kubectl CLI**: Interacts with and configures the Minikube cluster, managing resources like pods, deployments, and services.
- **Minikube CLI**: Manages the Minikube cluster (start, stop, delete).

---

## 4. Cluster Setup and Basic Commands <a name="basic-commands"></a>

### 1. Start Minikube Cluster

Minikube requires a virtualization environment like Docker to run.

```bash
minikube start --vm-driver=docker
```

### 2. Check Nodes and Pods

After starting the cluster, verify the status of nodes and pods:

```bash
kubectl get nodes
kubectl get pods
```

### 3. Cluster Status

Check the status of your Minikube cluster:

```bash
minikube status
```

### 4. Access a Pod's Terminal

To access a terminal within a pod:

```bash
kubectl exec -it <POD-NAME> -- /bin/bash
```

Example:

```bash
kubectl exec -it mongo-depl-887485654-4xm8b -- /bin/bash
```

---

## 5. Working with Pods and Deployments <a name="working-with-pods-and-deployments"></a>

### 1. Create a Deployment

To create a deployment that ensures a certain number of pods are running:

```bash
kubectl create deployment <deployment-name> --image=<image-name>
```

Examples:

```bash
kubectl create deployment nginx-depl --image=nginx
kubectl create deployment mongo-depl --image=mongo
```

To delete a deployment:

```bash
kubectl delete deployment mongo-depl
```

### 2. View Deployments and Pods

Check the status of your deployments and pods:

```bash
kubectl get deployment
kubectl get pods
```

### 3. Check ReplicaSet

View the ReplicaSet managing your pods:

```bash
kubectl get replicaset
```

---

## 6. Editing Deployments <a name="editing-deployments"></a>

You can modify a deployment’s configuration by editing its YAML file:

```bash
kubectl edit deployment <deployment-name>
```

Example:

```bash
kubectl edit deployment nginx-depl
```

---

## 7. Managing Configuration Files <a name="managing-configuration-files"></a>

### 1. Apply a Configuration File

To apply a configuration file:

```bash
kubectl apply -f <config-file.yaml>
```

Example:

```bash
kubectl apply -f nginx-deployment.yaml
```

### 2. Example NGINX Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:1.27.1
          ports:
            - containerPort: 80
```

### 3. Example NGINX Service YAML

This file defines a **Service** for NGINX:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```

### 4. Example MongoDB Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongodb-deployment
  labels:
    app: mongodb
spec:
  replicas: 2
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
        - name: mongodb
          image: mongo:1.27.1
          ports:
            - containerPort: 27017
```

---

## 8. Managing Secrets <a name="managing-secrets"></a>

### 1. Create a Secret

Encode strings into base64 for Kubernetes secrets:

```bash
echo -n 'username' | base64
```

### 2. Example MongoDB Secret YAML

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mongo-secrets
type: Opaque
data:
  mongo-root-username: <base64-username>
  mongo-root-password: <base64-password>
```

### 3. Apply the Secret

```bash
kubectl apply -f mongo-secrets.yaml
```

---

## 9. Retrieving Deployment Information <a name="retrieving-information"></a>

To retrieve and output the YAML of an existing deployment:

```bash
kubectl get deployment <deployment-name> -o yaml > <output-file.yaml>
```

Example:

```bash
kubectl get deployment nginx-deployment -o yaml > nginx-deployment-result.yaml
```

---

## 10. Mongo Express Setup <a name="mongo-express-setup"></a>

To access Mongo Express via Minikube, run:

```bash
minikube service mongo-express-service
```

Optional login credentials:
- **Username**: `admin`
- **Password**: `pass`

---

## 11. Mongo Express Service <a name="mongo-express-service"></a>

- **nodePort:** External traffic can access the service using the node's IP address and the specified port.
- **Note:** Service layer itself is a loadbalancer. The type could be `LoadBalancer`, which automatically provisions an external load balancer in supported cloud environments. This type of service exposes the application to external traffic by assigning a public IP address.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mongo-express-service
spec:
  selector:
    app: mongo-express
  type: LoadBalancer
  ports:
    - protocol: TCP
      port: 8081  # The port that the service will expose
      targetPort: 8081 # The port on the container that the service will forward traffic to
      nodePort: 30000  # The port on each node that will be exposed
```

---

## 12. Namespace <a name="namespace"></a>

A **namespace** in Kubernetes is a way to divide cluster resources between multiple users or teams. It helps organize and isolate resources like pods, services, and deployments within the same cluster.

### Default Namespaces:
1. **default**: The default namespace for resources with no specific namespace.
2. **kube-system**: Used for Kubernetes system components like the API server, controller manager, etc.
3. **kube-public**: A readable namespace for everyone, often used for public resources.
4. **kube-node-lease**: Holds objects used for node heartbeat tracking in the cluster.

Namespaces help manage resources and permissions within large clusters.

CLI commands: 

1. Cluster info

```bash
kubectl cluster-info
```

2. Create namespace

```bash
kubectl create namespace <name>
```

### kubens

`kubens` is a command-line tool that helps you easily switch between Kubernetes namespaces. It simplifies namespace management by allowing you to quickly view and switch the active namespace for your current `kubectl` context. 

In Kubernetes, you often need to work with multiple namespaces (e.g., for development, staging, production), and by default, `kubectl` commands are executed in the `default` namespace unless you specify otherwise using `-n <namespace>`. `kubens` makes switching between namespaces more convenient.

### Key Features:
1. **Switching Namespaces**: Easily switch the current active namespace.
2. **Listing Namespaces**: List all available namespaces in the current Kubernetes context.
3. **Integration with `kubectl`**: Automatically updates the current namespace for `kubectl` commands.

### How to Use `kubens`:

1. **Install `kubens`**:
   You can install `kubens` using a package manager like `brew` (for macOS), or download it manually:
   
   ```bash
   brew install kubectx
   ```

   `kubens` comes bundled with `kubectx` (a tool to switch between Kubernetes contexts), so they are installed together.

2. **Switch Namespace**:
   To switch to a specific namespace:
   
   ```bash
   kubens <namespace-name>
   ```

   Example:
   ```bash
   kubens my-namespace
   ```

3. **List Namespaces**:
   To list all available namespaces in the current Kubernetes context:
   
   ```bash
   kubens
   ```

4. **Current Namespace**:
   When you switch namespaces using `kubens`, it updates the active namespace for all future `kubectl` commands, so you don't need to specify the `-n <namespace>` option.

### Example Workflow:

```bash
# List all namespaces
kubens

# Switch to a namespace
kubens my-namespace

# After switching, any kubectl command will run in the selected namespace
kubectl get pods
```

### Why Use `kubens`?

- **Convenience**: Simplifies the process of switching between namespaces without needing to manually specify `-n <namespace>` for each `kubectl` command.
- **Productivity**: Speeds up workflows when working with multiple namespaces frequently.
  
In summary, `kubens` is a handy tool that enhances your Kubernetes namespace management, making it easy to navigate and switch between namespaces quickly.

---

## 13. Ingress <a name="ingress"></a>

In Kubernetes, an **Ingress** is an API object that manages external access to services within a cluster, typically via HTTP or HTTPS. It acts as a gateway to route traffic from the outside world into your cluster and can handle things like load balancing, SSL termination, and name-based virtual hosting.

### Key Features of Ingress:
1. **Routing**: Ingress can route traffic to different services based on URL paths or domains.
2. **SSL/TLS**: Supports HTTPS by managing SSL certificates.
3. **Load Balancing**: Can distribute traffic to different service backends.
4. **Host and Path-Based Routing**: Ingress allows routing based on domain names (hostnames) or URL paths.

### Example Ingress Configuration:

Let’s assume you have two services in your Kubernetes cluster:
1. **frontend-service** serving the user interface.
2. **auth-service** handling authentication.

You want:
- Traffic to `example.com` to go to the **frontend-service**.
- Traffic to `example.com/auth` to be routed to the **auth-service**.

This is achieved using an Ingress.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: frontend-service
            port:
              number: 80
      - path: /auth
        pathType: Prefix
        backend:
          service:
            name: auth-service
            port:
              number: 8000
```

### Use Cases for Ingress:

1. **Host-Based Routing**: Route traffic to different services based on the host (domain name).
   - Example: `foo.example.com` goes to **service A**, while `bar.example.com` goes to **service B**.

2. **Path-Based Routing**: Route traffic based on URL paths within the same domain.
   - Example: Requests to `/api` go to **service A**, while `/auth` goes to **service B**.

3. **TLS/SSL Termination**: Ingress can handle HTTPS by using TLS certificates for secure communication.
   - You can add certificates to the Ingress definition using the `tls` section.

### Example with TLS:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
spec:
  tls:
  - hosts:
    - example.com
    secretName: tls-secret # The secret containing the TLS certificate
  rules:
  - host: example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: frontend-service
            port:
              number: 80
      - path: /auth
        pathType: Prefix
        backend:
          service:
            name: auth-service
            port:
              number: 8000
```

---
