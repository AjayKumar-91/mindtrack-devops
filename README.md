# 🚀 Brain Tasks App - Production Deployment (DevOps Project)

This project demonstrates a complete CI/CD DevOps pipeline for deploying a React application using Docker, AWS EC2, DockerHub, and optional Kubernetes (EKS).

---

# 📌 Project Overview

* Application: React JS (Brain Tasks App)
* Deployment: Docker + EC2 (Primary)
* CI/CD: AWS CodeBuild + CodePipeline
* Registry: DockerHub
* Optional: Kubernetes (EKS)

---
## Create new EC2 Instance 

<img width="1918" height="872" alt="image" src="https://github.com/user-attachments/assets/b8e981f9-eb14-4953-a66e-b25fc91ded3b" />

## Connect EC2 to bash in my local machine

<img width="1918" height="1012" alt="image" src="https://github.com/user-attachments/assets/dff4f1de-0d85-4466-a1b0-f727b828ee70" />

# 🏗 Architecture

GitHub → AWS CodeBuild → DockerHub → EC2 (Docker Runtime)

Optional:
DockerHub → Kubernetes (EKS)

---

# 📥 Clone Repository

```bash
git clone https://github.com/Vennilavanguvi/Brain-Tasks-App.git
cd Brain-Tasks-App
```
<img width="1918" height="172" alt="image" src="https://github.com/user-attachments/assets/b6ce6df7-8fa3-4050-842c-4c7d9e20096d" />

---

# ▶️ Run 

```bash
npm install -g serve
serve -s . -l 3000
```
<img width="1918" height="487" alt="image" src="https://github.com/user-attachments/assets/29682a4c-2dab-4379-8f46-e74766d6e3df" />

<img width="1918" height="130" alt="image" src="https://github.com/user-attachments/assets/0b16cea6-90a1-401e-8b9d-e5bb1771b745" />


# Application runs at:

## added Security Groups

<img width="1918" height="821" alt="image" src="https://github.com/user-attachments/assets/c4c5c839-8c0f-416b-bd4d-091d1d4b7a2d" />

## http://18.207.122.82:3000/

<img width="1918" height="976" alt="image" src="https://github.com/user-attachments/assets/86a2933c-6f55-443a-91c9-b61ab494364b" />

---

# 🐳 Docker Setup

## Build Image

```bash
docker build -t brain-tasks-app .
```
<img width="1918" height="558" alt="image" src="https://github.com/user-attachments/assets/07e48cd9-5eaa-44c0-b232-72c2b4aeb627" />

<img width="1918" height="1012" alt="image" src="https://github.com/user-attachments/assets/0be4c52b-83e7-403e-9296-908a67f926af" />

<img width="1918" height="507" alt="image" src="https://github.com/user-attachments/assets/2ffb0582-6063-4ebb-a50a-5583ab7ed3cd" />

<img width="1918" height="1026" alt="image" src="https://github.com/user-attachments/assets/d4c57001-c3cc-49f0-8f9a-f0c933ac0c7a" />


## Run Container

```bash
docker run -d -p 3000:80 brain-tasks-app
```
<img width="1918" height="288" alt="image" src="https://github.com/user-attachments/assets/e0a4973d-c045-4272-9131-003453eef1cb" />

## After run Dockerfile application running 
<img width="1918" height="967" alt="image" src="https://github.com/user-attachments/assets/6c53f985-8ffe-4a1c-92d9-2fa852f854cd" />

---

# 📦 DockerHub Push

## Login

```bash
docker login
```
## docker login
<img width="1918" height="445" alt="image" src="https://github.com/user-attachments/assets/d88d0fbf-b280-487a-b6e6-eba136c5aaaa" />


## Tag Image

```bash
docker tag brain-tasks-app ajaykumar91/brain-tasks-app:latest
```

## Push Image

```bash
docker push ajaykumar91/brain-tasks-app:latest
```
<img width="1918" height="542" alt="image" src="https://github.com/user-attachments/assets/b4e36dbb-008d-4f68-aac8-1a9d01167b7a" />

<img width="1918" height="1022" alt="image" src="https://github.com/user-attachments/assets/3924483a-7aca-44a7-a2f9-750da04187c5" />



---

# ☁️ EC2 Deployment (Production)

## Install Docker

```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker ubuntu
newgrp docker
```

## Pull Image

```bash
docker pull ajaykumar91/brain-tasks-app:latest
```
<img width="1918" height="172" alt="image" src="https://github.com/user-attachments/assets/b2d45fab-5df7-4bd9-8379-4754cf926340" />

---

# EKS Cluster Setup

```bash
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
curl --silent  --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
sudo apt install zip -y
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```
## Step 1: Create EKS Cluster

### Use eksctl to create the cluster:
```bash
eksctl create cluster \
--name brain-cluster \
--region us-east-1 \
--nodegroup-name brain-nodes \
--node-type t2.micro \
--nodes 2
```
## Step 2: Configure kubectl for EKS

### After cluster creation, configure access:
```bash
aws eks --region us-east-1 update-kubeconfig --name brain-cluster
```
## Step 3: Verify EKS Cluster

### Check if nodes are running:
```bash
kubectl get nodes
```

# ⚙️ CI/CD Pipeline (AWS CodeBuild)

## buildspec.yml

```yaml
version: 0.2

env:
  variables:
    IMAGE_NAME: ajaykumar91/brain-tasks-app
    IMAGE_TAG: latest

phases:

  install:
    commands:
      - echo Using pre-installed Docker
      - docker --version

  pre_build:
    commands:
      - echo Logging into Docker Hub...
      - echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin

  build:
    commands:
      - echo Build started...
      - docker build -t $IMAGE_NAME:$IMAGE_TAG .

  post_build:
    commands:
      - echo Pushing image...
      - docker push $IMAGE_NAME:$IMAGE_TAG

      - echo Updating kubeconfig...
      - aws eks update-kubeconfig --region us-east-1 --name brain-eks-cluster

      - echo Deploying to EKS...
      - kubectl apply -f kubernetes/deployment.yaml
      - kubectl apply -f kubernetes/service.yaml
```

---

# 🔄 Pipeline Flow

GitHub → CodeBuild → DockerHub → EC2 Deployment

---

# ☸️ Kubernetes (Optional)

## Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: brain-app-deployment
  labels:
    app: brain-app-label  
spec:
  replicas: 2
  selector:
    matchLabels:
      app: brain-app-label
  template:
    metadata:
      labels:
        app: brain-app-label
    spec:
      containers:
      - name: brain-app-container
        image: ajaykumar1998/brain-tasks-app:latest
        ports:
        - containerPort: 3000
        resources:
          limits:
            memory: "512Mi"
            cpu: "500m"
          requests:
            memory: "256Mi"
            cpu: "250m"
```

## Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: brain-service
spec:
  type: LoadBalancer
  selector:
    app: brain-app-label
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000
```

---

# 📊 Monitoring

* EC2 logs: docker logs brain-app
* CodeBuild logs: AWS Console
* CloudWatch for pipeline monitoring


<img width="1536" height="1024" alt="ChatGPT Image Apr 21, 2026, 03_00_59 PM" src="https://github.com/user-attachments/assets/bad53501-77da-4f01-9637-9203021962ae" />
