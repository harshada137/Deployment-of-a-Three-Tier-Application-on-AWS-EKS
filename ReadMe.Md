# Deployment of a Three-Tier Application on AWS EKS

## Project Overview

This project demonstrates the deployment of a **three-tier web application** on **AWS EKS (Elastic Kubernetes Service)** using **Docker** and **Kubernetes**.

The application consists of:

* **Frontend:** ReactJS
* **Backend:** NodeJS (Express)
* **Database:** MongoDB

The application is exposed to users using an **AWS Application Load Balancer (ALB)** via Kubernetes Ingress. DockerHub is used as the container image registry. The deployment is done **manually (no CI/CD)** and uses **HTTP only (no domain, no HTTPS)**.

---

## Architecture

```
User (Browser)
   |
AWS Application Load Balancer (HTTP)
   |
AWS Load Balancer Controller
   |
Kubernetes Ingress
   |
-------------------------------------
|             |                     |
Frontend     Backend               MongoDB
(React)      (NodeJS)              (Database)
   |             |                     |
React Pods   NodeJS Pods          MongoDB Pod
                                   |
                             Persistent Volume (EBS)
```

---

## Tools & Technologies Used

* **AWS EKS** – Managed Kubernetes service
* **AWS EC2** – DevOps workstation & EKS worker nodes
* **Docker** – Containerization
* **DockerHub** – Image registry
* **Kubernetes** – Container orchestration
* **AWS ALB** – Application Load Balancer
* **Helm** – Install AWS Load Balancer Controller
* **MongoDB** – Database
* **GitHub** – Source code repository

---

## Prerequisites

### AWS & Infrastructure

* AWS account with **AdminAccess IAM user**
* AWS region: `ap-south-1`
* Ubuntu EC2 instance (used as DevOps workstation)
* EKS cluster with **t3.medium worker nodes**

### Local Tools (Installed on EC2)

* `docker`
* `kubectl`
* `awscli`
* `helm`
* `git`

### Accounts

* DockerHub account

---

## Repository Structure

```
TWSThreeTierAppChallenge/
├── client/        # React frontend
├── server/        # NodeJS backend
├── Dockerfile     # Docker build instructions
├── k8s/           # Kubernetes manifests
└── README.md
```

---

## High-Level Deployment Steps

### 1. Prepare Ubuntu EC2 (DevOps Workstation)

* Install Docker, kubectl, awscli, helm, git
* Configure AWS CLI

### 2. Create AWS EKS Cluster (Console)

* Create IAM role for EKS cluster
* Create EKS cluster
* Create Node Group (t3.medium)

### 3. Connect kubectl to EKS

```bash
aws eks update-kubeconfig --region ap-south-1 --name three-tier-cluster
kubectl get nodes
```

### 4. Clone the Repository

```bash
git clone https://github.com/LondheShubham153/TWSThreeTierAppChallenge.git
cd TWSThreeTierAppChallenge
```

### 5. Build & Push Docker Images (DockerHub)

#### Backend

```bash
cd server
docker build -t <dockerhub-username>/three-tier-backend:latest .
docker push <dockerhub-username>/three-tier-backend:latest
cd ..
```

#### Frontend

```bash
cd client
docker build -t <dockerhub-username>/three-tier-frontend:latest .
docker push <dockerhub-username>/three-tier-frontend:latest
cd ..
```

---

## Kubernetes Deployment

### 6. Create Namespace

```bash
kubectl create namespace three-tier
```

### 7. Deploy MongoDB (Database Tier)

* PersistentVolumeClaim (EBS-backed)
* MongoDB Deployment
* MongoDB ClusterIP Service

### 8. Deploy Backend (NodeJS)

* Backend Deployment
* Backend ClusterIP Service
* Environment variable to connect MongoDB

### 9. Deploy Frontend (React)

* Frontend Deployment
* Frontend ClusterIP Service

---

## AWS Load Balancer Controller

### 10. Install ALB Controller

* Create IAM policy for ALB controller
* Attach policy to node group IAM role
* Install controller using Helm

---

## Ingress Configuration

### 11. Create Ingress Resource

Routing rules:

* `/` → Frontend service
* `/api` → Backend service

This automatically provisions an **AWS Application Load Balancer**.

```bash
kubectl get ingress -n three-tier
```

---

## Accessing the Application

* Copy the **ALB DNS name** from the Ingress output
* Open it in the browser using HTTP

---

## Verification Checklist

* Pods are running
* MongoDB PVC is bound
* ALB is created successfully
* Frontend loads in browser
* Backend API responds correctly
* MongoDB data persists after pod restart

---

## Scalability

You can scale the application using:

```bash
kubectl scale deployment backend --replicas=3 -n three-tier
```

ALB automatically load-balances traffic.

---

## Security Notes

* MongoDB is **not publicly exposed**
* Internal communication uses ClusterIP services
* IAM roles are used for AWS access
* Security Groups restrict external traffic

---

## Learning Outcomes

* Real-world AWS EKS deployment
* Kubernetes multi-tier architecture
* Docker image management using DockerHub
* AWS ALB Ingress integration
* Persistent storage for databases


---


## DockerHub

**https://hub.docker.com/repositories/harshaa137**
---


## Guide

**Shubam Londhe**
---


## Actual Repository

**https://github.com/LondheShubham153/TWSThreeTierAppChallengershada**
---


## Author

**Harshada**
B.Tech Computer Science | Cloud & DevOps Enthusiast

---

## Notes
* This project uses **manual deployment only** (no CI/CD)
* HTTP is used instead of HTTPS
* No custom domain is configured

---


