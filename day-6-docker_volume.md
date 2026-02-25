# Docker Volume – Complete Practical Guide

## 📌 What is Docker Volume?

Docker Volume is a persistent storage mechanism used to store data outside the container lifecycle.

Containers are **ephemeral (temporary)**.  
If a container is deleted, its internal data is lost.

Volumes solve this problem by storing data on the host machine.

---

## 🧠 Why We Use Docker Volumes?

- To prevent data loss
- To persist database data
- To store logs permanently
- To maintain data during container recreation
- To support CI/CD redeployments

---

## 🏗 Without Volume

Container → Data  
Delete Container → ❌ Data Lost  

---

## 🏗 With Volume

Container → Volume → Host Storage  
Delete Container → ✅ Data Safe  

---

# 🔹 Step-by-Step Practical

## 1️⃣ Create a Volume

```bash
docker volume create practice-volume
```

Check volumes:

```bash
docker volume ls
```

---

## 2️⃣ Run Container with Volume

```bash
docker run -it --name volume-test -v practice-volume:/data ubuntu
```

Inside container:

```bash
cd /data
echo "Docker Volume Test" > file.txt
ls
exit
```

---

## 3️⃣ Delete Container

```bash
docker rm volume-test
```

---

## 4️⃣ Run Again with Same Volume

```bash
docker run -it --name volume-test2 -v practice-volume:/data ubuntu
```

Check:

```bash
cd /data
ls
cat file.txt
```

✅ File will still exist.

---

## 🔍 Where Docker Stores Volume Data?

Check volume details:

```bash
docker volume inspect practice-volume
```

You will see something like:

```
Mountpoint: /var/lib/docker/volumes/practice-volume/_data
```

Check host path directly:

```bash
ls /var/lib/docker/volumes/practice-volume/_data
```

---

## 🗑 Remove Volume

⚠️ Make sure container is removed first.

```bash
docker rm volume-test2
docker volume rm practice-volume
```

---

# 🔥 Important Docker Volume Commands

| Purpose | Command |
|----------|----------|
| List volumes | `docker volume ls` |
| Create volume | `docker volume create name` |
| Inspect volume | `docker volume inspect name` |
| Remove volume | `docker volume rm name` |
| Remove unused volumes | `docker volume prune` |

---

# 📦 Named Volume vs Bind Mount

## ✅ Named Volume (Recommended)

```bash
-v volume-name:/container-path
```

Docker manages storage automatically.

## ✅ Bind Mount

```bash
-v /host/path:/container/path
```

Uses a specific host directory.

---

# 🚀 CI/CD Use Case

In Jenkins pipeline:

Every deployment:
- Container is removed
- Container is recreated

Without volume → ❌ Data loss  
With volume → ✅ Data persists  

---

# 🎯 Interview One-Line Answer

Docker volumes are used to persist container data by storing it outside the container lifecycle, ensuring data safety even if the container is deleted or recreated.

---

# ✅ Summary

| Action | Data Status |
|--------|------------|
| Container Restart | Safe |
| Container Delete | Safe |
| Volume Delete | Lost |

---

💡 Practice this with:
- Ubuntu container
- MySQL container
- Jenkins pipeline deployment
