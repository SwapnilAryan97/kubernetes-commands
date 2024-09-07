# Kubernetes Setup with Minikube and Kubectl

This guide outlines the steps to set up a Kubernetes cluster using **Minikube** and **Kubectl**, covering the basic layers of Kubernetes abstraction, essential commands, and CLI usage.

---

## Layers of Abstraction in Kubernetes

Kubernetes provides various layers of abstraction to manage containerized applications, arranged from highest to lowest level:

- **Deployment**: Manages replica sets and ensures the desired number of pods are running.
- **ReplicaSet**: Maintains a stable set of pod replicas at all times.
- **Pods**: The smallest deployable units in Kubernetes, consisting of one or more containers.
- **Containers**: Lightweight, encapsulated environments for running applications.

---

## Prerequisites

Ensure that your system is updated and that **Minikube** and **Kubectl** are installed:

```bash
brew update
brew install minikube
brew install kubectl
```

### Verify Installation

To check the installation of **Minikube** and **Kubectl**, run:

```bash
file $(which minikube)
file $(which kubectl)
```

---

## CLI Tools Overview

### Kubectl CLI

- Interacts with and configures the Minikube cluster.
- Manages Kubernetes resources like pods, deployments, and services.

### Minikube CLI

- Manages the Minikube cluster (start, stop, delete).

---

## Basic Commands for Cluster Setup and Management

### 1. Start Minikube Kubernetes Cluster

Minikube requires a virtual environment to run. Here, we are using Docker for containerization:

```bash
minikube start --vm-driver=docker
```

### 2. Check Nodes and Pods

After starting the cluster, check the status of nodes and pods using **kubectl**:

```bash
kubectl get pods
kubectl get nodes
```

### 3. Check Cluster Status

To check the status of your Minikube cluster:

```bash
minikube status
```

### 4. Access Application Terminal

To access a terminal inside a pod:

```bash
kubectl exec -it <POD-NAME> -- bin/bash
```

Example:

```bash
kubectl exec -it mongo-depl-887485654-4xm8b -- bin/bash
```

---

## Working with Pods and Deployments

### 1. Create a Deployment

Deployments manage pods and ensure the desired number of pods are running. To create a deployment:

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

Check the status of deployments and pods:

```bash
kubectl get pods
kubectl get deployment
```

### 2. Check ReplicaSet

The **ReplicaSet** ensures the correct number of pod replicas are running. View the ReplicaSet managing your pods:

```bash
kubectl get replicaset
```

---

## Editing a Deployment Configuration

You can modify a deployment’s configuration by editing its YAML file:

```bash
kubectl edit deployment <deployment-name>
```

Example:

```bash
kubectl edit deployment nginx-depl
```

---

## Working with Configuration Files

### 1. Apply a Configuration File

To apply a Kubernetes configuration file:

```bash
kubectl apply -f <config-file.yaml>
```

Example:

```bash
kubectl apply -f nginx-deployment.yaml
```

### 2. Example Config File for Deployment

The following YAML file defines a basic **nginx** deployment:

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

### 3. Example Config File for Service

This YAML file defines a **Service** for the **nginx** deployment:

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

### 4. Example Config File for MongoDB Deployment

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

## Managing Secrets

### 1. Creating a Secret

Secrets are stored in base64 format. To encode a string into base64:

```bash
echo -n 'username' | base64
```

### 2. Example Config File for Secret

This YAML file creates a secret for MongoDB credentials:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mongo-secrets
type: Opaque
data:
  mongo-root-username: base64-username
  mongo-root-password: base64-password
```

---

## Retrieving Additional Information

To retrieve the detailed YAML for a deployment:

```bash
kubectl get deployment <deployment-name> -o yaml > <output-file.yaml>
```

Example:

```bash
kubectl get deployment nginx-deployment -o yaml > nginx-deployment-result.yaml
```

