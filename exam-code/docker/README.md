
# Crypto Price Tracker – Docker & Kubernetes Exam

## Overview
This project demonstrates containerization and orchestration of a Python-based cryptocurrency application.

The system consists of three components:

- **Frontend (Flask)** – Displays cryptocurrency prices
- **Backend (Flask API)** – Fetches and processes crypto data
- **MySQL Database** – Stores cryptocurrency prices

The project includes:

- Dockerfiles for frontend and backend
- Docker Compose orchestration
- Kubernetes deployments and services
- Secure secret handling (no hardcoded credentials)
- Helm charts for automated deployment
- Screenshots proving the application works

DockerHub Images used:

- `nixonp95/crypto-frontend:1.0`
- `nixonp95/crypto-backend:1.0`

---

# Project Structure

```
exam-code/docker
│
├── be/
├── fe/
├── docker-compose.yaml
│
├── k8s/
│   ├── backend-deployment.yaml
│   ├── backend-service.yaml
│   ├── frontend-deployment.yaml
│   ├── frontend-service.yaml
│   ├── mysql-deployment.yaml
│   ├── mysql-service.yaml
│   └── namespace.yaml
│
├── helm/
│   ├── backend/
│   ├── frontend/
│   └── mysql/
│
├── screenshots/
│   ├── k8s_pods.png
│   ├── k8s_services.png
│   └── app_running.png
│
├── .env.example
└── README.md
```

---

# Section 1 – Docker

## Prepare Environment Variables

Create a local `.env` file (not committed to Git):

```
MYSQL_USER=root
MYSQL_PASSWORD=YOUR_PASSWORD
MYSQL_ROOT_PASSWORD=YOUR_PASSWORD
MYSQL_DB=crypto_db
```

The repository contains `.env.example` as a template.

## Build and Run the Application

From:

```
exam-code/docker
```

Run:

```
docker compose up --build
```

Access the application:

```
http://localhost:5002
```

Expected result:

```
Crypto Price Tracker
BITCOIN: $XXXXX – Saved to database: true
```

This confirms:
- Frontend container works
- Backend API works
- MySQL database connection works

---

# Section 2 – Kubernetes

The application is deployed using Kubernetes **ClusterIP services** as required.

## Create Namespace

```
kubectl apply -f k8s/namespace.yaml
```

## Create MySQL Secret (secure – not stored in Git)

Run in terminal:

```
read -s -p "Enter MYSQL root password: " MYSQLPASS; echo

kubectl -n crypto create secret generic mysql-secret --from-literal=MYSQL_USER=root --from-literal=MYSQL_PASSWORD="$MYSQLPASS" --from-literal=MYSQL_ROOT_PASSWORD="$MYSQLPASS" --from-literal=MYSQL_DB=crypto_db

unset MYSQLPASS
```

## Deploy Kubernetes Resources

```
kubectl apply -f k8s/
```

Verify deployment:

```
kubectl get pods -n crypto
kubectl get svc -n crypto
```

Access the application using port-forward:

```
kubectl port-forward -n crypto svc/web 5002:5002
```

Open:

```
http://localhost:5002
```

---

# Section 3 – Helm Charts (Bonus)

Helm charts are provided for:

- frontend
- backend
- mysql

They allow configurable values such as:

- replica count
- container images
- database configuration

Install charts:

```
helm install crypto-mysql helm/mysql -n crypto
helm install crypto-backend helm/backend -n crypto
helm install crypto-frontend helm/frontend -n crypto
```

Verify deployment:

```
kubectl get pods -n crypto
kubectl get svc -n crypto
```

---

# Security

The repository follows secure DevOps practices:

- ❌ No passwords stored in Git
- ❌ No hardcoded secrets in YAML
- ❌ `.env` file committed to repository
- ✅ `.env.example` provided as a template
- ✅ Kubernetes Secrets created at deployment time
- ✅ Deployments reference secrets using `secretKeyRef`

---

# Architecture

Application request flow:

```
Browser
   ↓
Frontend (web service)
   ↓
Backend API (backend-service)
   ↓
MySQL Database (mysqldb)
```

---

# Screenshots

## Kubernetes Pods

![Kubernetes Pods](screenshots/k8s_pods.png)

## Kubernetes Services

![Kubernetes Services](screenshots/k8s_services.png)

## Application Running

![Crypto Price Tracker](screenshots/app_running.png)

---

# Author

Docker & Kubernetes DevOps Exam Submission
