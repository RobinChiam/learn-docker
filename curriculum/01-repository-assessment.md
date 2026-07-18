# 1. Repository Assessment

## Repository summary

**Repository:** [`RobinChiam/learn-docker`](https://github.com/RobinChiam/learn-docker)

This repository is a **Docker learning project**, not a production application monorepo. The root `README.md` states the goal clearly: learn Docker, setup, volumes, and containerization.

The only non-README content intended for study is **`welcome-to-docker`**, which is recorded in git as a **submodule/gitlink** pointing at Docker’s official sample:

| Field | Value |
|-------|--------|
| Upstream project | [`docker/welcome-to-docker`](https://github.com/docker/welcome-to-docker) |
| Pinned commit | `c8cd4b31b2f1f06bb865fe7a0d328f2d85777353` |
| Commit message | Merge PR #96 — Dockerfile vulnerability update |
| Local status in this clone | **Empty directory** — submodule not initialized; `.gitmodules` is missing |

**Parent repo contents today**

| Path | Status |
|------|--------|
| `README.md` | Present (5 lines, learning goal) |
| `welcome-to-docker/` | Empty on disk; gitlink in `HEAD` |
| Root `Dockerfile` / `compose.yaml` | **Absent** |
| CI/CD for this parent repo | **Absent** |
| `.env` examples | **Absent** |

The curriculum therefore centers on the **upstream sample application’s source and Dockerfile**, which learners will obtain by fixing the submodule or cloning the upstream repo.

---

## Technology stack

| Layer | Technology | Evidence |
|-------|------------|----------|
| UI framework | React 18 | `package.json` → `react`, `react-dom` `^18.2.0` |
| Scaffolding | Create React App | `react-scripts` `5.0.1`; scripts `start` / `build` / `test` |
| Visual extras | `react-particles`, `tsparticles` | Confetti effect in `src/Confetti.js` |
| Language | JavaScript (JSX) | `src/App.js`, `src/index.js` |
| Package manager | npm | `package.json`, `package-lock.json` |
| Base image | `node:22-alpine` | Upstream `Dockerfile` |
| Runtime server (built image) | `serve` (global npm package) | `CMD [ "serve", "-s", "build" ]` |
| Static assets | CRA `public/` | `public/index.html`, favicon, robots.txt |

There is **no backend language**, ORM, or server framework beyond static file serving after `npm run build`.

---

## Application architecture

```text
┌─────────────────────────────────────────────────────────┐
│  Browser → http://localhost:8088                        │
└──────────────────────────┬──────────────────────────────┘
                           │ published port mapping
                           ▼
┌─────────────────────────────────────────────────────────┐
│  Container: welcome-to-docker                           │
│                                                         │
│  Built-from-source path:                                │
│    serve -s build   listening on :3000                  │
│    (static files from CRA production build)             │
│                                                         │
│  Hub image path (docker/welcome-to-docker):             │
│    typically listens on :80 (optimized “small-image”)   │
└─────────────────────────────────────────────────────────┘
```

**Architecture characteristics**

- **Single service** — one frontend SPA; no API, worker, or database process.
- **Build-time compilation** — React source is compiled into static files inside the image.
- **Stateless at runtime** — no application database; congratulations page + social share links.
- **Upstream Hub vs source mismatch** — Hub README uses `-p 8088:80`; source `Dockerfile` uses `EXPOSE 3000` and docs say `-p 8088:3000`. Learners must treat these as **two different images**.

---

## Current startup process

### Without building (pull Hub image)

```bash
docker run -d -p 8088:80 --name welcome-to-docker docker/welcome-to-docker
# open http://localhost:8088
```

### Build from source (after obtaining `welcome-to-docker` files)

```bash
cd welcome-to-docker   # once content is present
docker build -t welcome-to-docker .
docker run -d -p 8088:3000 --name welcome-to-docker welcome-to-docker
# open http://localhost:8088
```

### Local Node development (non-Docker)

```bash
npm install
npm start              # CRA dev server, typically http://localhost:3000
npm run build          # production static output in build/
npm test               # CRA test runner
```

---

## Docker readiness assessment

| Criterion | Rating | Notes |
|-----------|--------|-------|
| Has a working sample Dockerfile upstream | ✅ Strong | Simple, readable, good for beginners |
| Has `.dockerignore` upstream | ⚠️ Weak | Only ignores `node_modules` |
| Multi-service Compose ready | ❌ Not present | Must be learner-built |
| Env var / secrets story | ❌ Minimal | Static SPA; env rarely required |
| Persistence story | ❌ Minimal | No DB; volumes taught via mounts + companion services |
| Parent repo setup | ❌ Broken submodule | Content not available until fixed |
| Beginner friendliness | ✅ High | Tiny app, clear “first container” UX |
| Intermediate depth | ⚠️ Needs extension | Networking/Compose need added sidecar services |

**Verdict:** Excellent **beginner Docker playground** once `welcome-to-docker` content is available. Intermediate topics (Compose, networking, persistence, multi-stage optimization) require **instructor-designed companion services** because the sample app alone is a single static frontend.

---

## Existing Docker-related files

### In parent repo (`learn-docker`)

| File | Present? |
|------|----------|
| `Dockerfile` | No |
| `.dockerignore` | No |
| `compose.yaml` / `docker-compose.yml` | No |
| `.env.example` | No |

### In upstream `docker/welcome-to-docker` @ `c8cd4b3` (intended submodule)

| File | Purpose |
|------|---------|
| `Dockerfile` | Build CRA app on `node:22-alpine`, install `serve`, expose 3000 |
| `.dockerignore` | Exclude `node_modules` from build context |
| `README.md` | Run Hub image and build/run instructions |
| `MAINTAINERS.md` | Hub publishing via `docker buildx` (advanced) |
| `.github/workflows/merge-main-into-small-image.yml` | Merge `main` → `small-image` branch |

**Upstream Dockerfile (annotated for curriculum use)**

```dockerfile
FROM node:22-alpine
WORKDIR /app
COPY package*.json ./
COPY ./src ./src
COPY ./public ./public
RUN npm install \
    && npm install -g serve@latest \
    && npm run build \
    && rm -fr node_modules
EXPOSE 3000
CMD [ "serve", "-s", "build" ]
```

**Teaching notes on this Dockerfile**

- Good: Alpine base, `WORKDIR`, copy before install pattern (partial), `EXPOSE`, exec-form `CMD`.
- Improve later: pin `serve` version, use `npm ci`, add multi-stage build, non-root `USER`, richer `.dockerignore`, cache-friendly copy order (`package*.json` then install then copy source).

---

## Required ports

| Context | Host port | Container port | Notes |
|---------|-----------|----------------|-------|
| Hub image (docs) | 8088 | **80** | `docker run -p 8088:80 ... docker/welcome-to-docker` |
| Built from source Dockerfile | 8088 | **3000** | `EXPOSE 3000`; `serve` default |
| CRA local `npm start` | 3000 | n/a | Dev only, not containerized by default |
| Curriculum nginx sidecar (proposed) | 8080 | 80 | Lab-only reverse proxy |
| Curriculum companion API (proposed) | 8081 | 3001 | Lab-only for multi-container demos |

---

## Required environment variables

**Application today:** effectively **none** for the static congratulations page.

| Variable | Required? | Notes |
|----------|-----------|-------|
| `PORT` | Optional | Some Node servers honor it; `serve` can take `-l` |
| `NODE_ENV` | Optional | CRA build embeds production mode at build time |
| `REACT_APP_*` | Optional | Only if learner adds CRA runtime-config demos (build-time only) |

**Assumption for Compose labs:** introduce placeholder vars such as `APP_TITLE`, `API_URL`, `NGINX_HOST` for teaching `.env` / substitution — **not** present in upstream source today.

**Never commit real secrets.** Use placeholders like `changeme` / `example-only`.

---

## Persistence requirements

| Data | Persistence needed? | Curriculum approach |
|------|---------------------|---------------------|
| React source (`src/`, `public/`) | Dev only | Bind mount for live editing labs |
| `node_modules` | Careful | Prefer rebuild in image; avoid host/container OS mismatch |
| `build/` static output | Ephemeral OK | Produced inside image |
| Application user data | **None** | App is stateless |
| Access logs / notes sidecar | Lab-invented | Named volume on companion service |
| Database files | **None upstream** | Optional stretch: add Postgres only in later labs |

---

## Supporting services

| Service | Present? | Curriculum use |
|---------|----------|----------------|
| Database | No | Optional stretch (Postgres) for persistence depth |
| Cache | No | Optional stretch (Redis) |
| Queue / worker | No | Out of scope for core path |
| Reverse proxy | No | **Essential lab add-on:** nginx in front of the app |
| Second HTTP service | No | **Essential lab add-on:** tiny companion API or static “status” container for DNS/networking |

---

## Repository-specific learning opportunities

1. **First successful container** using Hub image `docker/welcome-to-docker` (port 80).
2. **Read a real Dockerfile** that builds a React app learners can see in a browser.
3. **Port mapping confusion as a feature** — Hub `:80` vs build `:3000` teaches publish vs expose.
4. **Layer caching exercise** — reorder `COPY` / `RUN npm install` for faster rebuilds.
5. **`.dockerignore` expansion** — currently only `node_modules`.
6. **Multi-stage build refactor** — builder stage + slim `serve`/`nginx` runtime.
7. **Bind-mount CRA source** for a development workflow contrast with production image.
8. **Compose with nginx + app** for networking, healthchecks, and service DNS.
9. **Debugging** connection refused when mapping the wrong container port.
10. **Security/hardening** — non-root user, pin versions, drop build tools from final image.

---

## Risks or unknowns

| Risk | Impact | Mitigation in curriculum |
|------|--------|--------------------------|
| Submodule empty / no `.gitmodules` | Learners cannot see app source | Assessment documents gap; Lesson 1 uses Hub image; later labs clone upstream |
| Hub image port ≠ Dockerfile port | Confusing failures | Explicit callouts in Modules 2, 5, 8 |
| `serve@latest` unpinned | Non-reproducible builds | Teach pinning in Module 3 / 9 |
| `npm install` vs `npm ci` | Lockfile may be ignored | Teach `npm ci` for reproducibility |
| No real persistence needs | Volume lessons feel abstract | Add companion volume-backed service |
| No Compose file yet | Compose modules need scaffolding | Labs create `compose.yaml` from scratch |
| Parent README very thin | Onboarding unclear | Capstone writes Docker README section |
| Date note | User date is Jul 2026; sample uses Node 22 | Curriculum uses Node 22 as current sample |

---

## Assumptions

1. **Learning target content** = upstream `docker/welcome-to-docker` at commit `c8cd4b3` (or later compatible `main`), even though the local submodule is empty.
2. Learners install **Docker Desktop** or **Docker Engine + Compose plugin** on their own machine; this cloud environment may not have Docker available.
3. Prefer modern **`docker compose`** (plugin) over legacy `docker-compose`.
4. Prefer filename **`compose.yaml`**.
5. Multi-container labs introduce **nginx** and a small **companion HTTP service** not present in the repo today.
6. “Production-oriented” means **hardened local/prod-like images**, not Kubernetes.
7. No real credentials exist or will be invented beyond placeholders.
8. Estimated durations assume a motivated beginner studying part-time with hands-on labs.
