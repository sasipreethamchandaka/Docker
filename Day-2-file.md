# Docker Practice – Day 2

> This file is based on **actual commands you practiced**, corrected mistakes, and added the **right flow** so concepts are clear.

---

## 1. Install Docker (Amazon Linux / RHEL based)

```bash
yum install docker -y
systemctl start docker
systemctl enable docker
```

---

## 2. Pull Images from Docker Hub

```bash
docker pull nginx
docker pull ubuntu
docker pull python
```

👉 **Meaning**: `docker pull` downloads an image, it does **NOT run** anything.

---

## 3. List Images

```bash
docker images
```

---

## 4. Inspect Image (Check full image details)

```bash
docker inspect ubuntu
```

👉 Shows base OS, default CMD, ENV, layers etc.

---

## 5. Run Ubuntu Container (Correct Way)

```bash
docker run -it ubuntu
```

* `-i` → interactive
* `-t` → terminal
* Container keeps running because `/bin/bash` is default CMD

---

## 6. Why `docker run -dt ubuntu` works

```bash
docker run -dt ubuntu
```

* `-d` → detached (background)
* `-t` → terminal allocated
* Container runs because **bash is running**

---

## 7. One-time Command (Your earlier mistake fixed)

❌ Wrong (this failed):

```bash
docker run -dt ubuntu "echo hii"
```

✅ Correct:

```bash
docker run ubuntu echo hii
```

👉 `echo` runs once → prints output → container **exits**

---

## 8. Important Concept: CMD Override

* Image CMD → default command
* `docker run IMAGE COMMAND` → overrides CMD

Example:

```bash
docker run -it ubuntu /bin/bash
docker run ubuntu echo hii
```

---

## 9. List Containers

```bash
docker ps      # running only
docker ps -a   # all containers
```

---

## 10. Python Image Practice

### Check Python Version

```bash
docker run python python --version
```

✔ Correct usage (you did this right)

---

## 11. Your Mistake Explained (IMPORTANT)

You ran:

```bash
docker run -dt python python app.py
```

❌ Error:

```
python: can't open file '//app.py'
```

### WHY?

* `app.py` was on **host machine**
* Container **cannot see host files** automatically

---

## 12. Correct Way – Mount File

```bash
docker run -it -v $PWD:/app -w /app python python app.py
```

✔ `-v` → volume mount
✔ `-w` → working directory

---

## 13. Docker Logs

```bash
docker logs <container_id>
```

Used to check **what happened inside container**

---

## 14. Check Processes

```bash
docker ps -q
ps aux | grep python
```

---

---

## 16. Why Dockerfile is Needed

### EC2 Way (Manual ❌)

* Install python
* Install pip
* Install dependencies
* Run app

### Docker Way (Automatic ✅)

* Base image (python)
* Copy code
* Install dependencies
* Run app automatically

---

## 17. Final Goal (Your Note)

> From **image → container creation → app run** should be **automatic**

✔ This is achieved using **Dockerfile**

---

## 18. Summary (Day‑2 Concepts)

* Image vs Container
* CMD override
* Why containers stop
* Volume mounting
* Logs & process check
* Python & Flask in Docker
* Why Dockerfile is required

---

✅ **Day‑2 Completed Successfully**

# I Want to Run Python Application Inside a Container

## How the Project Was Done (Step-by-Step)

### Goal

Run a **Python Flask application inside a Docker container** with full automation.

---

### Step 1: Prepare the Project Folder
git clone https://github.com/CloudTechDevOps/docker_python_flask-project.git
Correct project structure is **mandatory**:

```
docker_python_flask-project/
├── Dockerfile
├── app.py
├── requirements.txt
├── README.md
```
# vi app.py
```
from flask import Flask
import os

app = Flask(__name__)

@app.route("/")
def hello():
    return "updated Flask sample application on azure hghapp service updated verrsion-4"


if __name__ == "__main__":
    port = int(os.environ.get("PORT", 5000))
    app.run(debug=True,host='0.0.0.0',port=port)
```

# vi requirements.txt
```
flask
```


> ⚠️ Common mistake: Dockerfile or requirements.txt placed outside the project folder, which causes files not to be copied into the Docker image.

---

### Step 2: Test Flask App Locally (Without Docker)

```bash
pip3 install -r requirements.txt
python3 app.py
```

Expected output:

```
Running on http://0.0.0.0:5000
```

This confirms:

* app.py is correct
* requirements.txt is correct
* Flask app works properly

Stop the server using **CTRL + C**.

---

### Step 3: Create Dockerfile (Automation Step)

The Dockerfile is responsible for:

* Installing Python
* Installing dependencies
* Running the application automatically

```dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python", "app.py"]
```

---

### Step 4: Build Docker Image

```bash
cd docker_python_flask-project
docker build -t flask-app .
```

✅ The image now contains:

* Python runtime
* Flask and dependencies
* Application code

---

### Step 5: Run Container

```bash
docker run -d -p 5000:5000 flask-app
```

Verify container:

```bash
docker ps
```

Open in browser:

```
http://<EC2_PUBLIC_IP>:5000
```

---

### Step 6: Common Practice Mistakes (And Fixes)

❌ `docker run python python app.py`

* app.py does not exist inside the image by default

❌ Using `-d` without port mapping

* App runs in background but is not accessible

❌ Running `pip install -r requirements.txt` in wrong directory

* File not found error occurs

---

### Final Understanding

* **Dockerfile provides full automation**
* Image = blueprint
* Container = running application
* Flask apps should always be run via Dockerfile, not manual docker run commands

✅ Python Flask application successfully running inside a Docker container


## Container & Image Cleanup (Deletion Commands)

This section documents how to correctly delete containers and images, based on practical usage.

---

### Important Rule

A container must be in **Exited** or **Stopped** state before it can be removed  
(unless force removal is used).

---

### Stop a Running Container
```bash
docker stop <container_id>
Stops a running container safely.

Remove a Stopped Container
docker rm <container_id>
# or
docker rm <container_name>
Removes a container that is already stopped.

Force Remove a Container (Running or Stopped)
docker rm -f <container_id>
Stops and deletes the container in one command.

Remove All Containers
docker rm -f $(docker ps -aq)
Deletes all running and stopped containers.

Remove Only Exited Containers
docker container prune
Deletes containers that are in Exited state.

Delete a Docker Image
docker rmi nginx
Removes the specified image
(only if no container is using it).

Delete All Unused Images, Containers, Networks
docker system prune -a
⚠️ Warning: This removes everything that is not actively in use.

Key Learnings
Running containers cannot be removed without stopping (unless forced)

Images cannot be deleted if containers depend on them

prune commands help clean unused Docker resources
