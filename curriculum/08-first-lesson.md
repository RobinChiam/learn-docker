# 8. First Lesson — Complete Beginner Start

**Module:** 01 — Container Foundations  
**Difficulty:** Beginner  
**Duration:** 60–90 minutes  
**Prerequisites:** Terminal access; Docker Desktop or Docker Engine installed (or install during this lesson)

This lesson is self-contained. You can complete it even if the local `welcome-to-docker/` folder is empty.

---

## Instructor explanation — What is a container?

A **container** is a lightweight, isolated environment that runs your application and the libraries it needs. Think of it as a **standardized shipping box for software**: once the box is packed correctly, ships, laptops, and servers that understand the format can run it the same way.

Containers share the host’s operating-system kernel. They do not each need a full guest operating system the way traditional virtual machines do.

---

## Containers versus virtual machines

| | Virtual machine | Container |
|--|-----------------|-----------|
| Isolation unit | Full virtual hardware + guest OS | Isolated processes + filesystem view |
| Size | Often many GB | Often tens–hundreds of MB (varies) |
| Start time | Slower | Faster |
| Analogy | Separate house | Locked room in a shared building |

Both are useful. Containers shine for packaging apps consistently for development and deployment. VMs shine when you need a completely different OS or stronger isolation boundaries.

---

## Docker images versus containers

| Term | Meaning | Analogy |
|------|---------|---------|
| **Image** | Immutable template (files + metadata + default command) | Class / cookie cutter / Blu-ray disc |
| **Container** | Instance created from an image (running or stopped) | Object / cookie / playing the disc |

You can create many containers from one image.

---

## Docker client and daemon

```text
┌─────────────┐     API      ┌─────────────────┐
│ docker CLI  │ ───────────► │ Docker daemon   │
│ (client)    │              │ (dockerd)       │
└─────────────┘              └────────┬────────┘
                                      │
                                      ▼
                               containers / images
```

- You type commands into the **client**.
- The **daemon** does the work: pull images, create networks, start containers.
- If the daemon is stopped, the client fails with a connection error.

---

## Registry basics

A **registry** stores images. **Docker Hub** is the default public registry.

This lesson uses the official learning image:

```text
docker/welcome-to-docker
│      └── image name
└── namespace (Docker’s official account)
```

That image is the same sample this repository intends to study via the `welcome-to-docker` gitlink.

---

## Installation verification

### Learner exercise — Verify Docker

**Instructor note:** Installation steps differ by OS. Prefer Docker’s current official docs for Desktop (Mac/Windows) or Engine (Linux).

After install:

```bash
docker version
```

**Expected:** Output includes **Client** and **Server** sections with version numbers.

```bash
docker info
```

**Expected:** A long summary (storage driver, OS, containers count, etc.) without a connection error.

If Server is missing or you see `Cannot connect to the Docker daemon`:

1. Start Docker Desktop, **or**
2. On Linux, start the Docker service (method depends on your distro).

---

## Your first safe container command

### Instructor explanation

We will run a pre-built image from Docker Hub. We will not build anything yet. We will map a port so your browser can reach the app, and we will name the container so it is easy to stop later.

### Learner exercise — Run the welcome container

```bash
docker run -d -p 8088:80 --name welcome-to-docker docker/welcome-to-docker
```

### Explanation of every part

| Part | Role |
|------|------|
| `docker` | The Docker client CLI |
| `run` | Create a new container and start it |
| `-d` | **Detached** — run in the background; return your terminal |
| `-p 8088:80` | **Publish** host port `8088` to container port `80` |
| `--name welcome-to-docker` | Assign a friendly name instead of a random hash name |
| `docker/welcome-to-docker` | Image to use (pulled from Hub if not already local) |

**Why port 80?** The published Hub image serves HTTP on port **80** inside the container. (Later, when you **build from source**, the sample Dockerfile serves on **3000**. Do not mix them up.)

### Expected output

- The command prints a long container ID (hex string).
- Your shell returns immediately (because of `-d`).

Then:

```bash
docker ps
```

**Expected (shape, not exact IDs):** a row with names like `welcome-to-docker`, image `docker/welcome-to-docker`, and ports like `0.0.0.0:8088->80/tcp`.

Open a browser to:

```text
http://localhost:8088
```

Or:

```bash
curl -I http://localhost:8088
```

**Expected:** A congratulations page about running your first container (or HTTP 200 headers from curl). Exact HTML may change over time — do not memorize byte-for-byte output.

---

## Cleanup instructions

Always clean up named lab containers so names do not conflict later.

```bash
# ⚠️ Stops the running process gracefully
docker stop welcome-to-docker

# 🛑 Removes the container record (does not delete the image)
docker rm welcome-to-docker
```

Verify:

```bash
docker ps -a
```

You should not see `welcome-to-docker` anymore. The **image** may still appear in `docker image ls` — that is normal and useful for the next lesson.

---

## Repository observation exercise

**Label: repository-specific**

Even if `welcome-to-docker/` is empty on disk, observe the parent repository:

1. Open `/README.md` (repo root).
2. Note the stated learning goals: Docker setup, volumes, containerization.
3. List the top-level entries you see (`README.md`, `welcome-to-docker`, `curriculum/` once added).
4. Write one sentence: “This repository is a learning shell around Docker’s welcome sample.”

**Assumption to record:** Local `welcome-to-docker` may be an uninitialized git submodule pointing at [`docker/welcome-to-docker`](https://github.com/docker/welcome-to-docker). Module 03 will obtain source for builds. Module 01 only needs the Hub image.

Optional peek at upstream Dockerfile online or after clone — notice `EXPOSE 3000` for **source builds**, contrasting with Hub’s port **80**.

---

## Five knowledge-check questions

1. In your own words, what problem do containers solve?
2. How is a container different from a virtual machine?
3. What is the difference between an image and a container?
4. What does the Docker daemon do?
5. In `-p 8088:80`, which number do you type in your browser’s URL?

**Brief answers**

1. Consistent packaging/runtime so apps run the same across machines.  
2. Containers share the host kernel; VMs virtualize hardware and run a guest OS.  
3. Image = template; container = instance.  
4. It builds/runs/manages containers and images on the host.  
5. `8088` (the host port).

---

## One practical challenge

1. Run the Hub image again so it is reachable at **`http://localhost:9000`** (hint: change only the **host** side of `-p`).
2. Confirm with `docker ps` that the mapping shows `9000->80`.
3. Open the page or `curl -I http://localhost:9000`.
4. Stop and remove **that** container.
5. Confirm cleanup with `docker ps -a`.

---

## Clear completion criteria

You are done with Lesson 1 when all of the following are true:

- [ ] You can explain containers vs VMs without reading the table.
- [ ] `docker version` shows both Client and Server.
- [ ] You successfully viewed the congratulations app via a published port.
- [ ] You can explain every token of the `docker run ... docker/welcome-to-docker` command.
- [ ] You stopped and removed your lab container.
- [ ] You completed the repository observation note.
- [ ] You finished the practical challenge on port 9000.

**Next:** [Module 02 — Docker CLI Fundamentals](modules/02-cli-fundamentals.md)
