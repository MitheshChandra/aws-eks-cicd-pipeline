# 🚀 End-to-End CI/CD Pipeline on AWS EKS

![Jenkins](https://img.shields.io/badge/Jenkins-D24939?style=for-the-badge&logo=jenkins&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Nodejs](https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white)

## 📌 Project Overview
This project implements a production-grade CI/CD pipeline that automates the deployment of a **Polyglot Microservices Application** (The "Example Voting App").

The pipeline is triggered by GitHub commits, builds Docker images, pushes them to **Docker Hub**, and orchestrates a zero-downtime deployment to an **Amazon EKS (Elastic Kubernetes Service)** cluster using **Jenkins**.

---

## 🏗️ DevOps Architecture
[Insert a Screenshot of your Jenkins Pipeline Diagram here]

The CI/CD flow:
1.  **Code Commit:** Developer pushes code to GitHub `main` branch.
2.  **Trigger:** GitHub Webhook triggers the Jenkins Pipeline.
3.  **Build:** Jenkins builds Docker images for the modified microservices.
4.  **Push:** Images are tagged with the Build ID and pushed to Docker Hub.
5.  **Deploy:** Jenkins runs `kubectl` commands to update the EKS deployment.
6.  **Verify:** The application is exposed via a Kubernetes LoadBalancer.

---

## 🧩 Application Architecture
The application consists of 5 microservices working together:

| Service | Language | Description |
| :--- | :--- | :--- |
| **Vote-App** | Python | Frontend web app to cast votes. |
| **Result-App** | Node.js | Frontend web app to view results. |
| **Worker** | .NET | Background worker that processes votes. |
| **Redis** | Redis | In-memory queue for incoming votes. |
| **Database** | PostgreSQL | Persistent storage for processed votes. |

---

## 🚀 How to Run This Project

### Prerequisites
* AWS Account & CLI configured.
* `kubectl`, `eksctl`, and `docker` installed.
* Docker Hub Account.

### Step 1: Provision Infrastructure
We first launch an EC2 instance to act as our "Management Server" (Jenkins Master), and then provision the EKS Cluster.

**1. Launch Jenkins Server (EC2)**
* **AMI:** Ubuntu 24.04 LTS
* **Instance Type:** `t3.large` (Required for Jenkins + Docker)
* **Security Group:** Allow ports `22` (SSH) and `8080` (Jenkins)

**2. Create EKS Cluster**
SSH into your EC2 instance and run:
```bash
# Create Cluster (Takes ~15 mins)
eksctl create cluster --name demo-cluster --region us-east-1 --node-type t3.medium --nodes 2

# Update local kubeconfig to talk to the cluster
aws eks update-kubeconfig --region us-east-1 --name demo-cluster
