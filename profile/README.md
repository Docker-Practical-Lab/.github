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
- [ ] **Dockerizing Applications** (Node, React, Full Stack)
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

### Commands for Dockerizing Applications

```bash
# 1. Create a Docker image using the Dockerfile
docker build -t image_name .

# 2. See all the images
docker images

# 3. Create a container from that image
docker run --name container_name image_name
docker run --name container_name -d image_name    # detached mode

# 4. See all running containers
docker ps

# 5. See all containers (running and stopped)
docker ps -a

# 6. Stop a running container
docker stop container_name
docker stop container_id

# 7. Restart a container (no need to reconfigure port mappings)
docker start container_name

# 8. Remove a container
docker ps -a
docker container rm CONTAINER_ID_OR_NAME

# Remove multiple containers
docker container rm CONTAINER_ID_OR_NAME CONTAINER_ID_OR_NAME

# 9. Remove a Docker image
docker images
docker image rm IMAGE_ID_OR_TAG

# 10. Remove all containers, images, and volumes
docker system prune -a
```

---

## 💾 8. Volumes & Persistent Data

Container files are **lost** when the container is removed — use volumes to persist data.

### Types of Docker Volumes

#### Anonymous Volumes
Created and managed by Docker but not given a specific name. These volumes are typically used for temporary data that doesn't need to be persisted beyond the container's lifecycle.

```bash
docker run -d --name my_container -v /app/data my_image
```

#### Named Volumes
Created with a specific name and can be referenced by multiple containers. Named volumes are useful for persisting data that needs to be shared between containers or across container restarts.

```bash
docker run -d --name my_container -v my_volume:/app/data my_image
```

#### Host Volumes (Bind Mounts)
Directly map a directory or file on the host to a directory or file in the container. Unlike managed volumes, the host determines where the data is stored. Bind mounts provide more control but less isolation from the host system.

```bash
docker run -d --name my_container -v /path/on/host:/path/in/container my_image
```

### Volume Commands

```bash
# 1. Create a Docker volume
docker volume create my_volume

# 2. Run a Docker container with a volume
docker run -d --name my_container -v my_volume:/app/data my_image

# For development with node_modules optimization
docker run --name container_name --rm -v /app/node_modules -v ${PWD}:/app image_name

# 3. List all Docker volumes
docker volume ls

# 4. Inspect Docker volumes
docker volume inspect my_volume

# 5. Remove a volume
docker volume rm my_volume
```

**Layer caching tip**: put frequently-changing files last in the Dockerfile for faster builds.

**The --rm flag**: Automatically removes the container and its filesystem when the container exits (stops running).

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

### Docker Compose Commands

```bash
docker compose up -d      # build + start all services
docker compose down       # stop & remove
docker compose logs -f    # stream logs
docker compose ps         # status
```

### Docker Compose Interactive Options

The `stdin_open: true` and `tty: true` options in Docker Compose are used to keep the container's standard input (stdin) open and allocate a pseudo-TTY (a terminal) to the container. These options are particularly useful for containers where you need to interact with the shell or command line.

- **stdin_open: true**: Keeps the standard input (stdin) open, even if not attached. Useful for containers where you might want to keep an interactive session open.
- **tty: true**: Allocates a pseudo-TTY, which is a terminal interface. Enables features such as colored output and allows interactive commands to work as if they are run in a regular terminal.

Example with interactive settings:

```yaml
services:
  app:
    build: .
    stdin_open: true
    tty: true
    ports:
      - "3000:3000"
```

---

## 📦 11. Versioning Images

Manage versions by adding a tag to your images:

```bash
# Add a tag to the Docker image
docker build -t image_name:tag .

# Create a container with the specified version
docker run --name container_name -p 3000:4000 image_name:tag
```

---

## 📤 12. Push Images to Docker Hub

Docker Hub is a cloud-based registry service provided by Docker, Inc. It allows you to store, manage, and distribute Docker images.

```bash
# Login to Docker Hub
docker login

# Tag your image with Docker Hub username
docker tag image_name dockerhub_username/image_name:tag

# Push the image to Docker Hub
docker push dockerhub_username/image_name:tag

# Pull an image from Docker Hub
docker pull dockerhub_username/image_name:tag
```

