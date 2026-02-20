# Docker Frontend-Backend Setup with Nginx Proxy

This document contains step-by-step instructions to set up a React frontend and Node.js backend using Docker with Nginx reverse proxy.

## i. Install Prerequisites

Install Docker:
```
sudo yum update -y
sudo yum install -y docker
sudo systemctl start docker
sudo systemctl enable docker
```
Install Git:
```
sudo yum install -y git
```
## ii. Clone Project Repository
```
git clone https://github.com/CloudTechDevOps/2nd10WeeksofCloudOps-main.git
cd 2nd10WeeksofCloudOps-main/
```
---

## Folder Structure
```
2nd10WeeksofCloudOps-main/
│
├── backend/
│   ├── Dockerfile
│   ├── index.js
│   ├── package.json
│   └── package-lock.json
│
├── client/
│   ├── Dockerfile
│   ├── proxy.conf
│   ├── package.json
│   ├── package-lock.json
│   └── src/
│       └── config.js
└── docker-compose.yaml (optional)
```
---

## 1. Backend Setup

- Folder: backend/

### Dockerfile

- Already present, no changes required if backend runs on port 5000.

### index.js

- Make sure backend listens on port 5000:
```
app.listen(5000, () => {
  console.log("Connected to backend on port 5000.");
});
```
### Commands

# Build backend image
```
 docker build -t backend1 .
```

# Remove old container if exists
```
 docker rm -f backend1 2>/dev/null
```

# Run backend container
```
 docker run -d --name backend1 --network app-network -p 5000:5000 backend1
```

# Verify
```
 docker ps
```

---

## 2. Frontend Setup

- Folder: client/

### src/config.js

Change API URL to use relative path via Nginx:
```
// Old: const API_URL = "http://<EC2-IP>:5000";
const API_URL = "/api";
```

### Dockerfile
```
# Stage 1: Build React
FROM node:18 AS build
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm install
COPY . .
RUN npm run build
---------- for a nginx reverse proxy --------
# Stage 2: Serve with Nginx
FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
COPY proxy.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### proxy.conf

    server {
    listen 80;
    server_name _;

    location ^~ /api/ {
        proxy_pass http://backend1:5000/;
        proxy_http_version 1.1;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    root /usr/share/nginx/html;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }
    }

### Commands

# Build frontend image
```
docker build -t frontend1 .
```
# Remove old container if exists
```
docker rm -f frontend1 2>/dev/null
```

# Run frontend container
```
docker run -d --name frontend1 --network app-network -p 81:80 frontend1
```

# Verify
```
docker ps
```

---

## 3. Docker Network

# Create network if not exists
```
docker network create app-network
```

Ensure frontend and backend containers are on the same network so that Nginx can communicate with backend using backend1:5000.

---

## 4. Testing
```
* Open in browser: http://<EC2-PUBLIC-IP>:81
* Frontend React app should load.
* API requests to /api/... should be proxied to backend successfully.
```

---

## 5. Useful Commands

# Check logs
```
docker logs frontend1
```

# Remove container forcefully
```
docker rm -f frontend1 2>/dev/null
```

# Build image
```
docker build -t <image_name> .
```

# Run container
```
docker run -d --name <container_name> --network app-network -p <host_port>:<container_port> <image_name>
```

---

*  Done! Now your frontend and backend are running in Docker with Nginx reverse proxy and working API calls.
*  Result: Frontend and backend are running in Docker with internal communication via Docker network, and Nginx handles API reverse proxy.
