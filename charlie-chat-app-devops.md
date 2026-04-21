# charlie-chat-app-devops

## 🧱 Phase-wise Roadmap (IMPORTANT)

## 🔹 Phase 1 – Local App (Foundation)

Don’t touch AWS yet

### Task:

- Create WebSocket chat server using:

    - Node.js

    - ws library

- Features:

    - Multi-user chat

    - Broadcast messages

    - Username support

## 🔹 Phase 2 – Production Nginx (WebSocket)

Here is a real production-ready Nginx config 👇

```
server {
    listen 80;
    server_name your-domain.com;

    location / {
        proxy_pass http://app:3000;

        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "Upgrade";

        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

#### Why this matters:

- Upgrade header → required for WebSocket

- Connection "Upgrade" → keeps connection alive

- Without this → WebSocket breaks silently

## 🔹 Phase 3 – Dockerization

### Dockerfile (Node app)

```
FROM node:18

WORKDIR /app
COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 3000
CMD ["node", "server.js"]
```

### docker-compose (local testing)

```
version: "3"

services:
  app:
    build: .
    ports:
      - "3000:3000"

  nginx:
    image: nginx:latest
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
```

## 🔹 Phase 4 – Push to AWS

### Steps:

- Create repo in
👉 Amazon Elastic Container Registry

- Login & push:

```
aws ecr get-login-password | docker login ...

docker build -t chat-app .
docker tag chat-app:latest <ECR-URL>
docker push <ECR-URL>
```

## 🔹 Phase 5 – Deploy to ECS (Free Tier Friendly)

### Use:

- Amazon ECS (Fargate optional but EC2 cheaper in free tier)

- Task Definition

- Service

### Architecture:

```
User → ALB → ECS → Nginx → Node.js (WebSocket)
```

## 🔹 Phase 6 – CI/CD with GitHub Actions

### Workflow file:

```
name: Deploy Chat App

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout
      uses: actions/checkout@v3

    - name: Build Docker
      run: docker build -t chat-app .

    - name: Push to ECR
      run: |
        aws ecr get-login-password ...
        docker push ...

    - name: Deploy to ECS
      run: |
        aws ecs update-service ...
```

## 🔹 Phase 7 – Blue/Green Deployment

### Use:

- AWS CodeDeploy

- ALB with 2 target groups:

    - Blue (current)

    - Green (new)

### Flow:

```
Deploy → Green → Test → Switch Traffic → Blue backup
```

## 🔹 Phase 8 – Canary Deployment (Simulation)

### ECS doesn’t do native Canary easily → simulate via:

- ALB weighted routing

- OR Route 53

#### Example:

- 90% → old version

- 10% → new version

## 🔹 Phase 9 – Bash Automation Script

```
#!/bin/bash

echo "Building Docker image..."
docker build -t chat-app .

echo "Tagging image..."
docker tag chat-app:latest $ECR_URI

echo "Pushing to ECR..."
docker push $ECR_URI

echo "Updating ECS service..."
aws ecs update-service \
  --cluster chat-cluster \
  --service chat-service \
  --force-new-deployment
```

### 📁 GitHub Repo Structure (Clean & Professional)

```
chat-app-devops/
│
├── app/
│   ├── server.js
│   ├── package.json
│
├── nginx/
│   └── nginx.conf
│
├── docker/
│   ├── Dockerfile
│   └── docker-compose.yml
│
├── .github/
│   └── workflows/
│       └── deploy.yml
│
├── scripts/
│   └── deploy.sh
│
├── k8s/              # optional (advanced phase)
│   ├── deployment.yaml
│   ├── service.yaml
│
└── README.md
```


