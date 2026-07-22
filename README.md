# Three-Tier Application Deployment on AWS EKS

## Overview

This project demonstrates the deployment of a Three-Tier Web Application on AWS EKS using Kubernetes.

The application consists of three layers:

- Frontend (React.js)
- Backend (Node.js + Express.js)
- Database (MongoDB)

The application is containerized using Docker and deployed on Kubernetes with AWS Load Balancer for external access.

---

## Project Structure

- Application Code
- Jenkins Pipeline Code
- Kubernetes Manifests Files

---

## Technologies Used

### Cloud
- AWS EC2
- AWS EKS
- AWS IAM
- AWS Load Balancer

### DevOps
- Docker
- Kubernetes
- Jenkins
- kubectl
- eksctl
- AWS CLI

### Application
- React.js
- Node.js
- Express.js
- MongoDB

---

# Getting Started

## Step 1: IAM Configuration

Create an IAM user with AdministratorAccess and generate an Access Key and Secret Access Key.

---

## Step 2: Launch EC2 Instance

Launch an Ubuntu EC2 instance and connect using SSH.

---

## Step 3: Install AWS CLI

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install unzip
unzip awscliv2.zip
sudo ./aws/install -i /usr/local/aws-cli -b /usr/local/bin --update
aws configure
```

---

## Step 4: Install Docker

```bash
sudo apt update
sudo apt install docker.io
sudo chown $USER /var/run/docker.sock
docker ps
```

---

## Step 5: Install kubectl

```bash
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl

chmod +x kubectl

sudo mv kubectl /usr/local/bin
    
kubectl version --client
```

---

## Step 6: Install eksctl

```bash
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp

sudo mv /tmp/eksctl /usr/local/bin

eksctl version
```

---

## Step 7: Create EKS Cluster

```bash
eksctl create cluster \
--name three-tier-cluster \
--region us-west-2 \
--node-type t2.medium \
--nodes-min 2 \
--nodes-max 2

aws eks update-kubeconfig \
--region us-west-2 \
--name three-tier-cluster

kubectl get nodes
```

---

## Step 8: Deploy Kubernetes Manifests

```bash
kubectl create namespace workshop

kubectl apply -f .

kubectl get pods

kubectl get svc

kubectl get ingress
```

---

## Step 9: Configure AWS Load Balancer Controller

```bash
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/install/iam_policy.json

aws iam create-policy \
--policy-name AWSLoadBalancerControllerIAMPolicy \
--policy-document file://iam_policy.json

eksctl utils associate-iam-oidc-provider \
--region us-west-2 \
--cluster three-tier-cluster \
--approve

eksctl create iamserviceaccount \
--cluster three-tier-cluster \
--namespace kube-system \
--name aws-load-balancer-controller \
--role-name AmazonEKSLoadBalancerControllerRole \
--attach-policy-arn=<YOUR_POLICY_ARN> \
--approve \
--region us-west-2
```

---

## Step 10: Deploy AWS Load Balancer Controller

```bash
sudo snap install helm --classic

helm repo add eks https://aws.github.io/eks-charts

helm repo update

helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
-n kube-system \
--set clusterName=three-tier-cluster \
--set serviceAccount.create=false \
--set serviceAccount.name=aws-load-balancer-controller

kubectl get deployment -n kube-system aws-load-balancer-controller

kubectl apply -f full_stack_lb.yaml
```

---

# Cleanup

Delete the EKS cluster when it is no longer required.

```bash
eksctl delete cluster \
--name three-tier-cluster \
--region us-west-2
```

Also delete:

- EC2 Instance
- AWS Load Balancer
- Unused Security Groups

---

## Features

- Three-Tier Architecture
- Dockerized Application
- Kubernetes Deployment
- AWS EKS
- AWS Load Balancer
- MongoDB Database
- React Frontend
- Node.js Backend
- Scalable Kubernetes Deployments

---

## Author

**Shaikh Arman**

Aspiring DevOps Engineer
