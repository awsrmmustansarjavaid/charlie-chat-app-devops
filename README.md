# charlie-chat-app-devops

## 🧪 Lab Title: “Real-Time Chat Application with Production-Grade AWS DevOps Pipeline (WebSocket + Nginx + ECS + CI/CD)”

### 🎯 Lab Objectives

By the end, you will:

- Build a real-time chat app (WebSocket + Node.js)

- Configure production-grade Nginx (WebSocket reverse proxy)

- Containerize with Docker

- Push to Amazon Elastic Container Registry

- Deploy on Amazon ECS

- Implement CI/CD with GitHub Actions

- Use Blue/Green deployment

- Simulate Canary deployment

- Use Bash scripts for automation

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

### ⚙️ Phase 0 — Prerequisites

### Install locally:

- Node.js (v18+)

- Docker

- Git

- AWS CLI

### Create accounts:

- AWS account (free tier)

### GitHub account

🔐 Configure AWS CLI

```
aws configure
```

### Enter:

```
Access Key
Secret Key
Region (example: ap-south-1)
Output: json
```

### 🧱 Phase 1 — Build WebSocket Chat App (Node.js)

### Step 1: Create project

```
mkdir chat-app && cd chat-app
npm init -y
npm install ws express
```

### Step 2: Create server.js

```
const express = require("express");
const http = require("http");
const WebSocket = require("ws");

const app = express();
const server = http.createServer(app);
const wss = new WebSocket.Server({ server });

let clients = [];

wss.on("connection", (ws) => {
    clients.push(ws);

    ws.on("message", (message) => {
        clients.forEach(client => {
            if (client !== ws && client.readyState === WebSocket.OPEN) {
                client.send(message.toString());
            }
        });
    });

    ws.on("close", () => {
        clients = clients.filter(c => c !== ws);
    });
});

app.get("/", (req, res) => {
    res.send("WebSocket Chat Server Running");
});

server.listen(3000, () => {
    console.log("Server running on port 3000");
});
```

### Step 3: Run locally

```
node server.js
```

#### Open browser:

```
http://localhost:3000
```

#### ✅ Verify:

- Server runs

- No crash

### 🌐 Phase 2 — Add Nginx (Production WebSocket Proxy)

### Step 1: Create nginx.conf

```
server {
    listen 80;

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

### Step 2: Why this config matters

#### Without:

```
Upgrade + Connection headers
```

👉 WebSocket WILL NOT WORK

### 🐳 Phase 3 — Docker Setup

### Step 1: Create Dockerfile

```
FROM node:18

WORKDIR /app
COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 3000
CMD ["node", "server.js"]
```

### Step 2: Create docker-compose.yml

```
version: "3"

services:
  app:
    build: .
    container_name: chat-app

  nginx:
    image: nginx:latest
    container_name: nginx
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
    depends_on:
      - app
```

### Step 3: Run containers

```
docker-compose up --build
```

### ✅ Verify:

#### Open:

```
http://localhost
```

### 🐙 Phase 4 — Push Code to GitHub

### Step 1: Init repo

```
git init
git add .
git commit -m "initial commit"
```

### Step 2: Push

```
git remote add origin <your-repo-url>
git push -u origin main
```

### 📦 Phase 5 — Push Docker Image to AWS

### Step 1: Create repo in

- Amazon Elastic Container Registry

### Step 2: Login

```
aws ecr get-login-password --region ap-south-1 \
| docker login --username AWS --password-stdin <account>.dkr.ecr.ap-south-1.amazonaws.com
```

### Step 3: Build & push

```
docker build -t chat-app .
docker tag chat-app:latest <ECR-URL>:latest
docker push <ECR-URL>:latest
```

#### ✅ Verify:

- Image visible in ECR

### ☁️ Phase 6 — Deploy to ECS

### Use:

- Amazon ECS

### Step 1: Create Cluster

- Type: EC2 (free tier safe)

### Step 2: Create Task Definition

- Launch type: EC2

- Container:

- Image: ECR image

- Port: 3000

### Step 3: Create Service

- Attach Load Balancer (ALB)

- Port mapping: 80 → 3000

#### ✅ Verify:

- Open ALB DNS → app loads

### 🔁 Phase 7 — CI/CD with GitHub Actions

### Use

- GitHub Actions

### Step 1: Add secrets in GitHub

- AWS_ACCESS_KEY

- AWS_SECRET_KEY

- AWS_REGION

### Step 2: Create workflow

.github/workflows/deploy.yml

```
name: Deploy

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3

    - name: Build Docker
      run: docker build -t chat-app .

    - name: Login ECR
      run: aws ecr get-login-password ...

    - name: Push
      run: docker push ...

    - name: Deploy ECS
      run: aws ecs update-service --force-new-deployment ...
```

#### ✅ Verify:

- Push code → auto deploy

### 🔵🟢 Phase 8 — Blue/Green Deployment

### Use:

- AWS CodeDeploy

### Steps:

#### Create 2 target groups:

- Blue (live)

- Green (new)

- Attach to ALB

- Configure CodeDeploy

### Flow:

```
Deploy → Green → Test → Switch Traffic → Done
```

### 🟡 Phase 9 — Canary Deployment

### Simulate using:

- ALB weighted routing

#### Example:

- 90% → old version

- 10% → new version

### 🧾 Phase 10 — Bash Deployment Script

```
#!/bin/bash

echo "Build"
docker build -t chat-app .

echo "Push"
docker push $ECR_URI

echo "Deploy"
aws ecs update-service \
  --cluster chat-cluster \
  --service chat-service \
  --force-new-deployment
```

### 📁 Final GitHub Structure

```
chat-app-devops/
│
├── server.js
├── package.json
├── nginx.conf
├── Dockerfile
├── docker-compose.yml
│
├── .github/workflows/deploy.yml
├── scripts/deploy.sh
│
└── README.md
```















