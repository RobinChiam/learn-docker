# Module 03 — Images and Dockerfiles

| Field | Value |
|-------|-------|
| **Difficulty** | Beginner → Advanced beginner |
| **Estimated study duration** | 3–4 hours |
| **Prerequisites** | Modules 01–02; local copy of `welcome-to-docker` source |

---

## Learning objectives

1. Read and explain every instruction in the sample `Dockerfile`.
2. Explain build context, layers, and caching.
3. Write and improve a `.dockerignore`.
4. Build, tag, and run an image from this repository’s sample app.
5. Distinguish `CMD` vs `ENTRYPOINT` and `ENV` vs `ARG`.
6. Understand when multi-stage builds help (preview for Module 09).

---

## Concepts explained

### What a Dockerfile is

A Dockerfile is a **script of image layers**. Each instruction usually creates a new layer. Order matters for **cache reuse**.

### Core instructions

| Instruction | Role | Notes for this app |
|-------------|------|--------------------|
| `FROM` | Base image | `node:22-alpine` |
| `WORKDIR` | Set working directory | `/app` |
| `COPY` | Copy files from build context | Preferred over `ADD` for local files |
| `ADD` | Copy + extra magic (URLs, tar auto-extract) | Usually unnecessary — prefer `COPY` |
| `RUN` | Execute build-time commands | `npm install`, `npm run build` |
| `CMD` | Default command when container starts | `serve -s build` |
| `ENTRYPOINT` | Fixed executable; `CMD` can supply args | Not used in sample yet |
| `ENV` | Persist env in image | Runtime configuration |
| `ARG` | Build-time only variables | Not in final runtime unless copied to `ENV` |
| `EXPOSE` | Documents intended port | Does **not** publish to host |
| `USER` | Run as non-root | Hardening (Module 09) |
| `HEALTHCHECK` | How daemon probes liveness | Compose labs later |

### Build context

When you run `docker build -t name .`, the `.` is the **build context** — files sent to the daemon. Large contexts = slow builds. `.dockerignore` excludes files (like `.gitignore`).

### Layers and caching

Docker reuses a layer if the instruction and prior layers are unchanged. Pattern:

1. Copy dependency manifests first (`package*.json`)
2. `RUN npm ci` / `npm install`
3. Copy application source
4. Build

The upstream sample copies `src` and `public` **before** `npm install`, which **busts cache** on any source change — a perfect improvement exercise.

### Image tags

```text
name:tag           e.g. welcome-to-docker:dev
registry/name:tag  e.g. docker/welcome-to-docker:latest
```

Untagged/dangling images appear after rebuilds — clean carefully.

### Development vs production images

| | Development | Production-oriented |
|--|-------------|---------------------|
| Goal | Fast feedback | Small, secure, reproducible |
| Typical | Bind-mount source, hot reload | Multi-stage, pinned versions, non-root |
| Sample today | Possible with `npm start` in container | Closer to current Dockerfile + improvements |

---

## Repository-specific relevance

**File:** upstream `welcome-to-docker/Dockerfile`

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

**File:** `.dockerignore` currently contains only:

```gitignore
node_modules
```

**Port reminder:** built image → container **3000** → run with `-p 8088:3000`.

**Obtaining source if submodule empty:**

```bash
# Option A — clone alongside (Assumption: acceptable for learning)
git clone https://github.com/docker/welcome-to-docker.git welcome-to-docker-src
cd welcome-to-docker-src
git checkout c8cd4b31b2f1f06bb865fe7a0d328f2d85777353

# Option B — fix submodule (see recommended changes)
```

---

## Commands introduced

| Command | Purpose |
|---------|---------|
| `docker build` | Build image from Dockerfile |
| `docker build -t` | Build and tag |
| `docker tag` | Add another tag to existing image |
| `docker history` | Show image layers |
| `docker build --no-cache` | Force full rebuild (**slower**) |

---

## Guided demonstration

```bash
cd welcome-to-docker   # or welcome-to-docker-src
docker build -t welcome-to-docker:dev .
```

Expected: Node base pull (first time), npm install, CRA build, finish with image ID.

```bash
docker image ls | grep welcome-to-docker
docker history welcome-to-docker:dev
docker run -d --rm --name wtd-built -p 8088:3000 welcome-to-docker:dev
```

Visit `http://localhost:8088`.

```bash
docker stop wtd-built   # --rm removes container automatically
```

Improve cache order (learner edit — save as experiment):

```dockerfile
FROM node:22-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install && npm install -g serve@14
COPY ./src ./src
COPY ./public ./public
RUN npm run build && rm -fr node_modules
EXPOSE 3000
CMD ["serve", "-s", "build"]
```

Rebuild twice; second build should reuse the `npm install` layer if `package*.json` unchanged.

---

## Hands-on exercises

1. Build `welcome-to-docker:lab3` from source.
2. Run it on `8088:3000` and verify the page.
3. Expand `.dockerignore` to exclude `.git`, `README.md`, `MAINTAINERS.md`, `.github`.
4. Change a comment in `src/App.js`, rebuild, observe which layers rerun.
5. Tag the image as `welcome-to-docker:0.1.0`.

---

## Expected results

- Image builds successfully.
- App serves on published port 8088 → 3000.
- Improved `.dockerignore` shrinks build context (watch build “transferring context” size).
- You can explain why `EXPOSE` alone does not open host ports.

---

## Common mistakes

| Mistake | Symptom | Fix |
|---------|---------|-----|
| `docker build` wrong directory | Missing Dockerfile / wrong files | `cd` to app root containing Dockerfile |
| Mapping `8088:80` on built image | Connection refused | Use `8088:3000` |
| Huge context | Slow build | Fix `.dockerignore` |
| Editing code but cache serves old app | Stale layers / wrong tag | Confirm tag; rebuild; avoid confusing Hub vs local names |
| Using `ADD` for local files | Confusion | Prefer `COPY` |

---

## Troubleshooting guidance

1. Build fails on `npm install` → check network; try again; read error log.
2. Build succeeds but blank/error page → `docker logs`; confirm `build/` exists in image via `docker run --rm -it --entrypoint sh IMAGE`.
3. “No space left” → `docker system df`; prune carefully (**🛑**).

---

## Knowledge-check questions

1. Does `EXPOSE 3000` publish port 3000 on the host?
2. Why prefer `COPY` over `ADD` for local source?
3. What is the build context?
4. Why copy `package*.json` before source for caching?
5. What is the difference between `ARG` and `ENV`?

---

## Practical challenge

Create `Dockerfile.dev` notes (can be comments in a doc) describing how a **development** image would differ from the current production-ish Dockerfile (e.g., run `npm start`, bind-mount `src`). You do not must fully implement it until Module 04/07.

Then produce a working production-style image tagged `welcome-to-docker:lab3` with an improved `.dockerignore`.

---

## Completion criteria

- [ ] Built and ran the app from the Dockerfile.
- [ ] Explained each Dockerfile line in the sample.
- [ ] Improved `.dockerignore` and rebuilt.
- [ ] Demonstrated understanding of layer cache with a second build.

---

## Optional stretch exercise

Convert the Dockerfile to a **multi-stage** build: stage `builder` runs `npm run build`; final stage copies `build/` into a minimal image running `serve` or nginx. Compare `docker image ls` sizes before/after.
