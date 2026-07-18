# Module 05 — Docker Networking

| Field | Value |
|-------|-------|
| **Difficulty** | Advanced beginner → Lower intermediate |
| **Estimated study duration** | 2–3 hours |
| **Prerequisites** | Modules 01–04 |

---

## Learning objectives

1. Explain default bridge vs user-defined bridge networks.
2. Distinguish **EXPOSE**, **publish (`-p`)**, and **container-to-container** connectivity.
3. Use container DNS / service names on user-defined networks.
4. Avoid the `localhost` trap inside containers.
5. Connect at least two containers related to this learning project.

---

## Concepts explained

### Fundamentals

Containers on a Docker network can reach each other by **IP** and, on user-defined bridges, by **name** (embedded DNS).

| Network type | DNS by name? | Typical use |
|--------------|--------------|-------------|
| Default `bridge` | No (legacy links only) | Quick single containers |
| User-defined bridge | **Yes** | Multi-container apps |
| `host` | N/A (shares host net) | Special cases |
| `none` | No networking | Locked down |

### Expose vs publish

| Concept | What it does |
|---------|--------------|
| `EXPOSE` in Dockerfile | Documents intent; does not open host ports |
| `-p HOST:CONTAINER` | Publishes to the host (your browser) |
| Same Docker network | Containers talk on **container ports** without `-p` |

You only need `-p` for **host access**. Service-to-service traffic uses the container port and the service/container name.

### Host port vs container port

Example for built sample:

```text
-p 8088:3000
   │     │
   │     └── container listens here (serve)
   └── your laptop browser uses this
```

Hub image example:

```text
-p 8088:80
```

### The localhost trap

Inside container A, `localhost` means **A itself**, not your laptop and not container B.

| From | Use |
|------|-----|
| Browser on laptop → app | `localhost:8088` (published port) |
| Container nginx → app container | `http://wtd:3000` (service/container name) |
| App container → itself | `localhost:3000` |

---

## Repository-specific relevance

Single SPA → introduce a second container for learning:

**Recommended lab pair**

1. **`wtd`** — `welcome-to-docker:dev` (or Hub image)
2. **`proxy`** — `nginx:alpine` configured to reverse-proxy to `wtd`

Alternatively, run a second HTTP server (`httpd:alpine` or `nginx`) as a “status” page and curl it **by name** from the app container’s network namespace using a debug container.

**Assumption:** Learners create a custom network named `wtd-net`.

---

## Commands introduced

| Command | Purpose |
|---------|---------|
| `docker network ls` | List networks |
| `docker network create` | Create user-defined network |
| `docker network inspect` | See connected containers, subnets |
| `docker network connect` / `disconnect` | Attach running containers |
| `docker network rm` | Delete network (**⚠️** if unused) |
| `docker run --network` | Start attached to a network |

---

## Guided demonstration

```bash
docker network create wtd-net
docker network ls
```

```bash
# Terminal-friendly demo with Hub image (port 80) OR built image (port 3000)
# Below uses Hub image for fewer build dependencies:

docker run -d --name wtd --network wtd-net docker/welcome-to-docker

# Second container: curl client on same network
docker run --rm --network wtd-net curlimages/curl:8.5.0 -I http://wtd:80
```

Expected: HTTP headers (200/301/etc.) — proves **DNS name `wtd`** resolves.

```bash
# Contrast: this usually FAILS (wtd not on default bridge DNS the same way /
# or name unknown depending on setup). Prefer user-defined networks.
```

Publish only what the browser needs:

```bash
docker rm -f wtd
docker run -d --name wtd --network wtd-net -p 8088:80 docker/welcome-to-docker
# Browser: localhost:8088
# Other containers: http://wtd:80
```

```bash
docker network inspect wtd-net
docker stop wtd && docker rm wtd
docker network rm wtd-net
```

### Two-container proxy sketch (learner builds config in labs)

```text
Browser → localhost:8080 → nginx (proxy) → http://wtd:80 → welcome app
```

Both `proxy` and `wtd` join `wtd-net`. Only nginx publishes `8080:80`.

---

## Hands-on exercises

1. Create `wtd-net`.
2. Run `welcome-to-docker` (Hub or built) attached to it **without** publishing ports; curl it from another container by name.
3. Publish a host port and open the app in a browser.
4. From a debug container, try `http://localhost:80` and observe failure/wrong target; then use the service name.
5. Inspect the network and identify container IPv4 addresses.
6. Clean up containers and network.

---

## Expected results

- User-defined network enables name-based DNS.
- Browser uses published host ports only.
- You can explain why `localhost` inside a container misleads beginners.
- `network inspect` shows both containers.

---

## Common mistakes

| Mistake | Symptom | Fix |
|---------|---------|-----|
| Connecting via `localhost` between containers | Connection refused | Use container/service name |
| Publishing every service port | Unnecessary host exposure | Publish only ingress (e.g., proxy) |
| Staying on default bridge | No reliable DNS by name | `docker network create` |
| Wrong target port (80 vs 3000) | Connection refused | Match image’s listen port |
| Forgetting shared network | Name resolve failure | Attach both with `--network` |

---

## Troubleshooting guidance

1. `curl: (6) Could not resolve host` → not on same user-defined network / wrong name.
2. `Connection refused` → resolved DNS but nothing listening on that port.
3. Browser works, container curl fails → you used host port from inside; switch to container port + name.
4. Port conflict on host → change host side of `-p`.

---

## Knowledge-check questions

1. Does `EXPOSE` make a port reachable from your browser?
2. Why create a user-defined bridge for two containers?
3. What does `localhost` mean inside a container?
4. When is `-p` unnecessary?
5. Hub image vs built image — which container ports do you curl?

---

## Practical challenge

Build this working topology:

1. Network `wtd-net`.
2. Container `wtd` running the welcome app (document whether Hub `:80` or built `:3000`).
3. Container `client` using `curlimages/curl` that successfully requests the app **by DNS name**.
4. Optional: add nginx reverse proxy published on `8080`.

Write the exact commands you used.

---

## Completion criteria

- [ ] Created and inspected a user-defined network.
- [ ] Connected two containers; verified HTTP by name.
- [ ] Explained expose vs publish vs east-west traffic.
- [ ] Cleaned up networks/containers.

---

## Optional stretch exercise

Add a third container and use `docker network connect` to attach a running container to `wtd-net` without recreating it.
