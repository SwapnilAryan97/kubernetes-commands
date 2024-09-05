# Kubernetes Setup with Minikube and Kubectl

This guide outlines the steps to set up a Kubernetes cluster using **Minikube** and **Kubectl**. It covers the basic layers of Kubernetes abstraction, essential commands, and CLI usage.

## Layers of Abstraction in Kubernetes

The following are the layers of abstraction in Kubernetes, ordered from the highest to the lowest level:

- **Deployment**: Manages replicasets and ensures the desired number of pods are running.
- **ReplicaSet**: Maintains a stable set of replica pods running at all times.
- **Pods**: The smallest deployable units in Kubernetes, containing one or more containers.
- **Containers**: Lightweight, encapsulated environments to run applications.

## Prerequisites

Ensure that your system is updated and that **Minikube** and **Kubectl** are installed:

```bash
brew update
brew install minikube
brew install kubectl
```

### Verify Installation

To check the installation location of **Minikube** and **Kubectl**, run:

```bash
file $(which minikube)
file $(which kubectl)
```

## CLI Tools Overview

### Kubectl CLI
- Used to interact with and configure the Minikube cluster.
- Enables the management of Kubernetes resources like pods, deployments, and services.

### Minikube CLI
- Used for managing the Minikube cluster (starting, stopping, deleting the cluster).

---

## Basic Commands for Cluster Setup and Management

### 1. Start Minikube Kubernetes Cluster
Minikube requires a virtual environment to run. You need to specify the hypervisor for Minikube to use. Here we are using Docker for containerization:

```bash
minikube start --vm-driver=docker
```

### 2. Check Nodes and Pods
After starting the cluster, you can check the status of the nodes and pods using **kubectl**:

```bash
kubectl get pods
kubectl get nodes
```

### 3. Check Cluster Status
To check the status of your Minikube cluster, use:

```bash
minikube status
```

---

## Working with Pods and Deployments

### 4. Create a Deployment
Deployments manage pods, ensuring that the desired number of pods are running. To create a deployment:

```bash
kubectl create deployment <deployment-name> --image=<image-name>
```

Example:

```bash
kubectl create deployment nginx-depl --image=nginx
```

Check the deployment and pod status:

```bash
kubectl get pods
kubectl get deployment
```

### 5. Check ReplicaSet
The **ReplicaSet** ensures the specified number of pod replicas are running. To view the ReplicaSet managing your pods:

```bash
kubectl get replicaset
```

### ReplicaSet and Pod Names
- **Pod name**: `nginx-depl-85c9d7c5f4-ldx8q`
- **ReplicaSet name**: `nginx-depl-85c9d7c5f4`

---

## Editing a Deployment Configuration

You can modify the deployment configuration directly using the following command, which opens the configuration file for editing:

```bash
kubectl edit deployment <deployment-name>
```

Example:

```bash
kubectl edit deployment nginx-depl
```

---

This structured approach provides a clearer guide for setting up a Kubernetes cluster using Minikube and Kubectl. It outlines the commands in logical steps, ensuring ease of understanding and execution.