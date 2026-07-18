# Module 07 — Docker Compose

| Field | Value |
|-------|-------|
| **Difficulty** | Lower intermediate |
| **Estimated study duration** | 3–4 hours |
| **Prerequisites** | Module 06; working image or Hub pull for welcome app |

---

## Learning objectives

1. Explain Compose’s mental model (declarative multi-container projects).
2. Author a `compose.yaml` for this learning repository.
3. Manage build, up, logs, exec, and teardown with Compose CLI.
4. Wire env files, volumes, networks, healthchecks, depends_on, restart policies, and profiles.
5. Distinguish Compose from Kubernetes (Compose ≠ cluster orchestrator).

---

## Concepts explained

### Purpose and mental model

Compose replaces long `docker run` strings with a **project file**. One project → many services sharing default network DNS names matching service names.

```text
compose.yaml
  services: app, proxy, ...
  networks: ...
  volumes: ...
```

`docker compose up` creates resources; `down` removes containers and default network (volumes kept unless `-v`).

### `docker compose` vs `docker-compose`

| | Modern | Legacy |
|--|--------|--------|
| Command | `docker compose` (plugin) | `docker-compose` (separate binary) |
| Status | **Preferred** | Deprecated/older installs |

### Compose file building blocks

| Key | Role |
|-----|------|
| `services` | Containers to run |
| `image` / `build` | Pre-built vs build from Dockerfile |
| `ports` | Publish to host |
| `environment` / `env_file` | Configuration |
| `volumes` | Mounts |
| `networks` | Connectivity |
| `command` / `entrypoint` | Override image defaults |
| `depends_on` | Start order (not full readiness unless healthcheck conditioned) |
| `healthcheck` | Readiness/liveness style probes |
| `restart` | Restart policy |
| `profiles` | Optional service groups (e.g., `debug`) |
| `develop` / override files | Advanced workflows (optional) |

### Development vs production Compose patterns

| Development | Production-oriented local |
|-------------|---------------------------|
| Bind-mount source | Image-only, no bind mounts |
| Hot reload / `npm start` | Pre-built static serve |
| Publish many ports | Publish only proxy |
| Verbose logging | Healthchecks + restart policies |

Compose on a single machine is **not** Kubernetes. No claim of multi-node scheduling.

---

## Repository-specific relevance

**Target deliverable:** `compose.yaml` at repo root managing:

| Service | Image / build | Notes |
|---------|---------------|-------|
| `web` | `build: ./welcome-to-docker` **or** Hub image | Prefer build once submodule fixed |
| `proxy` | `nginx:alpine` | Reverse proxy; publish `8080:80` |
| (optional) `cache` / `db` | stretch only | Not required by SPA |

**Networks:** `frontend` user-defined bridge (or default project network).

**Volumes:** named volume for proxy logs or companion data.

**Env:** `.env.example` with `HOST_PORT=8080` placeholders — no secrets.

**Port caution:**

```yaml
# Hub image
ports: ["8088:80"]

# Built from Dockerfile
ports: ["8088:3000"]
```

---

## Commands introduced

| Command | Purpose | Safety |
|---------|---------|--------|
| `docker compose config` | Validate/render | ✅ |
| `docker compose build` | Build service images | ✅/⚠️ |
| `docker compose up` | Create & start | ⚠️ |
| `docker compose up -d` | Detached | ⚠️ |
| `docker compose down` | Stop & remove containers/network | ⚠️ |
| `docker compose down -v` | Also delete volumes | 🛑 |
| `docker compose ps` | Project status | ✅ |
| `docker compose logs` / `logs -f` | Logs | ✅ |
| `docker compose exec` | Exec in service | ⚠️ |
| `docker compose run` | One-off service run | ⚠️ |
| `docker compose restart` | Restart services | ⚠️ |
| `docker compose stop` / `start` | Stop/start | ⚠️ |
| `docker compose pull` | Pull service images | ✅ |
| `docker compose images` | List images used | ✅ |
| `docker compose top` | Processes | ✅ |

