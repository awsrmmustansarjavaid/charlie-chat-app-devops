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





