
---

# Kubernetes Service Project

## Overview

This project demonstrates a simple Kubernetes setup with **backend** and **frontend** pods, exposing services and controlling traffic using a **NetworkPolicy**. It is deployed locally using **Minikube**, a tool for running Kubernetes on your local machine.

The project highlights:

* Pod creation
* Service exposure via NodePort
* Ingress control using NetworkPolicy
* Communication policies between frontend and backend pods

---

## Technologies Used

* **Kubernetes** – Container orchestration platform to manage containerized applications.
* **Minikube** – Local Kubernetes cluster for testing and development.
* **Docker** – Container runtime used by Minikube.
* **Python 3.9** – Backend container server.
* **Nginx (Alpine)** – Frontend container serving HTTP content.
* **kubectl** – Command-line tool to manage Kubernetes resources.

---

## Prerequisites

1. **Install Docker**
   Docker is required as the container runtime for Minikube.

```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
```

2. **Install Minikube**
   Download and install Minikube:

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

3. **Install kubectl**

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

---

## Start Minikube

```bash
minikube start --driver=docker
```

Check cluster status:

```bash
minikube status
kubectl get nodes
```

---

## Kubernetes Concepts Used

1. **Pod**

   * The smallest deployable unit in Kubernetes.
   * Pods can contain one or more containers.

2. **Service**

   * Exposes pods to the network.
   * Types: ClusterIP, NodePort, LoadBalancer.
   * Used here: **NodePort** to access pods via a specific port.

3. **NetworkPolicy**

   * Controls ingress and egress traffic to pods.
   * Allows fine-grained control over which pods can communicate.

4. **Namespace**

   * Logical partitioning of resources in Kubernetes.
   * All resources are deployed in the `dev` namespace.

---

## Project Architecture

```
dev namespace
├── backend-pod        (Python HTTP server on port 80)
│   └── backend-service (NodePort, exposes backend)
├── frontend-pod       (Nginx container on port 80)
│   └── frontend-service (NodePort 30080, exposes frontend)
└── allow-frontend-ingress (NetworkPolicy)
```

### Communication Flow

* **Frontend → Backend**: Allowed by NetworkPolicy.
* **Backend → Frontend**: Denied by NetworkPolicy.
  This simulates **one-way communication** between pods.

---

## Kubernetes YAML Explanation

### 1. Backend Pod (`backend-pod.yml`)

* Runs a simple Python HTTP server.
* Exposes port 80 internally.
* Uses `args` and `command` to create a Python server script on container start.

### 2. Backend Service (`backend-service.yml`)

* Exposes the backend pod using **NodePort**.
* Allows other pods in the cluster to reach it on port 80.

### 3. Frontend Pod (`frontend-pod.yml`)

* Runs Nginx on port 80.
* Ready to display content or connect to backend.

### 4. Frontend Service (`frontend-service.yml`)

* NodePort service on port 30080.
* Exposes frontend pod to external access.

### 5. Network Policy (`allow-frontend-ingress.yaml`)

* Only allows traffic **from frontend pods** to reach backend pods.
* Blocks all other ingress traffic to the backend.

---

## Commands to Apply Resources

```bash
kubectl create namespace dev

# Apply resources
kubectl apply -f backend-pod.yml
kubectl apply -f backend-service.yml
kubectl apply -f frontend-pod.yml
kubectl apply -f frontend-service.yml
kubectl apply -f allow-frontend-ingress.yaml

# Check pods and services
kubectl get pods -n dev
kubectl get svc -n dev
kubectl describe networkpolicy allow-frontend-to-backend -n dev
```

---

## Testing Communication

```bash
# From frontend pod (should work)
kubectl exec -it frontend-pod -n dev -- curl http://backend-service

# From backend pod (should fail)
kubectl exec -it backend-pod -n dev -- curl http://frontend-service
```

---

## Useful Commands

```bash
# Minikube dashboard
minikube dashboard

# Access frontend externally
minikube service frontend-service -n dev
```

---

## Summary

This project demonstrates:

* Deploying a frontend-backend architecture on Kubernetes.
* Exposing pods using services.
* Controlling traffic using NetworkPolicies.
* Running Kubernetes locally with Minikube.

---

