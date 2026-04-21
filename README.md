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