---

## Guided demonstration

### Example starter `compose.yaml` (Hub-based — works even if submodule empty)

```yaml
name: learn-docker

services:
  web:
    image: docker/welcome-to-docker
    # container_name optional — Compose prefers project_service_index names
    networks: [frontend]
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "wget", "-qO-", "http://127.0.0.1:80"]
      interval: 30s
      timeout: 3s
      retries: 3
      # Note: healthcheck CMD must exist in image; adjust if wget/curl absent

  proxy:
    image: nginx:alpine
    ports:
      - "${HOST_PORT:-8080}:80"
    volumes:
      - ./deploy/nginx/default.conf:/etc/nginx/conf.d/default.conf:ro
      - proxy-logs:/var/log/nginx
    networks: [frontend]
    depends_on:
      - web
    restart: unless-stopped
    profiles: ["proxy"]  # start with: docker compose --profile proxy up -d

networks:
  frontend:

volumes:
  proxy-logs:
```

> **Assumption:** `deploy/nginx/default.conf` is created in labs (recommended repo change). Minimal config proxies to `http://web:80`.

### Build-based variant (when source exists)

```yaml
services:
  web:
    build:
      context: ./welcome-to-docker
      dockerfile: Dockerfile
    image: welcome-to-docker:compose
    # For built image listening on 3000, proxy targets web:3000
```

### Demo flow

```bash
docker compose config
docker compose pull
docker compose --profile proxy up -d
docker compose ps
docker compose logs -f web
# Ctrl+C

docker compose exec web ls /
docker compose top
docker compose down          # keeps volumes
# docker compose down -v   # 🛑 deletes volumes too
```

---

## Hands-on exercises

1. Write `compose.yaml` with at least the `web` service.
2. Validate with `config`, then `up -d`, open the published URL.
3. Add a custom network and a second service; curl `web` by service name from `compose run`.
4. Add `.env` with `HOST_PORT` and confirm substitution.
5. Add `restart: unless-stopped` and a healthcheck **compatible with your image**.
6. Practice `logs`, `exec`, `restart`, `down`.

---

## Expected results

- `docker compose ps` shows healthy/running services.
- Browser reaches the app via published port.
- Service DNS name `web` resolves from another service.
- `down` removes containers; project is restartable with `up`.

---

## Common mistakes

| Mistake | Fix |
|---------|-----|
| Using legacy `docker-compose` only | Install Compose plugin; use `docker compose` |
| Wrong container port in proxy | Match Hub 80 vs build 3000 |
| `depends_on` assumed “ready” | Add healthcheck + `condition: service_healthy` (Compose v2) |
| Committing `.env` with secrets | Commit `.env.example` only |
| `down -v` by habit | **🛑** Wipes volumes |

---

## Troubleshooting guidance

1. `compose config` first for YAML errors.
2. `compose ps` + `logs` for crash loops.
3. `compose run --rm web wget ...` or curl sidecar for DNS tests.
4. Port conflicts → change `HOST_PORT`.

---

## Knowledge-check questions

1. What problem does Compose solve compared to many `docker run` commands?
2. What is deleted by `docker compose down` vs `down -v`?
3. How do services discover each other?
4. What are profiles for?
5. Is Compose a replacement for Kubernetes?

---

## Practical challenge

Produce a repo-root `compose.yaml` that:

1. Builds or pulls the welcome app.
2. Puts services on a named network.
3. Uses `.env` for the host port.
4. Includes at least one volume.
5. Documents start instructions in comments at the top.

Bring it up detached, show `ps` and `logs`, then tear down **without** deleting volumes.

---

## Completion criteria

- [ ] Valid `compose.yaml` for this project.
- [ ] Used at least 8 Compose CLI commands.
- [ ] Demonstrated env substitution and networking.
- [ ] Explained why Compose is not Kubernetes.

---

## Optional stretch exercise

Add `compose.override.yaml` for development bind mounts, and a production-like override without binds. Show `docker compose config` merging behavior.
