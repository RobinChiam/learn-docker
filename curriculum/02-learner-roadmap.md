# 2. Learner Roadmap

## Curriculum phases (recommended order)

```text
Phase 1  Foundations          →  Module 01
Phase 2  CLI fundamentals     →  Module 02
Phase 3  Images & Dockerfiles →  Module 03
Phase 4  Storage              →  Module 04
Phase 5  Networking           →  Module 05
Phase 6  YAML for Compose     →  Module 06
Phase 7  Docker Compose       →  Module 07
Phase 8  Debugging            →  Module 08
Phase 9  Security & optimize  →  Module 09
Phase 10 Capstone             →  Module 10
```

Do **not** skip phases. Each module reuses artifacts from earlier labs (image tags, networks, Compose files).

---

## Estimated total duration

| Pace | Total study time |
|------|------------------|
| Focused intensive | **18–24 hours** |
| Part-time (recommended) | **24–32 hours** across 2–3 weeks of sessions |
| With all stretch exercises | **32–40 hours** |

| Module | Title | Est. duration |
|--------|-------|---------------|
| 01 | Container foundations | 1.5–2 h |
| 02 | Docker CLI fundamentals | 2–3 h |
| 03 | Images and Dockerfiles | 3–4 h |
| 04 | Storage and persistence | 2–2.5 h |
| 05 | Docker networking | 2–3 h |
| 06 | YAML fundamentals for Compose | 1–1.5 h |
| 07 | Docker Compose | 3–4 h |
| 08 | Debugging and troubleshooting | 2–3 h |
| 09 | Security and image optimization | 2–3 h |
| 10 | Repository capstone | 3–5 h |

---

## Major milestones

| Milestone | After module | You can… |
|-----------|--------------|----------|
| M1 — First container | 01 | Explain containers; run and remove Hub image safely |
| M2 — CLI fluency | 02 | Manage container lifecycle without a GUI |
| M3 — Custom image | 03 | Build `welcome-to-docker` from a Dockerfile you understand |
| M4 — Persist & mount | 04 | Use bind mounts for dev; named volumes for durable data |
| M5 — Talk between containers | 05 | Connect app + nginx (or companion) on a user-defined bridge |
| M6 — YAML literacy | 06 | Read/write Compose YAML without indentation disasters |
| M7 — Multi-container app | 07 | `docker compose up` a full local stack for this repo |
| M8 — Independent debug | 08 | Form a hypothesis and verify with logs/inspect/exec |
| M9 — Hardened image | 09 | Ship a smaller, non-root, multi-stage image |
| M10 — Capstone complete | 10 | Document and demo a production-minded local Docker setup |

---

## Expected skill level after each phase

| After phase | Skill level | Typical independence |
|-------------|-------------|----------------------|
| 1 | Absolute beginner → aware beginner | Can run a pre-built image |
| 2 | Beginner | Can operate day-to-day CLI |
| 3 | Advanced beginner | Can write/modify Dockerfiles for this SPA |
| 4 | Advanced beginner | Avoids data-loss mistakes with volumes |
| 5 | Lower intermediate | Designs simple container networks |
| 6 | Lower intermediate | Maintains Compose YAML confidently |
| 7 | Intermediate | Owns multi-service local stacks |
| 8 | Intermediate | Troubleshoots without random command spam |
| 9 | Intermediate | Applies basic hardening and image slimming |
| 10 | Solid intermediate (practical) | Ready for team local-dev Docker; not yet a K8s expert |

---

## What this curriculum deliberately postpones

- Kubernetes, Swarm orchestration depth
- Service mesh, advanced CNI
- Docker-in-Docker CI complexity beyond concepts
- Proprietary cloud container registries deep-dives (basic registry concepts only)

These are **out of scope** for the beginner→intermediate path defined here.
