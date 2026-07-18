# Module 09 — Security and Image Optimization

| Field | Value |
|-------|-------|
| **Difficulty** | Intermediate |
| **Estimated study duration** | 2–3 hours |
| **Prerequisites** | Modules 03 and 07 strongly recommended |

---

## Learning objectives

1. Apply least-privilege basics (non-root, minimal trust).
2. Pin versions and avoid secret leakage in images.
3. Shrink images with Alpine/slim bases, caching, multi-stage builds.
4. Explain scanning, read-only roots, capabilities, and resource limits at a conceptual level.
5. Separate development convenience from production hardening.

---

## Concepts explained

### Why root in containers is risky

Processes in containers often default to UID 0 (root). If escaped, impact is worse. Prefer a dedicated user.

### Trusted bases and pinning

| Practice | Example |
|----------|---------|
| Prefer official images | `node`, `nginx` |
| Pin major/minor | `node:22-alpine` better than `node:latest` |
| Pin tools | `serve@14.2.1` not `serve@latest` |
| Prefer digests for prod | `node@sha256:...` (advanced) |

### Secrets anti-patterns

Never put secrets in:

- Dockerfile `ENV` / `ARG` that remain in layers
- Source control
- World-readable image layers (`docker history` can reveal)

Env vars at runtime are better than baking secrets into images, but still visible to anyone who can `inspect` — use proper secret managers in real production.

### Minimal images & multi-stage

For this React app:

1. **Builder stage:** install deps, `npm run build`
2. **Runtime stage:** copy only `build/` + `serve` (or nginx) — no compilers, no `src`, no full `node_modules` if unnecessary

Current sample already deletes `node_modules` after build but still keeps a Node toolchain-ish image and uses `npm install` in one stage — multi-stage improves this further.

### Other hardening concepts (awareness)

| Topic | Beginner takeaway |
|-------|-------------------|
| Image scanning | Tools flag known CVEs in packages |
| Read-only root FS | `--read-only` + tmpfs for temp dirs |
| Drop capabilities | Limit kernel privileges containers get |
| Resource limits | `--memory` / `--cpus` reduce noisy-neighbor risk |
| Dev vs prod | Bind mounts & root may be OK locally; not for prod |

---

## Repository-specific relevance

Improve upon upstream `Dockerfile`:

| Current | Hardening/optimization target |
|---------|--------------------------------|
| `serve@latest` | Pin version |
| `npm install` | `npm ci` with lockfile |
| Single stage | Multi-stage |
| Runs as root | `USER` non-root |
| Thin `.dockerignore` | Exclude `.git`, CI, markdown, tests if unused |
| No HEALTHCHECK | Add one suitable for `serve` |

---

## Commands introduced

| Command | Purpose |
|---------|---------|
| `docker build --target` | Build specific stage |
| `docker run --read-only` | Read-only root (may need tmpfs) |
| `docker run --user` | Override user |
| `docker run --memory` / `--cpus` | Resource limits (conceptual practice) |
| `docker image ls` | Compare sizes before/after |

*(Vendor scanners vary — discuss conceptually; do not require a specific SaaS.)*

---

## Guided demonstration

### Multi-stage sketch (learner implements)

```dockerfile
# syntax=docker/dockerfile:1

FROM node:22-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY public ./public
COPY src ./src
RUN npm run build

FROM node:22-alpine AS runner
WORKDIR /app
RUN npm install -g serve@14.2.4 \
  && addgroup -S app && adduser -S app -G app
COPY --from=builder /app/build ./build
USER app
EXPOSE 3000
HEALTHCHECK CMD wget -qO- http://127.0.0.1:3000/ || exit 1
CMD ["serve", "-s", "build", "-l", "3000"]
```

> Adjust healthcheck tools to what exists in the image; install `wget` if needed or use a Node-based check.

```bash
docker build -t welcome-to-docker:secure .
docker image ls
# Compare size to welcome-to-docker:dev
```

```bash
docker run -d --name secure-wtd -p 8088:3000 \
  --memory 256m --cpus 0.5 \
  welcome-to-docker:secure
```

---

## Hands-on exercises

1. Pin `serve` and switch to `npm ci`.
2. Expand `.dockerignore`.
3. Implement multi-stage build; record before/after sizes.
4. Run as non-root; confirm app still serves.
5. Try `--read-only` and note what breaks; remediate with tmpfs if appropriate.
6. List three secrets you would **never** put in this Dockerfile.

---

## Expected results

- Smaller or cleaner final image than the naive single stage.
- Process does not need root for serving static files.
- Build is more reproducible (lockfile + pins).
- You can explain remaining risks honestly.

---

## Common mistakes

| Mistake | Why it hurts |
|---------|--------------|
| Fake “security” with huge images | Attack surface remains |
| Secrets in build args “temporarily” | Persist in image history |
| Non-root without fixing file ownership | Permission denied on `build/` |
| Latest tags in prod compose | Surprise upgrades |
| Assuming Alpine is always safer | Still need updates & scanning |

---

## Troubleshooting guidance

1. Non-root cannot bind port <1024 — use 3000/8080 inside container.
2. Healthcheck fails — binary missing or app not listening on expected interface.
3. Multi-stage COPY path wrong — verify builder output path `/app/build`.

---

## Knowledge-check questions

1. Why is `serve@latest` a reproducibility risk?
2. Where can secrets accidentally persist in Docker?
3. What does a multi-stage build buy you for a CRA app?
4. Does Alpine alone make an image secure?
5. Name one tradeoff of `--read-only`.

---

## Practical challenge

Produce `welcome-to-docker:hardened` that:

1. Uses multi-stage builds
2. Pins versions
3. Runs as non-root
4. Has an expanded `.dockerignore`
5. Documents image size vs your Module 03 image

---

## Completion criteria

- [ ] Hardened Dockerfile builds and runs.
- [ ] Size comparison recorded.
- [ ] Non-root verified (`docker exec ... id` or inspect).
- [ ] Written short “dev vs prod security” paragraph.

---

## Optional stretch exercise

Add Compose `read_only: true`, `tmpfs`, and `mem_limit` for the web service; document what still fails and why.
