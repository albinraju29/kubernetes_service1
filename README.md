
***

# 🚀 Kubernetes Service Project

Welcome to the **Kubernetes Service Project**! Discover a robust frontend-backend application deployed inside Kubernetes using **Minikube**, featuring enforced **NetworkPolicies** for pod traffic control and best practices for container orchestration.

***

### 🌟 Features

- **Clear two-tier architecture** with isolated communication.
- Enforced **one-way traffic** using NetworkPolicy (frontend → backend only).
- Complete YAML manifests for pods, services, and policies.
- **Browser-accessible frontend** using NodePort.
- Hands-on **testing commands** to verify your deployment.

***

## 🛠 Technologies & Tools

| Tool         | Description                                                          |
| ------------ | ------------------------------------------------------------------- |
| 🐳 Docker    | Local container runtime for pod deployment and testing.              |
| ☸️ Kubernetes | Main orchestrator for pods, services, volumes, and policies.         |
| 🏠 Minikube  | Local Kubernetes cluster for rapid prototyping and dev.              |
| 🐍 Python 3.9 | Backend microservice running a simple HTTP server.                  |
| 🖥 Nginx (Alpine) | Lightweight, speedy web server for the frontend.                     |
| ⚡ kubectl    | Essential CLI for Kubernetes cluster interaction.                    |
| 👩‍💻 Bash     | Scripting usage for easy command reproduction.                      |

***

## 📌 Prerequisites

Ensure each tool below is set up & operational before continuing:

- **Docker** (install steps below)
- **Minikube**
- **kubectl** (latest preferred)

```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl enable docker
sudo systemctl start docker
```

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

***

## 🚀 Spin Up Minikube

Start your fresh local cluster using Docker as driver:

```bash
minikube start --driver=docker
```

Check node status to confirm setup:

```bash
minikube status
kubectl get nodes
```

***

## 🏗 Architecture Overview

#### **Emoji Diagram**

```
dev namespace
 ├─ 🐍 backend-pod [Python HTTP]
 │     └─ 🛡 backend-service [NodePort:80]
 ├─ 🖥 frontend-pod [Nginx]
 │     └─ 🌐 frontend-service [NodePort:30080]
 └─ 🛡 NetworkPolicy: allow-frontend-ingress
      ├── ✅ Frontend → Backend [allowed]
      ├── ❌ Backend → Frontend [blocked]
```
- **Arrows:** [frontend → backend] = allowed, [backend → frontend] = blocked.
- **NetworkPolicy:** Guarantees only the frontend pod may access the backend.

***

### 🔄 Traffic Flow

- Frontend requests (Nginx) can *reach* backend (Python).
- Backend requests to the frontend are **blocked by NetworkPolicy**.
- This enforces **secure, one-way access**, perfect for beginner security demos.

***

## 📄 Detailed Kubernetes YAML Breakdown

#### 1️⃣ backend-pod.yml
- Pod running a Python HTTP server.
- Responds with `"Hello from Backend"` on port 80.
- Inline script injected via command/args.

#### 2️⃣ backend-service.yml
- NodePort service exposing backend-pod within cluster.
- Listens on port 80; pods can access via service DNS.

#### 3️⃣ frontend-pod.yml
- Nginx running on port 80.
- Initiates HTTP requests to the backend-pod for data.

#### 4️⃣ frontend-service.yml
- NodePort service on port 30080.
- Allows direct access from local browser (via Minikube tunnel).

#### 5️⃣ allow-frontend-ingress.yaml
- NetworkPolicy selects backend pods for ingress.
- Only allows traffic source from frontend pods (using label selectors).
- Simulates simple one-way trust boundary.

***

## 📝 Apply & Manage Resources

Set up everything in the `dev` namespace:

```bash
kubectl create namespace dev
kubectl apply -f backend-pod.yml
kubectl apply -f backend-service.yml
kubectl apply -f frontend-pod.yml
kubectl apply -f frontend-service.yml
kubectl apply -f allow-frontend-ingress.yaml

kubectl get pods -n dev
kubectl get svc -n dev
kubectl describe networkpolicy allow-frontend-to-backend -n dev
```

***

## 🔍 Pod-to-Pod Connectivity Test

Try these to confirm NetworkPolicy works:

```bash
# ✅ Allowed: Frontend to Backend
kubectl exec -it frontend-pod -n dev -- curl http://backend-service

# ❌ Blocked: Backend to Frontend
kubectl exec -it backend-pod -n dev -- curl http://frontend-service
```

***

## 🌐 Open Frontend in Browser

Expose Nginx frontend for easy user testing:

```bash
minikube service frontend-service -n dev
```
- Opens browser automatically.
- Refresh to see dynamic backend fetch (if coded in frontend).

***

## 💡 Essential Commands

| Command | Purpose |
| ------- | ------- |
| `kubectl get pods -n dev` | Pod listing |
| `kubectl get svc -n dev` | Service listing |
| `kubectl describe pod <pod-name> -n dev` | Pod diagnostics |
| `kubectl logs <pod-name> -n dev` | Inspect logs |
| `minikube dashboard` | Launch visual cluster dashboard |

***

## 📚 Advanced Extensions

- **Try creating multiple backend/frontends** by editing the YAML's replica settings for real-world load balancing.
- **Inject resource limits/requests** for memory and CPU in pods for production readiness.
- **Expand NetworkPolicies** to allow/deny based on namespace, label, or IP range.

***

## 🏆 Project Takeaways

- **Kubernetes pod/service deployment** best practices.
- **NetworkPolicy basics** (pod-level traffic control).
- **Browser-based testing** through Minikube's NodePort.
- Real hands-on skills for infrastructure and security.

> Completing this project delivers practical experience with container orchestration, pod networking, and essential dev security concepts using Kubernetes.

***

## 🖼️ Architecture Diagram with Emojis

```
[🌐 User Browser]
    |
    ↓
[🖥 frontend-service (NodePort:30080)]
    |
    ↓
[🖥 frontend-pod (Nginx)]
    |
    ↓ (allowed by NetworkPolicy)
[🐍 backend-service (NodePort:80)]
    |
    ↓
[🐍 backend-pod (Python HTTP Server)]

       🚫
[No traffic from backend-pod → frontend-pod]
```



