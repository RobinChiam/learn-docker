# Docker Curriculum — Learn Docker with This Repository

A practical, beginner-to-intermediate Docker curriculum designed around this repository (`RobinChiam/learn-docker`) and its intended sample application, Docker’s official [`welcome-to-docker`](https://github.com/docker/welcome-to-docker) React app.

## How to use this curriculum

1. Read **[01 — Repository assessment](01-repository-assessment.md)** so you understand what you are containerizing.
2. Skim the **[02 — Learner roadmap](02-learner-roadmap.md)** for pacing and milestones.
3. Start immediately with **[08 — First lesson](08-first-lesson.md)** (also Module 01).
4. Work through modules **01 → 10** in order under [`modules/`](modules/).
5. Follow the progressive **[lab sequence](labs/lab-sequence.md)** as you go.
6. Use the **[command progression](reference/command-progression.md)** table as a cheat sheet.
7. Complete **[assessments](assessments/)** at the checkpoints and finish with the capstone rubric.

## Document map

| # | Document | Purpose |
|---|----------|---------|
| 1 | [Repository assessment](01-repository-assessment.md) | What this repo is, stack, ports, gaps, assumptions |
| 2 | [Learner roadmap](02-learner-roadmap.md) | Phases, duration, milestones, skill levels |
| 3 | [Full curriculum (modules)](modules/) | Ten progressive modules with full lesson structure |
| 4 | [Command progression](reference/command-progression.md) | Commands by module, safety, repo examples |
| 5 | [Repository lab sequence](labs/lab-sequence.md) | End-to-end labs producing real Docker artifacts |
| 6 | [Assessments](assessments/) | Quizzes, checkpoints, mid-course project, capstone rubric |
| 7 | [Recommended repository changes](07-recommended-changes.md) | Essential → production recommendations (not applied yet) |
| 8 | [First lesson](08-first-lesson.md) | Complete Lesson 1 so you can begin immediately |

## Important repository facts (quick)

| Item | Finding |
|------|---------|
| Parent repo | Learning shell: `README.md` + intended `welcome-to-docker` content |
| Sample app | React 18 Create React App; production static files served with `serve` |
| Existing Docker files | Present in upstream `docker/welcome-to-docker`: `Dockerfile`, `.dockerignore` |
| Container port (built from source) | **3000** |
| Published Hub image port | **80** (`docker/welcome-to-docker` on Docker Hub) |
| Host demo port | **8088** (as documented upstream) |
| Database / queue / cache | **None** in the sample app |
| Submodule status | `welcome-to-docker` is recorded as a gitlink but is **not initialized** locally |

> **Assumption:** Until the submodule is fixed, treat `/tmp` clones or a local copy of [`docker/welcome-to-docker`](https://github.com/docker/welcome-to-docker) at commit `c8cd4b3` as the application under study. Recommended fixes are listed in [07-recommended-changes.md](07-recommended-changes.md).

## Safety legend used throughout

| Marker | Meaning |
|--------|---------|
| ✅ Safe | Read-only or easily reversible |
| ⚠️ Potentially disruptive | Stops/restarts containers or changes runtime state |
| 🛑 Destructive | Deletes containers, images, volumes, networks, or cache — confirm before running |