---

## 🔧 13. .dockerignore

The `.dockerignore` file specifies which files and directories should be excluded from the Docker build context. When you build a Docker image, Docker uses a "build context" which includes all files and directories in the current directory (where your Dockerfile resides) and its subdirectories. The `.dockerignore` file helps optimize the build process by preventing unnecessary or sensitive files from being sent to the Docker daemon.

### Example .dockerignore

```
node_modules
npm-debug.log
.git
.gitignore
.env
.env.local
dist
build
.next
.DS_Store
```

---

## 🎯 14. Dockerizing Applications

### React Application

#### Dockerfile
```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 5173

CMD ["npm", "run", "dev"]
```

#### Running React Container
```bash
# Build the image
docker build -t vite-app .

# Run with volume for live reload
docker run --name vite_container \
  -p 3000:5173 \
  --rm \
  -v /app/node_modules \
  -v ${PWD}:/app \
  -e CHOKIDAR_USEPOLLING=true \
  vite-app
```

The `--rm` flag automatically removes the container when it exits.
The `-v ${PWD}:/app` enables live code updates during development.
The `-e CHOKIDAR_USEPOLLING=true` ensures reliable file change detection.

---

### Node/Express Application

#### Dockerfile
```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 5000

CMD ["node", "index.js"]
```

#### Running Node Container
```bash
# Build the image
docker build -t node-app .

# Run with volume for development
docker run --name node_container \
  -p 5000:5000 \
  --rm \
  -v /app/node_modules \
  -v ${PWD}:/app \
  node-app
```

---

### Full Stack Application (MERN)

#### Server Dockerfile
```dockerfile
FROM node:18-alpine

WORKDIR /app/server

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 5000

CMD ["node", "index.js"]
```

#### Client Dockerfile
```dockerfile
FROM node:18-alpine

WORKDIR /app/client

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 5173

CMD ["npm", "run", "dev"]
```

#### Docker Compose for Full Stack
```yaml
version: "3.9"

services:
  server:
    build: ./server
    container_name: mern_server
    ports:
      - "5000:5000"
    environment:
      - MONGODB_URI=mongodb://mongo:27017/mern-db
      - NODE_ENV=development
    depends_on:
      - mongo
    volumes:
      - ./server:/app/server
      - /app/server/node_modules
    stdin_open: true
    tty: true

  client:
    build: ./client
    container_name: mern_client
    ports:
      - "3000:5173"
    depends_on:
      - server
    volumes:
      - ./client:/app/client
      - /app/client/node_modules
    environment:
      - VITE_API_URL=http://localhost:5000
    stdin_open: true
    tty: true

  mongo:
    image: mongo:5.0
    container_name: mern_mongo
    ports:
      - "27017:27017"
    volumes:
      - mongo-data:/data/db
    environment:
      MONGO_INITDB_ROOT_USERNAME: admin
      MONGO_INITDB_ROOT_PASSWORD: password

volumes:
  mongo-data:

networks:
  default:
    name: mern-network
```

#### Running the Full Stack Application
```bash
# Start all services
docker compose up -d

# View logs
docker compose logs -f

# Stop all services
docker compose down

# Stop and remove volumes
docker compose down -v
```

---

## 🧪 15. Projects / Hands-on Practice

- Containerize a simple Node/Express or Python web app.
- Serve static files with `nginx`.
- Run a **web + database** stack with docker-compose.
- Attach a volume so DB data survives container restarts.
- Push your images to Docker Hub and pull them from another machine.
- Build a full-stack MERN application with Docker Compose.

---

## 🌱 16. Best Practices & Next Steps

- Use **official / slim (`-alpine`) base images**.
- Keep images small - fewer layers, clean caches.
- Use **`.dockerignore`** to exclude junk files.
- One concern per container.
- Tag images meaningfully: `myapp:1.0`, `myapp:latest`.
- Copy `package*.json` before the app code for better layer caching.
- Use multi-stage builds for production images to reduce size.
- Always specify a version for base images (`node:18-alpine` instead of `node:latest`).
- Run containers as non-root users for security.
- Learn **Kubernetes** next for orchestrating many containers.

---

## 🙌 Happy Docker Learning!

> Good learning! - Practice hands-on, not just theory.
