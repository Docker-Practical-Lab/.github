# 🐳 Docker Learning Roadmap

## 📌 Progress Tracker

- [ ] **Introduction to Docker**
- [ ] **Core Concepts** (What / Why / When)
- [ ] **Installing Docker**
- [ ] **Docker Engine & CLI Basics**
- [ ] **Images vs Containers**
- [ ] **Building & Running Containers**
- [ ] **Dockerfile & Custom Images**
- [ ] **Volumes & Persistent Data**
- [ ] **Networking & Container Communication**
- [ ] **Docker Compose**
- [ ] **Projects / Hands-on Practice**
- [ ] **Best Practices & Next Steps**

---

## 🧠 1. Introduction to Docker

- What is Docker & what problem it solves.
- **"Works on my machine"** problem → containers give consistency.
- Container vs Virtual Machine vs Bare-metal.

| | Virtual Machine | Docker Container |
|---|---|---|
| OS | Full guest OS per VM | Shares host OS kernel |
| Boot time | Minutes | Milliseconds |
| Size | GBs | MBs |
| Resource use | High | Low |

---

## ⚙️ 2. Core Concepts

| Term | Meaning |
|---|---|
| **Image** | Read-only template / blueprint to run a container |
| **Container** | A running instance of an image (isolated process) |
| **Dockerfile** | Text file with instructions to build an image |
| **Volume** | Persist data outside the container lifecycle |
| **Network** | Lets containers talk to each other & the outside world |
| **Docker Hub** | Public registry to pull/share images |
| **docker-compose** | Define & run multi-container apps with one YAML file |

---

## 🛠️ 3. Installing Docker

- **Windows**: Docker Desktop (WSL2 backend recommended).
- **Linux**: `apt install docker.io` + start/enable `docker` service.
- **macOS**: Docker Desktop.
- Verify install with:

```bash
docker --version
docker run hello-world
```

---

## 💻 4. Docker Engine & CLI Basics

```bash
docker version                # client + server versions
docker info                   # full engine info
docker images                 # list local images
docker ps                     # running containers
docker ps -a                  # all containers (incl. stopped)
docker pull <image>           # download an image
docker rmi <image>            # remove an image
docker system prune           # clean unused data
```

---

## 🖼️ 5. Images vs Containers

- **Image** = the *class / blueprint* (read-only).
- **Container** = the *object / instance* (writable layer on top).

```
Image  ──run──▶  Container (adds writable layer)
                  └── can be started, stopped, removed
```

---

## ▶️ 6. Building & Running Containers

```bash
# Run interactively with a terminal
docker run -it ubuntu bash

# Run in detached (background) mode + map port
docker run -d -p 8080:80 nginx

# Name your container + set env vars
docker run -d --name myapp -e MY_VAR=hello nginx

# Start / stop / remove
docker start myapp
docker stop  myapp
docker rm    myapp

# Attach to running container's shell
docker exec -it myapp bash

# Check logs
docker logs -f myapp
```

---

## 🏗️ 7. Dockerfile & Custom Images

Minimal `Dockerfile` example:

```dockerfile
# Base image
FROM node:18-alpine

# Working directory inside the container
WORKDIR /app

# Copy dependency files first (better caching)
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of the app
COPY . .

# Expose the app's port
EXPOSE 3000

# Command to run on start
CMD ["node", "index.js"]
```

Build & run your own image:

```bash
docker build -t my-app .
docker run -p 3000:3000 my-app
```

---

## 💾 8. Volumes & Persistent Data

Container files are **lost** when the container is removed — use volumes to persist data.

```bash
# Named volume (Docker-managed storage)
docker run -v mydata:/data my-app

# Bind mount (host folder ↔ container folder)
docker run -v $(pwd)/host-folder:/data my-app

# List & remove volumes
docker volume ls
docker volume rm mydata
```

**Layer caching tip**: put frequently-changing files last in the Dockerfile for faster builds.

---

## 🌐 9. Networking & Container Communication

- Every container gets its own isolated network namespace + IP.
- Default networks: `bridge`, `host`, `none`.

```bash
# List networks
docker network ls

# Create a custom bridge network
docker network create mynet

# Run containers on the same network (talk by container name)
docker run -d --network mynet --name api my-api
docker run -d --network mynet --name db  my-db
```

Containers on the same custom network can reach each other using their **container name** as the hostname.

---

## 🚀 10. Docker Compose

Define multi-container apps in a single `docker-compose.yml`:

```yaml
version: "3.9"

services:
  web:
    build: .
    ports:
      - "3000:3000"
    depends_on:
      - db
    environment:
      - DB_HOST=db

  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: secret
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

```bash
docker compose up -d      # build + start all services
docker compose down       # stop & remove
docker compose logs -f    # stream logs
docker compose ps         # status
```

---

## 🧪 11. Projects / Hands-on Practice

- Containerize a simple Node/Express or Python web app.
- Serve static files with `nginx`.
- Run a **web + database** stack with docker-compose.
- Attach a volume so DB data survives container restarts.

---

## 🌱 12. Best Practices & Next Steps

- Use **official / slim (`-alpine`) base images**.
- Keep images small - fewer layers, clean caches.
- Use **`.dockerignore`** to exclude junk files.
- One concern per container.
- Tag images meaningfully: `myapp:1.0`, `myapp:latest`.
- Learn **Kubernetes** next for orchestrating many containers.

---

## 🙌 Happy Docker Learning!

> Good learning! - Practice hands-on, not just theory.
