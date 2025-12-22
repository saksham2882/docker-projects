# Docker Projects & Core Development Reference

This repository contains containerized sample applications along with a comprehensive reference of fundamental Docker concepts, commands, and best practices commonly used across application development.

---

## Repository Structure

- **[flask-app-docker](./flask-app-docker)**: Python Flask web application packaged with Docker.
- **[simple-java-docker](./simple-java-docker)**: Java console application containerized using an Alpine-based OpenJDK environment (`amazoncorretto:17-alpine`).

---

## 1. Core Architecture

| Component | Description |
|---|---|
| **Image** | Read-only template containing application code, runtime, libraries, and dependencies. |
| **Container** | Running, isolated instance created from a Docker image with a read-write layer on top. |
| **Dockerfile** | Text configuration file describing step-by-step instructions to assemble an image. |
| **Volume** | Managed storage mechanism outside container lifecycle used for persistent data. |
| **Network** | Isolated communication channel connecting multiple containers together. |

---

## 2. Dockerfile Directives

```dockerfile
# 1. Base image
FROM python:3.9-slim

# 2. Set active working directory inside container
WORKDIR /app

# 3. Environment variables
ENV PORT=5000 \
    PYTHONUNBUFFERED=1

# 4. Copy files from host to container
COPY requirements.txt .

# 5. Execute build commands (runs during image build time)
RUN pip install --no-cache-dir -r requirements.txt

# 6. Copy remaining source code
COPY . .

# 7. Document the listening port
EXPOSE 5000

# 8. Container entrypoint & default command
CMD ["python", "app.py"]
```

> **Key Rule (`CMD` vs `ENTRYPOINT`)**: `ENTRYPOINT` sets the base executable that should always run, while `CMD` supplies default arguments that can be overridden at runtime via `docker run`.

---

## 3. Essential Docker Commands

### Image Management

```bash
# Build an image from Dockerfile in current directory
docker build -t my-app:latest .

# Build with a custom Dockerfile path
docker build -f Dockerfile.prod -t my-app:prod .

# List local images
docker images

# Tag an image for a remote registry
docker tag my-app:latest username/my-app:1.0.0

# Push / Pull images
docker push username/my-app:1.0.0
docker pull username/my-app:1.0.0

# Remove an image
docker rmi my-app:latest
```

---

### Container Lifecycle & Operations

```bash
# Run in background (detached mode) with port forwarding and custom name
docker run -d --name web-app -p 5000:5000 my-app:latest

# Run interactively with terminal attached
docker run -it --name debug-container ubuntu:22.04 /bin/bash

# Run container and remove it automatically on exit
docker run --rm -p 8080:8080 my-app:latest

# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# View container logs (follow stream with timestamps)
docker logs -f --tail 100 web-app

# Execute a command or access shell inside a running container
docker exec -it web-app /bin/sh

# Stop and start containers
docker stop web-app
docker start web-app
docker restart web-app

# Remove a container (force remove if running)
docker rm -f web-app
```

---

### Storage & Volumes

Containers have ephemeral storage. Any changes to the filesystem are lost when a container is deleted unless persisted using volumes or bind mounts.

```bash
# Named Volume (Docker-managed persistent storage)
docker volume create app-data
docker run -d --name db -v app-data:/var/lib/data postgres:15

# Bind Mount (Directly mounts a directory from host machine into container)
# Ideal for local development live-reloading:
docker run -d --name dev-app -p 5000:5000 -v $(pwd):/app my-app:latest

# Volume management commands
docker volume ls
docker volume inspect app-data
docker volume rm app-data
```

---

### Networking

Containers connected to the same user-defined bridge network can communicate using their container names as hostnames.

```bash
# Create a custom bridge network
docker network create my-network

# Run containers attached to the custom network
docker run -d --name api-service --network my-network my-api:latest
docker run -d --name db-service --network my-network -e POSTGRES_PASSWORD=secret postgres:15

# List and inspect networks
docker network ls
docker network inspect my-network

# Connect/disconnect an existing container to a network
docker network connect my-network existing-container
docker network disconnect my-network existing-container
```

---

### Docker Compose

Docker Compose coordinates multi-container applications using a single YAML declaration file (`docker-compose.yml`).

```bash
# Start all services defined in docker-compose.yml in detached mode
docker compose up -d

# Rebuild images before starting services
docker compose up -d --build

# View logs across all services
docker compose logs -f

# Check status of compose services
docker compose ps

# Stop and remove containers, networks, and internal volumes
docker compose down

# Stop and remove containers including all named volumes
docker compose down -v
```

---

### System Cleanup & Diagnostics

```bash
# Monitor container resource usage (CPU, Memory, Network I/O)
docker stats

# Inspect detailed configuration and IP of a container
docker inspect web-app

# Remove unused stopped containers, dangling images, and build cache
docker system prune

# Comprehensive cleanup (removes stopped containers, all unused images, and unused volumes)
docker system prune -a --volumes
```

---

## 4. Running Included Applications

### Flask Application (`flask-app-docker`)

```bash
# Navigate to application folder
cd flask-app-docker

# Build Docker image
docker build -t flask-docker-app .

# Run container exposing port 5000
docker run -d --name flask-container -p 5000:5000 flask-docker-app

# Verify in browser
curl http://localhost:5000
```

### Java Application (`simple-java-docker`)

```bash
# Navigate to application folder
cd simple-java-docker

# Build Docker image
docker build -t simple-java-app .

# Run container (prints output to console and exits)
docker run --rm simple-java-app
```
