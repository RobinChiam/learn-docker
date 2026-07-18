# Module 02 — Docker CLI Fundamentals

| Field | Value |
|-------|-------|
| **Difficulty** | Beginner |
| **Estimated study duration** | 2–3 hours |
| **Prerequisites** | Module 01 complete; Docker daemon running |

---

## Learning objectives

1. Navigate image and container commands confidently.
2. Use common `docker run` flags intentionally (not by copy-paste).
3. Inspect logs, processes, and resource usage.
4. Clean up unused resources carefully.
5. Distinguish safe vs destructive commands.

---

## Concepts explained

### Images vs containers in daily CLI work

- **Images** are templates: pull, list, inspect, tag, remove.
- **Containers** are instances: run, list, stop, start, restart, exec, logs, remove.

### Detached vs interactive

| Mode | Flag | When to use |
|------|------|-------------|
| Detached | `-d` | Long-running servers (our welcome app) |
| Interactive + TTY | `-it` | Shells, REPLs, debugging |
| Auto-remove | `--rm` | Throwaway containers that should vanish on exit |

### Publishing ports and env vars

- `-p HOST:CONTAINER` publishes a port to your machine.
- `-e KEY=value` sets one environment variable.
- `--env-file file` loads many variables (do not commit secrets).

### Mounts and networks (preview)

Module 02 introduces the flags; Modules 04–05 go deep:

- `-v` / `--mount` attach storage
- `--network` place a container on a network
- `--restart` policy for crash recovery

---

## Repository-specific relevance

You will practice CLI on:

1. Hub image `docker/welcome-to-docker` (port **80**)
2. Later, locally built `welcome-to-docker` (port **3000**)

Always check which image you are running before choosing `-p`.

---

## Commands introduced

| Command | What it does | Why use it |
|---------|--------------|------------|
| `docker pull` | Download an image | Pre-fetch or update |
| `docker image ls` | List images | See what you have locally |
| `docker image inspect` | JSON details | Digests, env, exposed ports |
| `docker image rm` | Delete image | Free disk (**🛑**) |
| `docker container ls` / `docker ps` | List containers | See state |
| `docker start` / `restart` | Start existing container | Resume without recreate |
| `docker exec` | Run command in running container | Debug shells |
| `docker logs` | Show container stdout/stderr | Failures & access logs |
| `docker inspect` | Container/image JSON | Networks, mounts, IP |
| `docker stats` | Live CPU/mem | Resource sanity |
| `docker events` | Daemon event stream | See create/start/die |
| `docker system df` | Disk usage summary | Spot bloat |
| `docker system prune` | Delete unused data | Cleanup (**🛑**) |

### Important `docker run` flags (gradual)

| Flag | Meaning |
|------|---------|
| `--name` | Assign a name |
| `-d` | Detached |
| `-it` | Interactive terminal |
| `--rm` | Remove on exit |
| `-p` | Publish ports |
| `-e` / `--env-file` | Environment |
| `-v` / `--mount` | Storage |
| `--network` | Network attachment |
| `--restart` | Restart policy (`no`, `on-failure`, `always`, `unless-stopped`) |

---

## Guided demonstration

```bash
# ✅ Pull explicitly (also happens automatically on run)
docker pull docker/welcome-to-docker

docker image ls
docker image inspect docker/welcome-to-docker
```

In inspect output, look for `"ExposedPorts"` and `"Cmd"`.

```bash
docker run -d --name wtd-cli -p 8088:80 --restart unless-stopped docker/welcome-to-docker

docker ps
docker logs wtd-cli
docker logs -f wtd-cli   # Ctrl+C to stop following (does not stop container)

docker exec wtd-cli ls /
# If the image has a shell:
docker exec -it wtd-cli sh

docker stats --no-stream wtd-cli
docker inspect wtd-cli
```

```bash
# ⚠️ Stop / start / restart
docker stop wtd-cli
docker start wtd-cli
docker restart wtd-cli
```

```bash
# 🛑 Remove container, then optionally image
docker stop wtd-cli
docker rm wtd-cli
# docker image rm docker/welcome-to-docker   # only if you intend to delete the image
```

```bash
docker system df
# 🛑 Review carefully before pruning
# docker system prune
```

---

## Hands-on exercises

1. List images and identify `docker/welcome-to-docker`.
2. Run it named `lab-cli` on host port `8088`.
3. Use `docker logs` and `docker exec` to explore.
4. Restart it and confirm it still serves the page.
5. Stop, remove, and confirm with `docker ps -a`.

---

## Expected results

- You can explain each flag you used.
- Logs show the process serving HTTP (exact text varies by image).
- `inspect` shows port bindings under `NetworkSettings.Ports`.
- Container can be restarted without rebuilding.

---

## Common mistakes

| Mistake | Result | Recovery |
|---------|--------|----------|
| `docker rm` on running container | Error | `docker stop` first, or `docker rm -f` (**⚠️**) |
| `docker system prune -a --volumes` | Mass deletion | Avoid until you understand; never run blindly |
| Forgetting `-d` | Terminal blocked by foreground process | Ctrl+C may stop container; use `-d` for servers |
| `exec` on stopped container | Error | `docker start` first |

---

## Troubleshooting guidance

Form a hypothesis, then verify:

1. Is it running? → `docker ps -a`
2. Did it crash? → `docker logs NAME`
3. Wrong ports? → `docker port NAME` or `inspect`
4. Disk full? → `docker system df`

---

## Knowledge-check questions

1. What is the difference between `docker stop` and `docker rm`?
2. When would you use `--rm`?
3. Does `docker logs -f` stop the container when you Ctrl+C?
4. What does `docker system prune` delete by default (without extra flags)?
5. Why is `docker image rm` dangerous on a shared machine?

---

## Practical challenge

Without using Docker Desktop UI:

1. Pull `docker/welcome-to-docker`.
2. Run it detached with `--name challenge-02` and `-p 8088:80`.
3. Capture one line of logs.
4. Exec a directory listing.
5. Stop and remove **only** that container.
6. Show `docker system df` output.

---

## Completion criteria

- [ ] Used at least 10 distinct Docker CLI commands successfully.
- [ ] Explained `-d`, `-p`, `--name`, `--rm` in your own words.
- [ ] Cleaned up lab containers without pruning the whole system.

---

## Optional stretch exercise

Run `docker events` in one terminal while you `run`/`stop`/`rm` in another. Describe three event lines you saw.
