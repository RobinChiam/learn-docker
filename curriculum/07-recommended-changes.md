# 7. Recommended Repository Changes

> **Do not apply these yet** (per curriculum design request). They improve the Docker learning experience once approved.

---

## Essential

| Change | File to create/modify | Purpose | Learning value | Potential risk | Beginner exercise or instructor-provided? |
|--------|----------------------|---------|----------------|----------------|-------------------------------------------|
| Fix `welcome-to-docker` checkout | `.gitmodules` + init submodule **or** vendor source into `welcome-to-docker/` | Make app source actually present | Unblocks all Dockerfile labs | Wrong commit / large history if mishandled | **Instructor-provided** first; learner can practice `git submodule update` later |
| Expand root README learning path | `README.md` | Point to `curriculum/` and Hub vs build ports | Orientation | Overlong README | Instructor-provided outline; learner expands in capstone |
| Add `.env.example` | `.env.example` | Teach env substitution without secrets | Compose Module 07 | None if placeholders only | Beginner exercise |
| Add root `compose.yaml` starter | `compose.yaml` | Multi-container baseline | Compose + networking | Port conflicts with local tools | Start instructor stub; learner completes |
| Document Hub port 80 vs build port 3000 | `docs/PORTS.md` or README table | Prevent classic failure | Debugging | Docs drift if image changes | Instructor-provided |

---

## Helpful

| Change | File | Purpose | Learning value | Risk | Who |
|--------|------|---------|----------------|------|-----|
| Nginx reverse-proxy config | `deploy/nginx/default.conf` | Real multi-service topology | Networking, Compose | Misconfigured upstream | Instructor template; learner edits upstream host/port |
| Improve `.dockerignore` | `welcome-to-docker/.dockerignore` | Faster/safer builds | Build context | Over-ignoring needed files | Beginner exercise |
| Cache-friendly Dockerfile reorder | `welcome-to-docker/Dockerfile` | Faster rebuilds | Layer caching | Break build if paths wrong | Beginner→intermediate exercise |
| Pin `serve` + use `npm ci` | `Dockerfile` | Reproducibility | Module 09 | Version conflicts | Intermediate exercise |
| Debugging checklist template | `docs/DOCKER_DEBUG.md` | Structured troubleshooting | Module 08 | None | Instructor template |
| Lab data directory | `lab-data/.gitkeep` | Bind-mount practice | Volumes | Accidental secret files | Instructor-provided empty dir |

---

## Optional

| Change | File | Purpose | Learning value | Risk | Who |
|--------|------|---------|----------------|------|-----|
| `Dockerfile.dev` | `welcome-to-docker/Dockerfile.dev` | Hot-reload style workflow | Dev vs prod images | node_modules bind issues | Intermediate |
| `compose.override.yaml` example | `compose.override.yaml.example` | Dev overrides pattern | Compose advanced | Accidental commit of override | Instructor example |
| Companion notes API (tiny Node/Python) | `services/notes/` | Persistence + networking depth | Volumes + DNS | Scope creep | Optional stretch / instructor |
| Profiles for debug tooling | `compose.yaml` `profiles:` | Optional services | Compose profiles | Confusion if undocumented | Intermediate |
| Makefile / task scripts | `Makefile` | DX shortcuts | Not required for Docker literacy | Hides commands from beginners | Optional later |

---

## Production-oriented

| Change | File | Purpose | Learning value | Risk | Who |
|--------|------|---------|----------------|------|-----|
| Multi-stage hardened Dockerfile | `welcome-to-docker/Dockerfile` | Smaller non-root image | Module 09–10 | Healthcheck tool missing | Intermediate / capstone |
| Image digest pins in Compose | `compose.yaml` | Immutable deploys | Supply-chain awareness | Digests must be updated | Instructor-guided |
| CI build & smoke test | `.github/workflows/docker.yml` | PR builds image, curls health | Real workflow | CI minutes; secrets | Instructor-provided |
| Non-root + read-only + resource limits | Compose deploy keys / run flags | Hardening practice | Security module | Breaks writes/logs | Capstone stretch |
| Registry push docs | `docs/PUBLISH.md` | Align with upstream `MAINTAINERS.md` ideas | Registries beyond Hub pull | Credential leaks | Advanced / instructor |
| SBOM / scan step (conceptual or tool) | CI config | Vulnerability awareness | Scanning concepts | False positives | Optional advanced |

---

## Priority order if implementing later

1. Make `welcome-to-docker` source available (Essential).
2. Port documentation table (Essential).
3. Starter `compose.yaml` + nginx config + `.env.example` (Essential/Helpful).
4. Dockerfile hardening path as graded exercises (Helpful → Production-oriented).
5. CI smoke test (Production-oriented).
