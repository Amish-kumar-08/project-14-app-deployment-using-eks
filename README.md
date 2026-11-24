# Project 14: Application Deployment using Amazon EKS

## Overview

This project demonstrates how to deploy and expose a containerized web application using **Amazon Elastic Kubernetes Service (Amazon EKS)**.

The application uses Nginx to serve a custom HTML webpage. Kubernetes manages the application deployment, maintains two replicas, and exposes the application publicly through an AWS LoadBalancer Service.

The deployment uses an existing Amazon EKS cluster and a dedicated Kubernetes namespace to keep the project resources isolated from other workloads.

## Architecture

```text
                 Internet
                    |
                    v
          AWS LoadBalancer Service
                    |
                    v
             Amazon EKS Cluster
              project14-cluster
                    |
          Kubernetes Namespace
                project-14
                    |
             Nginx Deployment
                2 Replicas
                 /     \
                v       v
          Nginx Pod  Nginx Pod
                \       /
                 \     /
              Custom HTML
              app/index.html
```

## Technologies Used

- Amazon Web Services (AWS)
- Amazon Elastic Kubernetes Service (EKS)
- Kubernetes
- kubectl
- Amazon EC2
- Nginx
- Docker Container Image
- YAML
- Linux / Ubuntu

## Project Structure

```text
project-14-app-deployment-using-eks/
│
├── app/
│   └── index.html
│
├── kubernetes/
│   ├── namespace.yaml
│   ├── deployment.yaml
│   ├── service.yaml
│   └── configmap.yaml
│
├── screenshots/
│   └── text.txt
│
├── .gitignore
└── README.md
```

## AWS Infrastructure

| Component | Configuration |
|---|---|
| AWS Region | ap-south-1 (Mumbai) |
| EKS Cluster | project14-cluster |
| Kubernetes Version | 1.32 |
| Node Group | project14-nodes |
| Worker Instance Type | t3.small |
| Desired Worker Nodes | 2 |
| Namespace | project-14 |
| Application | Nginx |
| Replicas | 2 |
| Service Type | LoadBalancer |

**Note:** The project uses an existing EKS cluster. Two healthy worker nodes were used for the application deployment.

## Implementation Steps

### 1. Connect to Amazon EKS

Configure kubectl to communicate with the EKS cluster.

```bash
aws eks update-kubeconfig \
  --region ap-south-1 \
  --name project14-cluster
```

Verify the connection:

```bash
kubectl get nodes
```

### 2. Create Kubernetes Namespace

Apply the namespace configuration:

```bash
kubectl apply -f kubernetes/namespace.yaml
```

Verify:

```bash
kubectl get namespaces
```

### 3. Create ConfigMap

Create a ConfigMap using the custom HTML file.

```bash
kubectl create configmap nginx-html \
  --namespace project-14 \
  --from-file=index.html=app/index.html \
  --dry-run=client -o yaml | kubectl apply -f -
```

Verify:

```bash
kubectl get configmap -n project-14
```

### 4. Deploy the Nginx Application

Apply the Kubernetes Deployment:

```bash
kubectl apply -f kubernetes/deployment.yaml
```

The Deployment creates two Nginx replicas and mounts the custom HTML content from the ConfigMap.

Check deployment status:

```bash
kubectl get deployments -n project-14
```

