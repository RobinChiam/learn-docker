# 6. Assessments

## Short quizzes

### Quiz A — After Module 01–02 (`quiz-01` / foundations)

See also answer key at the bottom of this file.

1. An image is to a container as a ______ is to a ______.
2. Which component actually runs containers: client or daemon?
3. In `-p 8088:80`, which port is on the host?
4. What does `docker stop` do that `docker rm` does not?
5. True/False: `docker logs -f` stops the container when you press Ctrl+C.

### Quiz B — After Module 03–04

1. Does `EXPOSE` publish a port to your laptop?
2. Why is `.dockerignore` important?
3. Why might you copy `package*.json` before `src/`?
4. What happens to files in the container writable layer after `docker rm`?
5. Name one good use of a bind mount for `welcome-to-docker`.

### Quiz C — After Module 05–07

1. Why prefer a user-defined bridge for two containers?
2. From nginx, should you call `http://localhost:3000` or `http://web:3000` to reach the app service?
3. What does `docker compose config` do?
4. What extra does `docker compose down -v` delete?
5. Is Docker Compose the same as Kubernetes?

### Quiz D — After Module 08–09

1. Give one hypothesis for “connection refused” on a published port.
2. Why is `serve@latest` risky?
3. Name two places secrets must not live.
4. What is the main benefit of a multi-stage build for this CRA app?
5. Does running Alpine automatically make you secure?

---

## Practical checkpoints

| Checkpoint | When | Pass criteria |
|------------|------|---------------|
| CP1 | End Module 02 | Run/stop/rm Hub image; show logs + exec without GUI |
| CP2 | End Module 03 | Build `welcome-to-docker:lab3`; open page on `8088→3000` |
| CP3 | End Module 05 | Two containers communicate by DNS name on custom network |
| CP4 | End Module 07 | `compose up -d` starts documented stack; `compose ps` healthy/running |
| CP5 | End Module 09 | Hardened image runs non-root; size noted vs lab3 |

---

## Debugging exercises

| ID | Setup | Learner task |
|----|-------|--------------|
| D1 | Hub image with `-p 8088:3000` | Diagnose and fix mapping |
| D2 | Compose proxy to `http://localhost:80` | Fix upstream to service name |
| D3 | `build.context: ./welcome-to-docker` while directory empty | Identify submodule/source problem |
| D4 | Healthcheck calls missing binary | Adjust test or install tool |
| D5 | Bind mount shadows app files incorrectly | Inspect mounts; fix path |

Each write-up must include: symptom, hypothesis, proof command, fix, verification.

---

## Mid-course project (after Module 07)

**Title:** Compose the welcome stack

**Deliverables**

1. Root `compose.yaml` with `web` + one supporting service.
2. `.env.example` with `HOST_PORT`.
3. Custom network.
4. At least one volume.
5. One-page start guide.

**Time box:** ~2 hours within Module 07 duration.

**Pass bar:** Another learner can bring the stack up using only your guide.

---

## Final capstone rubric

Score each row 0–4. **Passing ≥ 36 / 44 (≈ 80%).**

| Criterion | 0 | 2 | 4 | Weight notes |
|-----------|---|---|---|--------------|
| **Docker concepts** | Confused terms | Mostly correct | Clear, accurate explanations in docs/Q&A | |
| **CLI proficiency** | GUI-only / fragile | Core lifecycle OK | Fluent debug + cleanup | |
| **Dockerfile quality** | Broken / cargo-cult | Works | Readable, cache-aware, pinned basics | |
| **Image size & build efficiency** | Huge / no thought | Some improvement | Multi-stage + measured comparison | |
| **Volume usage** | Misused / data loss | Basic mounts | Appropriate bind vs named choices | |
| **Network design** | Host-network hacks | Default only | User-defined network + correct DNS | |
| **Compose quality** | Invalid / incomplete | Runs | Clear services, env, restart, health | |
| **YAML correctness** | Frequent parse errors | Minor issues | Clean, validated with `config` | |
| **Security awareness** | Secrets in image/git | Some awareness | Non-root, pins, no secrets, honest limits | |
| **Debugging ability** | Random commands | Fixes with hints | Hypothesis loop with evidence | |
| **Documentation quality** | Missing | Partial | Another beginner can start cold | |

**44** = 11 criteria × 4.

### Capstone evidence packet

Submit / commit:

- Dockerfiles + `.dockerignore`
- `compose.yaml` + `.env.example`
- Size comparison notes
- Debug write-ups (2 bugs)
- `README` / `docs/DOCKER.md`
- Design decisions (short)

---

## Answer key (short quizzes)

### Quiz A

1. recipe/template → running instance (cookie cutter → cookie)
2. Daemon
3. 8088 (left)
4. Stop leaves the container filesystem; rm deletes the container
5. False

### Quiz B

1. No
2. Shrinks context; avoids secrets/junk layers
3. Preserve npm install cache when only source changes
4. They are deleted with the container
5. Live-edit `src/` during development

### Quiz C

1. Embedded DNS by service/container name
2. `http://web:3000` (or `:80` for Hub)
3. Validates/renders the Compose config
4. Named/anonymous volumes declared for the project
5. No

### Quiz D

1. Nothing listening on container port / wrong mapping / app crashed
2. Non-reproducible builds; surprise breaking changes
3. Dockerfile layers, git history, image history, world-readable compose committed secrets
4. Exclude build toolchain & source from final runtime
5. No
