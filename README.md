# 🚀 CI/CD Pipeline with Jenkins & ArgoCD on Kubernetes

## 📌 Project Overview
This project automates the deployment of a **multi-tier Java web application** on **Kubernetes** using **Jenkins & ArgoCD**. The pipeline ensures **continuous integration and continuous deployment (CI/CD)** with **automated builds, testing, and Kubernetes synchronization.**

## ⚠️ System Requirements
This setup requires a powerful system to handle Jenkins and ArgoCD effectively.

**Minimum Requirements:**
- **CPU:** 4 Cores (Recommended 8 Cores for production)
- **RAM:** 8GB (Recommended 16GB+ for smooth operation)
- **Storage:** 50GB Free Space

## 🎯 Purpose
- **Automate builds & deployments** using Jenkins & ArgoCD.
- **Ensure zero-downtime updates** for Kubernetes applications.
- **Improve software delivery speed & reliability.**
- **Enable automatic rollback in case of failure.**

## 🔧 Tech Stack & Tools Used
| Tool          | Purpose                                         |
|--------------|-----------------------------------------------|
| **Jenkins**  | Automates building & testing of applications |
| **ArgoCD**   | Manages GitOps-based deployments in K8s     |
| **Kubernetes** | Orchestrates containerized application    |
| **Docker**   | Builds & containers application images      |
| **GitHub/GitLab** | Stores application source code        |

## 📌 Setup Instructions

### 1️⃣ Prerequisites
Ensure you have:
- A **Kubernetes cluster** (Minikube, K3s, or cloud-based cluster)
- **kubectl & helm installed** → [Install Guide](https://helm.sh/docs/intro/install/)
- **Docker Installed** → [Download Docker](https://www.docker.com/get-started)

### 2️⃣ Install Jenkins
```sh
$ helm repo add jenkins https://charts.jenkins.io
$ helm repo update
$ helm install jenkins jenkins/jenkins --namespace cicd --create-namespace
```
- Get Jenkins admin password:
```sh
$ kubectl get secret --namespace cicd jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode
```
- Access Jenkins UI:
```sh
$ kubectl port-forward svc/jenkins 8080:8080 -n cicd
```
- Visit **http://localhost:8080** and log in with the admin password.

### 3️⃣ Install ArgoCD
```sh
$ kubectl create namespace argocd
$ kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
- Access ArgoCD UI:
```sh
$ kubectl port-forward svc/argocd-server -n argocd 8081:443
```
- Visit **https://localhost:8081** and log in using:
```sh
$ kubectl get secret -n argocd argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 --decode
```

### 4️⃣ Configure Jenkins for CI/CD
- Install **Git, Docker, and Kubernetes plugins** in Jenkins.
- Create a **Pipeline Job** in Jenkins:
```groovy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Docker Build & Push') {
            steps {
                sh 'docker build -t myapp:latest .'
                sh 'docker push myrepo/myapp:latest'
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f k8s/'
            }
        }
    }
}
```

### 5️⃣ Configure ArgoCD for Deployment
- Connect ArgoCD to your Git repository:
```sh
$ argocd app create myapp --repo https://github.com/your-repo/k8s.git --path ./ --dest-server https://kubernetes.default.svc --dest-namespace java
```
- Sync the application:
```sh
$ argocd app sync myapp
```

### 6️⃣ Testing & Validation
Once everything is deployed, verify:
```sh
$ kubectl get pods -n java
$ kubectl get services -n java
```
- Trigger a change in the Git repo and watch Jenkins build & deploy the update automatically!

## 🔥 Features
✅ **Automated Build & Deployment Process**
✅ **ArgoCD watches Git and syncs deployments**
✅ **Rollback mechanism for failures**
✅ **Seamless Kubernetes integration**

## 🚀 Next Steps
In the next phase, we will:
- **Implement monitoring with Prometheus & Grafana.** 📊
- **Enhance security with Role-Based Access Control (RBAC).** 🔐
- **Optimize deployments with Helm & Terraform.** ⚙️

```
# .gitignore
/k8s/
```

## 👨‍💻 Contributors
- **Mohamed Elweza**
- **Nader Ashour**

## 📜 License
This project is licensed under the **MIT License**.

---

📌 **Follow this repository for updates!**
✅ If you found this useful, don't forget to **star ⭐ the repo**!

💬 Feel free to open issues or contribute to improve this setup!
