# 🐳 Docker – Day 1 (Containerization Basics)

## 📌 What is Containerization?
Containerization is a process of packaging an application along with its source code, libraries, and dependencies into a single lightweight unit called a **container**, so it can run consistently on any environment.

---

## 📌 Why Containerization?
- Solves dependency issues
- Eliminates environment mismatch
- Easy deployment
- Lightweight and fast

---

## 📦 What is a Container?
A container is a lightweight environment that includes:
- Application
- Required OS libraries
- Dependencies

It does **NOT** include a full OS kernel.

---

## 🖥️ Traditional Deployment Problems (OS Issues)
- OS version conflicts
- Hardware resource allocation is difficult
- If OS crashes → all applications go down
- If one application needs changes → other apps may get affected
- Not a good approach for High Availability (HA)

---

## 🧱 Virtualization (Old Approach)
In virtualization:
- Each application needs:
  - Full OS
  - Dependencies
- Uses a **Hypervisor**
- Heavy and slow
- Example:
  - Ubuntu OS (6GB)
  - App dependencies (1GB)
  - Total ≈ 7–8 GB per VM

---

## 🐳 Containerization vs Virtualization

| Feature | Virtualization | Containerization |
|------|---------------|----------------|
| OS | Full OS per VM | Shared OS kernel |
| Size | Very large | Very small |
| Speed | Slow | Fast |
| Resource usage | High | Low |
| Isolation | VM-level | Process-level |

---

## 🧠 Docker Architecture
Docker runs containers on top of:
- Host OS
- Docker Engine
- Shared Kernel

Each container has:
- Application
- App-related dependencies only

Example:
- Ubuntu base image ≈ 30 MB
- App dependencies ≈ 10 MB
- Total ≈ 40 MB

---

## 🧪 Case Study

### Case 1: Deploy 3 Applications (Without Docker)
- Need 3 servers (EC2)
- Each server runs one app
- High cost ❌

### Case 2: Deploy 100 Applications (With Docker)
- Single EC2 instance
- 100 containers
- Low cost ✅
- Better resource utilization

---

## 🛠️ Docker Day-1 Practical Commands

### 1️⃣ Check Docker Version
```bash
docker --version


2️⃣ Start Docker Service
systemctl start docker

3️⃣ Pull Ubuntu Image from Docker Hub
docker pull ubuntu

4️⃣ List Docker Images
docker images

5️⃣ Run Ubuntu Container (Detached Mode)
docker run -dt ubuntu


Runs container in background

No terminal access

6️⃣ Run Ubuntu Container (Interactive Mode)
docker run -it ubuntu


Terminal access available

Default process: /bin/bash

7️⃣ Inside Container – Basic Commands
ls
pwd
whoami


Default user: root

Default directory: /

8️⃣ Install Packages Inside Container
apt update
apt install -y procps sudo

9️⃣ Check Running Processes
ps aux


📌 Note:
A container must have at least one running process.
If no process runs → container stops automatically.

🔟 List Containers
docker ps        # running containers
docker ps -a     # all containers
docker ps -q     # container IDs only

🔚 Exit Container
exit


Stops the container

Exits terminal

✅ Key Takeaways (Very Important)

Container ≠ Virtual Machine

Docker containers are lightweight

Shared OS kernel

Faster deployment

Ideal for DevOps & Cloud

✅ Day-1 Completed 🎉
