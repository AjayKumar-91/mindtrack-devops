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

---

# ▶️ Run Locally

```bash
npm install -g serve
serve -s . -l 3000
```

Application runs at:
[http://localhost:3000](http://localhost:3000)

---

# 🐳 Docker Setup

## Build Image

```bash
docker build -t brain-tasks-app .
```

## Run Container

```bash
docker run -d -p 3000:80 brain-tasks-app
```

---

# 📦 DockerHub Push

## Login

```bash
docker login
```

## Tag Image

```bash
docker tag brain-tasks-app username/brain-tasks-app:latest
```

## Push Image

```bash
docker push username/brain-tasks-app:latest
```

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

## Docker Login
docker login

## Run Container

```bash
docker run -d -p 3000:80 --name brain-app ajaykumar91/brain-tasks-app:latest
```

## Access App

http://<EC2-PUBLIC-IP>:3000

---

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

---

# 📸 Submission Checklist

* GitHub repository
* DockerHub image
* EC2 deployed URL
* Screenshots (build, deploy, UI)
* Pipeline success

---

# 🎯 Final Output

✔ React app containerized
✔ Deployed on EC2
✔ CI/CD pipeline working
✔ DockerHub registry integrated
✔ Optional Kubernetes support

