# Module 01 — Container Foundations

| Field | Value |
|-------|-------|
| **Difficulty** | Beginner |
| **Estimated study duration** | 1.5–2 hours |
| **Prerequisites** | Ability to open a terminal; no Docker experience required |

> The complete narrative version of this module is also available as **[First Lesson](../08-first-lesson.md)** for immediate start.

---

## Learning objectives

By the end of this module you will be able to:

1. Explain what a container is in plain language.
2. Contrast containers with virtual machines.
3. Define image, container, client, daemon, and registry.
4. Verify that Docker is installed.
5. Run your first container from Docker Hub.
6. Stop and remove that container safely.

---

## Concepts explained (beginner-friendly)

### The problem containers solve

Installing software the old way often means: “It works on my machine.” Different OS versions, library versions, and missing tools break apps. Containers package the **application plus its runtime dependencies** so the same unit runs the same way on another computer that has Docker.

### Containers versus virtual machines

| | Virtual machine | Container |
|--|-----------------|-----------|
| What it virtualizes | Hardware (full guest OS) | Process isolation on a shared host kernel |
| Typical size | Gigabytes | Megabytes to low gigabytes |
| Startup | Seconds to minutes | Usually seconds or less |
| Isolation | Strong (separate OS) | Strong enough for many apps; still shares host kernel |
| Analogy | Renting an entire apartment building floor | Renting a furnished room in a shared building |

Containers are **not** tiny VMs. They share the host’s Linux kernel (on Windows/Mac, Docker runs a small Linux VM under the hood so containers still see Linux).

### Docker terminology

| Term | Plain meaning |
|------|----------------|
| **Image** | Read-only recipe/template (filesystem + metadata + default command) |
| **Container** | A running (or stopped) instance created from an image |
| **Dockerfile** | Text file of build instructions that produces an image |
| **Registry** | Library of images (Docker Hub is the default public one) |
| **Docker client** | The `docker` CLI (or Desktop UI) you type into |
| **Docker daemon** | Background engine (`dockerd`) that builds/runs containers |
| **Tag** | Version label on an image, e.g. `node:22-alpine` |
| **Layer** | A filesystem diff stacked to form an image |

**Analogy:** An image is a cookie cutter; a container is a cookie. You can stamp many cookies from one cutter.

### Docker architecture (mental model)

```text
You → docker CLI (client) → Docker daemon → containers
                              ↓
                         pulls/pushes
                              ↓
                         Registry (Docker Hub, etc.)
```

---

## Repository-specific relevance

This repo’s learning goal matches Module 01 exactly. Upstream docs recommend:

```bash
docker run -d -p 8088:80 --name welcome-to-docker docker/welcome-to-docker
```

That is your **first repository-shaped container**. Note the Hub image listens on **port 80** inside the container.

**Assumption:** If `welcome-to-docker/` is empty locally, you can still complete Module 01 using only the Hub image — no source checkout required yet.

---

## Commands introduced

| Command | Purpose |
|---------|---------|
| `docker version` | Show client/server versions |
| `docker info` | Show daemon configuration summary |
| `docker run` | Create and start a container from an image |
| `docker ps` | List running containers |
| `docker stop` | Gracefully stop a container |
| `docker rm` | Remove a stopped container |

---

## Guided demonstration

### Instructor explanation

1. Confirm Docker talks to a running daemon.
2. Run the official welcome image in the background.
3. Confirm it is listed and reachable.
4. Stop and remove it so your machine stays clean.

### Demo commands

```bash
docker version
docker info
```

You should see both **Client** and **Server** sections. If Server is missing, the daemon is not running (start Docker Desktop or the Docker service).

```bash
# Repository-specific: Hub image (container port 80)
docker run -d -p 8088:80 --name welcome-to-docker docker/welcome-to-docker
```

| Part | Meaning |
|------|---------|
| `docker run` | Create + start container |
| `-d` | Detached (background) |
| `-p 8088:80` | Map host 8088 → container 80 |
| `--name welcome-to-docker` | Human-friendly name |
| `docker/welcome-to-docker` | Image on Docker Hub (`namespace/name`) |

```bash
docker ps
```

Open a browser to `http://localhost:8088` (or `curl http://localhost:8088`). Expect a congratulations page.

### Cleanup

```bash
# ⚠️ Stops the running container
docker stop welcome-to-docker

# 🛑 Removes the container (not the image)
docker rm welcome-to-docker
```

---

## Hands-on exercises

1. Run `docker version` and write down client vs server version numbers.
2. Start `welcome-to-docker` again with a **different host port**, e.g. `-p 8090:80`.
3. Confirm with `docker ps` that the port mapping shows `0.0.0.0:8090->80/tcp`.
4. Stop and remove the container.

---

## Expected results

- Docker client and server respond.
- Container appears in `docker ps` while running.
- Browser/curl shows the congratulations UI.
- After `rm`, `docker ps -a` no longer lists that name.

---

## Common mistakes

| Mistake | Symptom | Fix |
|---------|---------|-----|
| Daemon not running | `Cannot connect to the Docker daemon` | Start Docker Desktop / service |
| Port already in use | `bind: address already in use` | Choose another host port or stop the conflicting process |
| Using `:3000` with Hub image | Connection refused / wrong page | Hub image uses **80**, not 3000 |
| Reusing `--name` | `Conflict. The container name ... is already in use` | `docker rm` old container or pick a new name |

---

## Troubleshooting guidance

1. **Hypothesis:** Docker daemon down → verify with `docker info`.
2. **Hypothesis:** Wrong port → check `docker ps` PORTS column.
3. **Hypothesis:** Container exited → `docker ps -a` then `docker logs welcome-to-docker`.

---

## Knowledge-check questions

1. What is the difference between an image and a container?
2. Who does the work of actually running containers — client or daemon?
3. What does `-p 8088:80` mean, left side vs right side?
4. Why might a container be lighter than a VM?
5. What does a registry store?

*(Answers in [assessments/quiz-01.md](../assessments/quiz-01.md).)*

---

## Practical challenge

Run the Hub image so it is available at **`http://localhost:9000`**, confirm it works, then fully clean up the container. Leave the image on disk for Module 02.

---

## Completion criteria

- [ ] You can explain containers vs VMs in your own words.
- [ ] `docker version` shows Client and Server.
- [ ] You successfully opened the welcome page via a published port.
- [ ] You stopped and removed the container without deleting unrelated containers.

---

## Optional stretch exercise

Compare `docker run hello-world` (prints a message and exits) with `welcome-to-docker` (keeps running). Note which one disappears from `docker ps` immediately and why.
