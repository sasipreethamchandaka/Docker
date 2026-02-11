# Docker CMD, ENTRYPOINT, CMD + ENTRYPOINT (Practical Guide)

## 📘 Definitions (Easy Language)

### CMD (Command)

**CMD** is used to define the **default command or arguments** that run when a container starts. It can be **overridden at runtime** using `docker run`.

> Simple words: CMD tells Docker *what to run by default*.

---

### ENTRYPOINT

**ENTRYPOINT** defines the **main fixed command** of the container. Whatever you pass at runtime is treated as an **argument**, not a replacement.

> Simple words: ENTRYPOINT decides *which program must always run*.

---

### CMD + ENTRYPOINT

When **CMD and ENTRYPOINT are used together**, Docker combines them:

```
ENTRYPOINT + CMD
```

* ENTRYPOINT → main command
* CMD → default arguments

> This is the **best practice** for production containers.

---

This guide explains **CMD**, **ENTRYPOINT**, and **CMD + ENTRYPOINT** with **working Docker examples**.

---

## 1️⃣ CMD Example
mkdir CMD

### Dockerfile

```dockerfile
FROM ubuntu
CMD ["echo", "Hello from CMD"]
```

### Build Image

```bash
docker build -t cmd-demo .
```

### Run Container

```bash
docker run cmd-demo
```

**Output**

```
Hello from CMD
```

### Override CMD

```bash
docker run cmd-demo echo "Overridden CMD"
```

**Output**

```
Overridden CMD
```

**Key Point:** CMD is a default command and can be overridden at runtime.

---

## 2️⃣ ENTRYPOINT Example

### Dockerfile

```dockerfile
FROM ubuntu
ENTRYPOINT ["echo"]
```

### Build Image

```bash
docker build -t entry-demo .
```

### Run Container

```bash
docker run entry-demo
```

**Output**

```
(no output because ENTRYPOINT expects arguments)
```

### Try Override

```bash
docker run entry-demo mahima
```

**Output**

```
mahima    #Docker runs internally: echo mahima

```

**Key Point:** ENTRYPOINT is fixed. Arguments are appended; command itself is not replaced easily.

---

## 3️⃣ CMD + ENTRYPOINT (Best Practice)

### Dockerfile

```dockerfile
FROM ubuntu
ENTRYPOINT ["echo"]
CMD ["Hello from CMD"]
```

### Build Image

```bash
docker build -t both-demo .
```

### Run Without Arguments

```bash
docker run both-demo
```

**Output**

```
Hello from CMD
```

### Run With Arguments

```bash
docker run both-demo Hi Mahima
```

**Output**

```
Hi Mahima
```

**Rule:**

* ENTRYPOINT = main fixed command
* CMD = default arguments

---

## 4️⃣ Real Production Example (Node.js)

```dockerfile
FROM node:18
WORKDIR /app
COPY package*.json .
RUN npm install
COPY . .
ENTRYPOINT ["node"]
CMD ["app.js"]
```

### Result

* Default run: `node app.js`
* Override args: `docker run image server.js` → `node server.js`

---

## 5️⃣ Interview Summary

| Feature  | CMD             | ENTRYPOINT      |
| -------- | --------------- | --------------- |
| Override | Easy            | Difficult       |
| Purpose  | Default command | Main executable |
| Best Use | Defaults        | Fixed process   |

**Best Practice:** Use **ENTRYPOINT + CMD** together.
