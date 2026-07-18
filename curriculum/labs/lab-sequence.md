# 5. Repository Lab Sequence

Progressive labs that produce real artifacts for `learn-docker`. Complete in order. Each lab states inputs, actions, and outputs.

**Assumption:** When `welcome-to-docker/` is empty, Lab 0 is mandatory.

---

## Lab 0 — Obtain application source

| | |
|--|--|
| **Goal** | Make upstream sample source available locally |
| **Input** | Empty `welcome-to-docker/` gitlink |
| **Actions** | Fix submodule **or** `git clone https://github.com/docker/welcome-to-docker.git` and checkout `c8cd4b3` into a working path |
| **Output** | Readable `Dockerfile`, `package.json`, `src/`, `public/` |

---

## Lab 1 — First Hub container

| | |
|--|--|
| **Module** | 01 |
| **Goal** | Run official image safely |
| **Commands** | `docker run -d -p 8088:80 --name welcome-to-docker docker/welcome-to-docker` |
| **Verify** | Browser/curl `http://localhost:8088` shows congratulations |
| **Cleanup** | `docker stop` + `docker rm` |
| **Output** | Confidence with Hub pull/run; notes on port **80** |

---

## Lab 2 — CLI workout

| | |
|--|--|
| **Module** | 02 |
| **Goal** | Lifecycle fluency |
| **Actions** | pull → run → logs → exec → stats → restart → stop → rm |
| **Output** | Short command journal (copy/paste of successful sequence) |

---

## Lab 3 — Working Dockerfile image

| | |
|--|--|
| **Module** | 03 |
| **Goal** | Produce **working Dockerfile** build from source |
| **Actions** | `docker build -t welcome-to-docker:lab3 .` from app dir; run `-p 8088:3000` |
| **Output** | Image `welcome-to-docker:lab3`; notes on port **3000** |

---

## Lab 4 — Improve `.dockerignore`

| | |
|--|--|
| **Module** | 03 |
| **Goal** | Smaller build context |
| **Actions** | Exclude `.git`, `.github`, `*.md`, `node_modules`, coverage, etc. |
| **Output** | Updated **`.dockerignore`**; before/after context observations |

---

## Lab 5 — Development container workflow

| | |
|--|--|
| **Modules** | 03–04 |
| **Goal** | **Development container workflow** |
| **Actions** | Document and try bind-mounting `src/` into a Node container; contrast with immutable production image |
| **Output** | Notes or `Dockerfile.dev` / compose override sketch |

---

## Lab 6 — Persistent-data solution

| | |
|--|--|
| **Module** | 04 |
| **Goal** | **Persistent-data solution** |
| **Actions** | Named volume demo + optional `./data` bind mount; prove survival across `docker rm` |
| **Output** | Documented volume name(s); proof commands |

---

## Lab 7 — Custom Docker network

| | |
|--|--|
| **Module** | 05 |
| **Goal** | **Custom Docker network** |
| **Actions** | `docker network create wtd-net`; attach app + curl client; request by DNS name |
| **Output** | Network `wtd-net` (or Compose equivalent); successful curl by name |

---

## Lab 8 — Multi-container environment

| | |
|--|--|
| **Modules** | 05–07 |
| **Goal** | **Multi-container environment** |
| **Actions** | Add nginx reverse proxy (or companion HTTP service) on shared network |
| **Output** | Browser entry via proxy published port; internal hop to `web` |

---

## Lab 9 — Docker Compose configuration

| | |
|--|--|
| **Modules** | 06–07 |
| **Goal** | Complete **`compose.yaml`** |
| **Actions** | Services, build/image, ports, env, volumes, networks, healthcheck, restart |
| **Output** | Root **`compose.yaml`** + **`.env.example`** |

---

## Lab 10 — Debugging checklist

| | |
|--|--|
| **Module** | 08 |
| **Goal** | Personal **debugging checklist** |
| **Actions** | Break port mapping and localhost DNS on purpose; fix with evidence |
| **Output** | `docs/DOCKER_DEBUG.md` (or notes) with hypothesis template |

---

## Lab 11 — Optimized production-oriented image

| | |
|--|--|
| **Module** | 09 |
| **Goal** | **Optimized production-oriented image** |
| **Actions** | Multi-stage, pin versions, non-root, compare sizes |
| **Output** | Tag `welcome-to-docker:hardened` + size table |

---

## Lab 12 — Developer-facing Docker README

| | |
|--|--|
| **Module** | 10 |
| **Goal** | **Developer-facing Docker README section** |
| **Actions** | Quick start, ports table (Hub vs build), env, compose up/down, troubleshooting link |
| **Output** | Updated `README.md` section or `docs/DOCKER.md` |

---

## Lab 13 — Capstone integration

| | |
|--|--|
| **Module** | 10 |
| **Goal** | All artifacts work together from a clean machine following docs only |
| **Output** | Capstone packet ready for rubric scoring |

---

## Artifact checklist (end state)

- [ ] Working Dockerfile
- [ ] `.dockerignore`
- [ ] Development container workflow notes/files
- [ ] Persistent-data solution
- [ ] Custom Docker network
- [ ] Docker Compose configuration
- [ ] Multi-container environment
- [ ] Debugging checklist
- [ ] Optimized production-oriented image
- [ ] Developer-facing Docker README section
