---
title: "Docker for Non-Docker People: A Zero-Knowledge Guide"
date: 2026-04-05
category: "DevOps"
tags: ["Docker", "Beginners", "Tutorial", "DevOps", "Containers"]
---

## What Is Docker? (The 30-Second Version)

Docker is like a shipping container for software. Instead of worrying about what operating system, libraries, or dependencies your software needs, you package everything into a **container image** that runs anywhere.

Think of it like this:
- **Image** = A recipe (the instructions)
- **Container** = A cake (the running instance)
- **Docker Hub** = A cookbook store (where you get images)

That's it. You don't need to understand the internals to use it.

---

## Step 1: Install Docker

### Mac
1. Go to [docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop/)
2. Download and install Docker Desktop
3. Open Docker Desktop from Applications
4. Wait for the whale icon to stop animating in your menu bar

### Windows
1. Go to [docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop/)
2. Download and install Docker Desktop
3. Restart your computer when prompted
4. Open Docker Desktop

### Linux
```bash
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker $USER
```
Log out and back in.

### Verify It Works
```bash
docker --version
# Should show something like: Docker version 24.0.x
```

---

## Step 2: Run Your First Tool

Let's start with **PulseCheck** — the simplest tool. It monitors your system's health.

```bash
docker run -d --name pulsecheck -p 8080:8080 \
  ghcr.io/irfancode/pulsecheck:latest
```

Let me break down every part of this command:

| Part | What It Means |
|------|--------------|
| `docker run` | "Start a container" |
| `-d` | "Run in the background (detached)" |
| `--name pulsecheck` | "Call it 'pulsecheck' so I can reference it later" |
| `-p 8080:8080` | "Map port 8080 on my computer to port 8080 in the container" |
| `ghcr.io/...` | "Use this image" |

That's the entire command structure. Every Docker command follows this pattern.

### See It Working

Open your browser and go to **http://localhost:8080**. You should see the PulseCheck dashboard showing your CPU, memory, and disk usage.

---

## Step 3: Common Docker Commands You'll Actually Use

You only need about 10 Docker commands. Here they are:

### Start a container
```bash
docker run -d --name myapp -p 8080:8080 image-name:latest
```

### See running containers
```bash
docker ps
```

### Stop a container
```bash
docker stop pulsecheck
```

### Start a stopped container
```bash
docker start pulsecheck
```

### See container logs
```bash
docker logs pulsecheck
```

### Remove a container
```bash
docker rm pulsecheck
```

### See all containers (including stopped ones)
```bash
docker ps -a
```

### See disk usage
```bash
docker system df
```

### Clean up unused images
```bash
docker system prune
```

### Execute a command inside a running container
```bash
docker exec -it pulsecheck sh
```

That's literally 90% of what you'll ever do with Docker.

---

## Step 4: Understanding Volumes (Persistent Data)

By default, when you delete a container, all its data is deleted too. **Volumes** solve this by storing data outside the container.

```bash
# Without volume — data is lost when container is deleted
docker run -d --name datacore \
  -e POSTGRES_PASSWORD=mypassword \
  -p 5432:5432 \
  ghcr.io/irfancode/datacore:latest

# With volume — data persists even if container is deleted
docker run -d --name datacore \
  -e POSTGRES_PASSWORD=mypassword \
  -v datacore-data:/var/lib/postgresql/data \
  -p 5432:5432 \
  ghcr.io/irfancode/datacore:latest
```

The `-v datacore-data:/var/lib/postgresql/data` part means:
- `datacore-data` = Name of the volume (Docker creates it automatically)
- `/var/lib/postgresql/data` = Where inside the container to mount it

---

## Step 5: Docker Compose (Running Multiple Tools)

When you have multiple containers, typing `docker run` commands gets tedious. **Docker Compose** lets you define everything in a file.

Create a file called `docker-compose.yml`:

```yaml
services:
  monitoring:
    image: ghcr.io/irfancode/pulsecheck:latest
    ports:
      - "8080:8080"

  database:
    image: ghcr.io/irfancode/datacore:latest
    environment:
      - POSTGRES_PASSWORD=mypassword
    volumes:
      - db-data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

  cache:
    image: ghcr.io/irfancode/cachebox:latest
    volumes:
      - cache-data:/data
    ports:
      - "6379:6379"
      - "8081:8080"

volumes:
  db-data:
  cache-data:
```

Then run:
```bash
docker compose up -d
```

All three containers start. To stop them all:
```bash
docker compose down
```

---

## Step 6: Recommended Starter Stack

If you're new to all this, start here:

```bash
# 1. System monitoring
docker run -d --name pulsecheck -p 8080:8080 \
  ghcr.io/irfancode/pulsecheck:latest

# 2. Database (if you need one)
docker run -d --name datacore \
  -e POSTGRES_PASSWORD=changeme \
  -v datacore-data:/var/lib/postgresql/data \
  -p 5432:5432 \
  ghcr.io/irfancode/datacore:latest

# 3. Cache (if you need one)
docker run -d --name cachebox \
  -v cachebox-data:/data \
  -p 6379:6379 -p 8081:8080 \
  ghcr.io/irfancode/cachebox:latest
```

Dashboards:
- http://localhost:8080 — System health
- http://localhost:8081 — Redis management

---

## Troubleshooting

### "Port already in use"
Something else is using that port. Either stop the other service or use a different port:
```bash
docker run -d --name pulsecheck -p 9090:8080 ...
```
Then access at http://localhost:9090

### "Container won't start"
Check the logs:
```bash
docker logs pulsecheck
```

### "How do I update?"
```bash
docker stop pulsecheck
docker rm pulsecheck
docker pull ghcr.io/irfancode/pulsecheck:latest
docker run -d --name pulsecheck -p 8080:8080 ghcr.io/irfancode/pulsecheck:latest
```

### "How do I see what's using disk space?"
```bash
docker system df
```

---

## That's It

You now know enough Docker to run production infrastructure. The Docker Toolkit is designed so that every tool works with the exact patterns described above.

No Kubernetes. No Helm. No operators. Just `docker run`.

---

**GitHub:** [github.com/irfancode/docker-toolkit](https://github.com/irfancode/docker-toolkit)
