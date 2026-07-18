# Module 08 — Debugging and Troubleshooting

| Field | Value |
|-------|-------|
| **Difficulty** | Intermediate |
| **Estimated study duration** | 2–3 hours |
| **Prerequisites** | Modules 01–07 |

---

## Learning objectives

1. Use a hypothesis-driven debugging loop.
2. Diagnose common container, network, volume, and Compose failures.
3. Map symptoms to the right Docker commands.
4. Fix intentionally broken scenarios related to this repository.

---

## Concepts explained

### Debugging loop

```text
1. Observe symptom (error message, blank page, exit code)
2. Form ONE hypothesis
3. Pick the smallest command that can confirm/deny it
4. Fix or form a new hypothesis
5. Record what you learned
```

Avoid random command spam — it hides the signal.

### High-value diagnostic tools

| Tool | Reveals |
|------|---------|
| `docker ps -a` / `compose ps` | Exit codes, restarts |
| `docker logs` / `compose logs` | App errors |
| `docker inspect` | Ports, env, mounts, networks |
| `docker exec` | Live filesystem & connectivity |
| `docker events` | Timing of die/oom |
| `docker compose config` | YAML/merge mistakes |
| `docker system df` | Disk pressure |

---

## Repository-specific relevance

**Classic bugs for this project**

| Bug | Why it happens here |
|-----|---------------------|
| `-p 8088:3000` on Hub image | Hub listens on **80** |
| `-p 8088:80` on built image | Dockerfile serves **3000** |
| Curl `localhost` from proxy to reach web | Wrong namespace |
| Missing build context / empty submodule | Dockerfile path invalid |
| Stale cache after Dockerfile logic change | Need `--no-cache` sometimes |
| Healthcheck using missing binary | Alpine images differ (`wget`/`curl`) |
| CRA `API_URL=http://localhost:3001` in browser code | Browser localhost ≠ container localhost |

---

## Commands introduced / reinforced

Focus on diagnostic combinations rather than new syntax:

- `docker logs --tail 100 NAME`
- `docker inspect -f '{{.State.Status}} {{.State.ExitCode}}' NAME`
- `docker inspect -f '{{json .NetworkSettings.Ports}}' NAME`
- `docker compose logs --no-color SERVICE`
- `docker compose run --rm SERVICE sh` (if shell exists)

---

## Guided demonstration

### Scenario: container exits immediately

```bash
docker run --name boom alpine
docker ps -a
docker logs boom
docker inspect -f '{{.State.ExitCode}}' boom
docker rm boom
```

Hypothesis: Alpine default command finished → exit 0. Not a crash — expected.

### Scenario: wrong port mapping (repository-specific)

```bash
# Intentionally wrong for Hub image:
docker run -d --name badport -p 8088:3000 docker/welcome-to-docker
curl -v http://localhost:8088
# Expect failure / connection refused

docker port badport
docker inspect -f '{{json .Config.ExposedPorts}}' badport
docker rm -f badport

# Correct:
docker run -d --name goodport -p 8088:80 docker/welcome-to-docker
curl -I http://localhost:8088
docker rm -f goodport
```

### Scenario: localhost between containers

```bash
docker network create debug-net
docker run -d --name web --network debug-net docker/welcome-to-docker
docker run --rm --network debug-net curlimages/curl:8.5.0 -I http://localhost:80 || true
docker run --rm --network debug-net curlimages/curl:8.5.0 -I http://web:80
docker rm -f web
docker network rm debug-net
```

---

## Hands-on exercises

Work each symptom → hypothesis → proof → fix:

1. Port conflict on 8088.
2. Typo in image name.
3. Compose service with bad indent (config fails).
4. Volume mount that hides `/usr/share/nginx/html`.
5. App build context pointing at empty `welcome-to-docker/`.
6. Stale image tag confusion (`welcome-to-docker` local vs `docker/welcome-to-docker` Hub).

---

## Expected results

- You debug with evidence, not guesses alone.
- You can explain at least five failure modes from the list below.
- You maintain a personal debugging checklist (lab deliverable).

---

## Common failure catalog

| Problem | First checks |
|---------|--------------|
| Exits immediately | `ps -a`, `logs`, command/entrypoint |
| Wrong command/entrypoint | `inspect` Config.Cmd / Entrypoint |
| Missing env vars | `inspect` Env; compose `config` |
| Port conflicts | OS bind error; change host port |
| Connection refused | Process listening? right port? |
| localhost misuse | Use service DNS name |
| DNS issues | Same network? correct service name? |
| Missing files in image | `.dockerignore`; COPY paths; exec `ls` |
| Wrong build context | Path in `docker build` / compose `context` |
| Stale build cache | `--no-cache`; confirm layer invalidation |
| Permission errors | USER, bind mount UIDs |
| Volume mistakes | inspect Mounts; path typos |
| DB startup timing | healthcheck + depends_on condition |
| Failed healthchecks | test command exists; correct URL/port |
| Large images | history; multi-stage; alpine |
| Disk space | `system df`; careful prune |
| Compose errors | `compose config` |

---

## Troubleshooting guidance

**Template notes for learners**

```text
Symptom:
Hypothesis:
Command used to test:
Result:
Next hypothesis / fix:
```

---

## Knowledge-check questions

1. Why might `docker run alpine` exit immediately with code 0?
2. How do you confirm which ports a container published?
3. What is the first Compose command to run when YAML might be wrong?
4. Why can browser `localhost` and container `localhost` disagree?
5. When is `--no-cache` appropriate?

---

## Practical challenge

Instructor (or self) introduces **three** bugs into your Compose setup (wrong port, bad service name in nginx config, missing volume path). Fix all three using the debugging loop. Record hypotheses and proof commands.

---

## Completion criteria

- [ ] Written personal debugging checklist committed to notes/`curriculum` lab folder.
- [ ] Solved wrong-port Hub vs build confusion deliberately.
- [ ] Demonstrated DNS vs localhost failure and fix.
- [ ] Used inspect/logs/exec evidence on every fix.

---

## Optional stretch exercise

Capture `docker events --since 10m` during a failed `compose up` and map events to the failure timeline.
