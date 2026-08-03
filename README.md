# Deploy MongoDB & Mongo Express on Kubernetes

A simple Kubernetes project that deploys **MongoDB** and **Mongo Express** using Kubernetes Deployments, Services, Secrets, and ConfigMaps.
<img width="1244" height="955" alt="image" src="https://github.com/user-attachments/assets/da9510e3-22e4-4c02-97e6-5a35bf449062" />

## 📌 Project Overview

This project demonstrates how to deploy a complete application consisting of:

- MongoDB Database
- Mongo Express Web UI
- Kubernetes Secret for database credentials
- Kubernetes ConfigMap for configuration
- Internal communication using Kubernetes Services
- External access using NodePort
- i use Use kubectl port-forward to access me application
- kubectl port-forward service/mongo-express-service 8081:8081 --address=0.0.0.0
- http://<EC2-Public-IP>:8081
---

## 🏗️ Architecture

```
                    Internet
                        │
                        ▼
              NodePort Service (30000)
                        │
                        ▼
              Mongo Express Pod
                        │
                        ▼
             MongoDB Service (ClusterIP)
                        │
                        ▼
                  MongoDB Pod
                        │
               Persistent Storage
```

---

## 📂 Project Structure

```
.
├── mongodb-secret.yaml
├── mongodb-configmap.yaml
├── mongodb-deployment.yaml
├── mongo-express.yaml
└── README.md
```

---

## 🔹 Components

### 1. Secret

Stores MongoDB credentials securely.

```yaml
kind: Secret
```

Contains:

- MongoDB Root Username
- MongoDB Root Password

---

### 2. ConfigMap

Stores MongoDB service name used by Mongo Express.

```yaml
kind: ConfigMap
```

Example:

```yaml
database_url: mongodb-service
```

---

### 3. MongoDB Deployment

Deploys MongoDB Pod.

Features:

- Official MongoDB image
- Environment variables from Secret
- Exposes port **27017**

---

### 4. MongoDB Service

Creates an internal ClusterIP service.

```
mongodb-service
```

Only accessible inside the Kubernetes cluster.

---

### 5. Mongo Express Deployment

Deploys Mongo Express.

Reads:

- MongoDB Username
- MongoDB Password
- MongoDB Service Name

from Kubernetes Secret and ConfigMap.

Exposes container port:

```
8081
```

---

### 6. Mongo Express Service

Exposes Mongo Express using NodePort.

```
NodePort: 30000
```

---

## 🚀 Deploy the Project

### Create Secret

```bash
kubectl apply -f mongodb-secret.yaml
```

### Create ConfigMap

```bash
kubectl apply -f mongodb-configmap.yaml
```

### Deploy MongoDB

```bash
kubectl apply -f mongodb-deployment.yaml
```

### Deploy Mongo Express

```bash
kubectl apply -f mongo-express.yaml
```

---

## Verify Resources

```bash
kubectl get all
```

Example:

```bash
kubectl get pods
kubectl get deployments
kubectl get svc
kubectl get secrets
kubectl get configmaps
```
<img width="1309" height="745" alt="image" src="https://github.com/user-attachments/assets/8dcbce21-f4da-44d1-a149-b2f4d82ca907" />

---

## Access Mongo Express

### Minikube

```bash
minikube service mongo-express-service
```

or

```bash
kubectl port-forward service/mongo-express-service 8081:8081
```

Open:

```
http://localhost:8081
```

---

### NodePort

If running on EC2 or a Kubernetes node:

```
http://<Node-IP>:30000
```

Example:

```
http://13.53.xxx.xxx:30000
```

---

## Useful Commands

### View Pods

```bash
kubectl get pods
```

### View Services

```bash
kubectl get svc
```

### View Deployments

```bash
kubectl get deployments
```

### Describe Pod

```bash
kubectl describe pod <pod-name>
```

### View Logs

```bash
kubectl logs <pod-name>
```

### Delete Everything

```bash
kubectl delete -f .
```

---

## Kubernetes Objects Used

| Object | Purpose |
|----------|---------|
| Deployment | Manage Pods |
| Service | Networking |
| Secret | Store sensitive data |
| ConfigMap | Store configuration |
| Pod | Run containers |

---

## Learning Objectives

After completing this project, you will understand:

- Kubernetes Deployments
- Pods
- Services
- ClusterIP
- NodePort
- Secrets
- ConfigMaps
- Environment Variables
- Container Communication
- Internal DNS in Kubernetes

---

## Technologies

- Kubernetes
- Minikube
- Docker
- MongoDB
- Mongo Express
