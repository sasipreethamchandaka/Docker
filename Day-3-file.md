# Day 3 - Docker & Flask Setup on EC2

## 1. Project Files

* `app.py` : Flask application
* `requirements.txt` : Python dependencies
* `Dockerfile` : Docker image build instructions
* `README.md` : Project information
* Other folders: `deploy+process_ec2_process`, `nginx-process`

---

## 2. Flask Application (`app.py`)

```python
from flask import Flask
import os

app = Flask(__name__)

@app.route("/")
def hello():
    return "updated Flask sample application on azure highapp service updated verrsion-4"

if __name__ == "__main__":
    port = int(os.environ.get("PORT", 5000))
    app.run(debug=True, host='0.0.0.0', port=port)
```

* App runs on `host='0.0.0.0'` for external access
* Uses environment variable `PORT` for dynamic port assignment

---

## 3. Dockerfile

```dockerfile
FROM ubuntu:22.04
COPY . /app
WORKDIR /app
RUN apt-get update && \
    apt-get install -y python3-pip && \
    rm -rf /var/lib/apt/lists/* && \
    pip install -r requirements.txt
CMD ["python3", "app.py"]
```

* Base image: Ubuntu 22.04
* Copies project files to `/app`
* Installs Python & dependencies from `requirements.txt`
* Container starts Flask app on startup

---

## 4. Docker Commands

**Build Image:**

```bash
docker build -t flaskapp:latest .
```

**Run Container:**

```bash
docker run -d -p 5000:5000 flaskapp
```

* `-d` : detached mode
* `-p host_port:container_port` : map host port to container port

**Multiple Containers on Same Host:**

```bash
docker run -d -p 5001:5000 flaskapp
docker run -d -p 5002:5000 flaskapp
```

---

## 5. Issues Faced & Fixes

1. **Dockerfile COPY error**

```
COPY requires at least two arguments
```

* Fix: `COPY file1 /app` → destination `/app` add them

2. **apt install python3 failed**

```
apt install python3 -y
```

* Fix: `apt update` add them before install

```dockerfile
RUN apt update && apt install -y python3
```

3. **requirements.txt not found**

```
Could not open requirements file: [Errno 2] No such file or directory
```

* Fix: Dockerfile me `COPY requirements.txt /app` add them

4. **Container run failed (port already allocated)**

```
Bind for 0.0.0.0:5000 failed: port is already allocated
```

* Fix: Existing container stop & remove it

```bash
docker ps
docker stop <container_id>
docker rm <container_id>
```

* Separate host ports for multiple containers: `-p 5001:5000`
5. **Flask app not accessible externally**

* Fix: run Flask app with `host="0.0.0.0"`
* * EC2 Security Group me port 5000-5002 TCP open karo

6. **docker run with '.' error**

```
exec: ".": executable file not found
```

* Fix: Run container with bash or CMD:

```bash
docker run -it <image_name> /bin/bash
```

---

## 6. EC2 Access

**Flask app URL:**

```
http://<EC2-Public-IP>:5000
http://<EC2-Public-IP>:5001
```

**Test with curl:**

```bash
curl http://<EC2-Public-IP>:5000
```

---

## 7. Notes

* Enter container interactive shell:

```bash
docker run -it <image_name> /bin/bash
```

* Clean up old containers to avoid port conflicts
* Use `docker ps -a` to check all containers
