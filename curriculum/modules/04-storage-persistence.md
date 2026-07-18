# Module 04 — Storage and Persistence

| Field | Value |
|-------|-------|
| **Difficulty** | Advanced beginner |
| **Estimated study duration** | 2–2.5 hours |
| **Prerequisites** | Module 03; ability to build `welcome-to-docker:dev` |

---

## Learning objectives

1. Explain why data written inside a container can disappear.
2. Choose between bind mounts and named volumes.
3. Mount source code for development workflows.
4. Inspect and clean up volumes safely.
5. Recognize permission pitfalls.

---

## Concepts explained

### Container writable layer

A running container can write files, but those writes live in a **thin writable layer**. Remove the container → those writes are gone (unless stored in a volume/bind mount).

**Analogy:** Editing a photocopy. Throw away the photocopy, edits vanish. Volumes are filing cabinets outside the photocopy.

### Three mount styles

| Type | Syntax idea | Best for |
|------|-------------|----------|
| **Bind mount** | Host path → container path | Live source code, config files you edit on host |
| **Named volume** | Docker-managed named storage | Databases, durable app data |
| **Anonymous volume** | Docker-managed, no friendly name | Easy to forget and leak disk |

### Read-only mounts

`--mount ...,readonly` or `-v host:container:ro` prevents container writes — useful for config and hardened apps.

### Choosing bind vs volume for this repo

| Need | Choice | Why |
|------|--------|-----|
| Edit `src/App.js` live | Bind mount | Files are your project on disk |
| Durable DB files (if added later) | Named volume | Portable, managed by Docker |
| `node_modules` in bind-mounted Node apps | Careful | Host OS binaries may break in Linux container — often use anonymous volume on `node_modules` or install in container only |

### Persistence and this sample app

The congratulations SPA is **stateless**. Persistence lessons still matter because:

1. You will mount source for **dev**.
2. You will attach a **companion service** (e.g., nginx logs or a tiny notes API) with a named volume.
3. You must avoid the classic mistake: “I thought my data was safe inside the container.”

---

## Repository-specific relevance

Paths that matter once source is present:

| Path | Role |
|------|------|
| `welcome-to-docker/src/` | Bind-mount candidate for CRA dev |
| `welcome-to-docker/public/` | Static public assets |
| `welcome-to-docker/package.json` | Dependencies |
| Container `/app` | `WORKDIR` in Dockerfile |

**Assumption:** For a simple persistence demo without inventing a database yet, use a named volume on an nginx sidecar’s log directory, or a bind mount of a local `./data` folder into a companion container.

---

## Commands introduced

| Command | Purpose |
|---------|---------|
| `docker volume ls` | List volumes |
| `docker volume create` | Create named volume |
| `docker volume inspect` | See mountpoint/metadata |
| `docker volume rm` | Delete volume (**🛑** data loss) |
| `docker run -v` / `--mount` | Attach storage |

---

## Guided demonstration

### A — Prove data loss without volumes

```bash
docker run --name lose-data alpine sh -c "echo hello > /tmp/note.txt && cat /tmp/note.txt"
docker rm lose-data
docker run --name lose-data alpine cat /tmp/note.txt
# Expected: file not found — previous container filesystem is gone
docker rm lose-data
```

### B — Named volume keeps data

```bash
docker volume create wtd-notes
docker run --rm -v wtd-notes:/data alpine sh -c "echo persistent > /data/note.txt"
docker run --rm -v wtd-notes:/data alpine cat /data/note.txt
# Expected: persistent
```

### C — Bind mount project docs (safe, read-only)

From repo root (parent `learn-docker`):

```bash
docker run --rm -v "$PWD/README.md:/readme:ro" alpine cat /readme
```

### D — Development-oriented bind mount (conceptual)

```bash
# Example pattern for CRA-style live work (may need node image + npm install)
# Repository-specific paths:
docker run --rm -it \
  -v "$PWD/welcome-to-docker/src:/app/src" \
  -w /app \
  node:22-alpine sh
```

Inside, `ls src` should show your host files.

### Cleanup volumes

```bash
docker volume ls
# 🛑 Deletes volume data permanently
docker volume rm wtd-notes
```

---

## Hands-on exercises

1. Reproduce the data-loss demo with Alpine.
2. Create volume `lab4-data`, write a file, read it from a second container.
3. Bind-mount this repo’s `README.md` read-only into a container and print it.
4. `docker volume inspect lab4-data` and identify the driver.
5. Remove the lab volume deliberately after you are done.

---

## Expected results

- You can predict when data survives `docker rm`.
- Named volume data survives container recreation.
- Bind mount reflects host file changes immediately.
- You hesitate before any `volume rm` or prune command.

---

## Common mistakes

| Mistake | Impact | Prevention |
|---------|--------|------------|
| Assuming container disk is durable | Lost notes/uploads/DB | Use volumes for durable data |
| Bind-mounting over important image dirs | Hides image contents | Mount specific subpaths |
| `docker volume prune` | Deletes unused volumes | **🛑** Confirm nothing important is “unused” |
| Permission denied on bind mount | App cannot write | Align UIDs or adjust permissions carefully |
| Committing secrets via bind mounts into images | Leak | Never `docker commit` secret-filled containers |

---

## Troubleshooting guidance

1. “My edits don’t appear” → confirm bind mount paths; confirm you edit the mounted path.
2. “Permission denied” → `docker exec` and `ls -l`; check user in container (`USER`).
3. “Volume empty after compose down” → did you use `-v` with `compose down`? (`docker compose down -v` is **🛑**).

---

## Knowledge-check questions

1. What happens to files in the writable layer when a container is removed?
2. When should you prefer a named volume over a bind mount?
3. What does `:ro` mean on a bind mount?
4. Why is `docker volume prune` dangerous?
5. Does this React sample need a database volume to show the congratulations page?

---

## Practical challenge

Design (and test) a two-container demo:

1. Container A writes a timestamp into named volume `wtd-shared`.
2. Container B reads and prints it.
3. Document the exact commands in your notes.

Optional: bind-mount `./lab-data` on the host instead and show the file with `cat` on the host.

---

## Completion criteria

- [ ] Demonstrated data loss without volumes.
- [ ] Used a named volume successfully across two containers.
- [ ] Used a bind mount with a repository file.
- [ ] Inspected and removed a volume intentionally.

---

## Optional stretch exercise

Add a tiny companion container (e.g., `nginx`) with a named volume mounted at `/var/log/nginx`. Generate requests, then copy logs out with `docker run --rm -v VOL:/data alpine ls /data`.
