# Module 10 — Repository Capstone

| Field | Value |
|-------|-------|
| **Difficulty** | Intermediate |
| **Estimated study duration** | 3–5 hours |
| **Prerequisites** | Modules 01–09 |

---

## Learning objectives

Integrate everything into a coherent Dockerized workflow for **this** repository that another beginner developer could start from documentation alone.

---

## Concepts explained

A capstone is not new theory — it is **proof of practical proficiency**. You will make deliberate design decisions and document tradeoffs:

- Hub image vs local build
- Bind mounts for development vs immutable production images
- Whether nginx belongs in front
- How healthchecks fail closed or open
- What you refuse to put in git

---

## Repository-specific relevance

Capstone centers on:

| Artifact | Location (recommended) |
|----------|------------------------|
| Application Dockerfile | `welcome-to-docker/Dockerfile` (improved) |
| `.dockerignore` | `welcome-to-docker/.dockerignore` |
| Compose | `compose.yaml` at repo root |
| Env example | `.env.example` |
| Nginx config | `deploy/nginx/default.conf` |
| Developer docs | `README.md` Docker section **or** `docs/DOCKER.md` |
| Debugging checklist | `docs/DOCKER_DEBUG.md` |

If the submodule is still empty, your first capstone task is to **obtain application source** (clone upstream or fix gitlink) so builds work from this repo.

---

## Commands introduced

No brand-new commands required — use the full toolkit from Modules 01–09.

---

## Guided demonstration

### Capstone checklist (instructor walkthrough order)

1. Ensure `welcome-to-docker` source is present.
2. Improve Dockerfile (cache order, pin, multi-stage, non-root).
3. Expand `.dockerignore`.
4. Build & tag: `welcome-to-docker:capstone`.
5. Run via CLI with `-e` / `--env-file` placeholders.
6. Add named volume for proxy logs or lab data.
7. Create network via Compose (prefer Compose-managed network).
8. Add nginx (or companion) service.
9. Write complete `compose.yaml` + `.env.example`.
10. Add healthchecks where suitable.
11. Verify `compose ps`, `logs`, browser access.
12. Introduce and fix two intentional bugs (port + DNS).
13. Record image size optimization results.
14. Write developer quick-start docs.
15. Write design-decision notes (10–15 lines).

---

## Hands-on exercises

The capstone **is** the exercise. Follow the Required capstone deliverables below.

---

## Expected results

A working multi-container local stack for the learning app, documented and optimized, with evidence of troubleshooting skill.

---

## Common mistakes

| Mistake | Avoidance |
|---------|-----------|
| Shipping secrets in `.env` | Only `.env.example` in git |
| Documenting Hub ports for a built image | Match docs to actual listen port |
| Leaving submodule empty and undocumented | Document setup step 0 |
| Claiming “production ready” without scanning/auth | Be honest: “production-oriented local setup” |
| Skipping cleanup instructions | Include `compose down` |

---

## Troubleshooting guidance

Use your Module 08 checklist. Capstone grading rewards **process evidence**, not only a green stack.

---

## Knowledge-check questions

1. Summarize containers vs VMs in two sentences.
2. Why does this SPA still benefit from Compose?
3. What is your restart policy and why?
4. What is still unsafe about env vars for secrets?
5. What would you add next for a real production deployment (high level)?

---

## Practical challenge (required capstone)

Complete **all** of the following:

1. Containerize the repository application.
2. Create or improve its Dockerfile.
3. Add an appropriate `.dockerignore`.
4. Build and tag the image (`welcome-to-docker:capstone`).
5. Run with Docker CLI and env-based config placeholders.
6. Persist required/lab data via volume or bind mount as appropriate.
7. Use a custom network (Compose network is fine).
8. Add required supporting service(s) (nginx strongly recommended).
9. Create complete Docker Compose configuration.
10. Add healthchecks where suitable.
11. Verify logs and service status.
12. Troubleshoot two intentionally introduced problems (document them).
13. Optimize the final image (measure size).
14. Document how another developer starts the project.
15. Explain key design decisions.

---

## Completion criteria

- [ ] `docker compose up -d` brings stack up from clean state (documented prerequisites).
- [ ] Browser shows congratulations page through your published entryport.
- [ ] Docs include ports, env, cleanup, and Hub-vs-build notes.
- [ ] Rubric self-score ≥ 80% (see assessments).
- [ ] No real secrets in git.

---

## Optional stretch exercise

- Add a `devtools` profile with a debug container.
- Add `compose.prod.yaml` without bind mounts.
- Build multi-arch with `docker buildx` (see upstream `MAINTAINERS.md` ideas).
