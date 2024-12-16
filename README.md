#  GITOPS2 with ArgoCD and Kubernetes


This repository implements a **GitOps** workflow using **ArgoCD**, **Minikube**, and **Kubernetes**. The application consists of a simple **HTML** page served via **Nginx**, designed to showcase a complete continuous integration and deployment pipeline using modern GitOps practices.

The goal of this project is to provide a detailed, step-by-step guide to deploy and manage applications using Kubernetes and ArgoCD. With these instructions, you can run the application on any compatible machine.


## 📋 Description


GitOps leverages Git as the single source of truth to manage Kubernetes configurations and applications declaratively. Changes in the repository are automatically synchronized with the Kubernetes cluster using **ArgoCD**, ensuring the desired state is maintained.

In this project, we deploy a web application that displays a custom HTML page managed by **Nginx**, while demonstrating how updates in the repository are seamlessly reflected in the Kubernetes cluster.



## 📂 Repository Structure

```plaintext
GITOPS/
│
├── index.html            # Web page
├── README.md            # Documentation
└── manifests/           # Kubernetes YAML files de Kubernetes
    ├── deployment.yaml
    ├── service.yaml
    └── configmap.yaml


```

## 🎯 Prerequisites

Antes de comenzar, asegúrate de tener instaladas las siguientes herramientas:

- Docker: To build and push container images.
- Kubectl: Kubernetes CLI to manage the cluster.
- Minikube: To create a local Kubernetes cluster.
- ArgoCD CLI: To interact with ArgoCD from the command line.
- Git: To clone and manage this repository.

## ⚙️ Setup Steps

### 1. Clone the Repository

```bash
git clone https://github.com/EstebanASM/GITOPS2.git
cd GITOPS2
```

### 2. Set Up the Kubernetes Cluster with Minikube

Start the Minikube cluster using VirtualBox as the driver:

```bash
minikube start --driver=virtualbox


```

Enable necessary Minikube addons:

```bash
minikube addons enable ingress
minikube addons enable metrics-server

```

Verify the cluster status:

```bash
kubectl get nodes

```

### 3. Install ArgoCD in the Kubernetes Cluster:

Install ArgoCD on the cluster:

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f argocd-install.yaml

```

Expose ArgoCD Using Port-forward:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443

```

- Access ArgoCD at: https://localhost:8080
- **User:** `admin`
- **Password:** Retrieve the admin password:

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

### 4. Configure the Application in ArgoCD

1. Open the ArgoCD interface.

2. Click Applications > New App.

3. Fill in the following details:

- Application Name: gitops2-web-app
- Project: default
- Sync Policy: Automatic (optional for auto-sync).
- Git Repository URL: https://github.com/EstebanASM/GITOPS2.git
- Path: manifests/
- Cluster: Your Kubernetes cluster.
- Namespace: default
4. Save and synchronize the application.

## 🌐 5. Access the Application

### Option 1: Using Minikube Service

Expose and access the application:

```bash
minikube service gitops-web-app-service

```

Open your browser in: `http://localhost:8080`.

### Option 2: Using Port-forward

Alternatively, access the service locally with:

```bash
kubectl port-forward svc/gitops-web-app-service 8080:80

```

Open your browser at: http://localhost:8080

## 🧪 6. Test the GitOps Workflow

### Update the Application:

1. Edit index.html and change the content (e.g., update the text).
2. Commit and push the changes to GitHub:

```bash
git add .
git commit -m "Update GitOps Web App content"
git push


```

### Watch ArgoCD Synchronize:

1. Open the ArgoCD interface.
2. Check that the application automatically synchronizes and deploys the changes.
3. Refresh the browser to view the updated application.

## 📖 7. Summary

This guide ensures your GitOps2 project runs seamlessly on any machine, leveraging Minikube, Kubernetes, Docker, and ArgoCD for a robust CI/CD pipeline. The modular steps provided are adaptable to various environments.
![alt text](Screenshots\ss.png)