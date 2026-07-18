# Module 06 — YAML Fundamentals for Docker Compose

| Field | Value |
|-------|-------|
| **Difficulty** | Beginner |
| **Estimated study duration** | 1–1.5 hours |
| **Prerequisites** | Modules 01–05 recommended; no prior YAML required |

---

## Learning objectives

1. Read and write the YAML subset Compose needs.
2. Avoid indentation and quoting pitfalls.
3. Use comments and simple environment substitution.
4. Validate small YAML snippets before writing a full Compose file.

---

## Concepts explained

YAML is a **whitespace-sensitive** data format. Compose files are YAML documents describing services, networks, and volumes.

### Key-value mappings

```yaml
services:
  web:
    image: docker/welcome-to-docker
```

`services` maps to an object; `web` is a service key; `image` is a property.

### Nested mappings

Indent with **spaces** (Compose examples typically use 2 spaces). Do not use tabs.

### Lists

```yaml
ports:
  - "8088:80"
  - "8443:443"
```

### Strings, numbers, booleans

```yaml
restart: unless-stopped    # string
replicas: 2                # number (when supported by the key)
privileged: false          # boolean
```

### Quoting

Quote when values contain `:`, `#`, `{`, or look like booleans:

```yaml
ports:
  - "8088:80"          # good — contains colon
environment:
  TITLE: "Hello: Docker"
  DEBUG: "true"        # string, not boolean — if you need a string
```

### Multiline values

```yaml
command: |
  echo line1
  echo line2
```

Or folded:

```yaml
description: >
  This is one logical line
  split for readability.
```

### Comments

```yaml
# This is a comment
image: nginx:alpine   # trailing comment
```

### Environment-variable substitution (Compose)

```yaml
ports:
  - "${HOST_PORT:-8088}:80"
```

| Syntax | Meaning |
|--------|---------|
| `${VAR}` | Replace from shell or `.env` |
| `${VAR:-default}` | Default if unset/empty |
| `${VAR:?error}` | Error if unset |

### Common indentation errors

```yaml
# BAD — ports wrongly indented under image
services:
  web:
    image: nginx
  ports:
      - "80:80"
```

```yaml
# GOOD
services:
  web:
    image: nginx
    ports:
      - "80:80"
```

---

## Repository-specific relevance

You will soon write `compose.yaml` at the **learn-docker repo root** (recommended) or beside `welcome-to-docker/`, defining at least:

- `web` / `app` service for the welcome UI
- later: `proxy`, networks, volumes, env files

No Compose file exists in the parent repo yet — Module 06 prepares you to author it safely.

---

## Commands introduced

| Command | Purpose |
|---------|---------|
| `docker compose config` | Render and validate Compose file (**✅** read-only) |

*(Full Compose workflow arrives in Module 07.)*

---

## Guided demonstration

Create a temporary file `compose.sandbox.yaml`:

```yaml
# Learning sandbox — not the final project file
services:
  web:
    image: docker/welcome-to-docker
    ports:
      - "8088:80"
    environment:
      # Placeholder teaching vars — app may ignore them
      LEARNING_TRACK: docker-basics
      GREETING: "Welcome to Docker"
    restart: unless-stopped
```

Validate:

```bash
docker compose -f compose.sandbox.yaml config
```

Expected: Compose prints a normalized YAML config or exits 0. Errors will cite line/indent issues.

---

## Hands-on exercises

**Exercise A — Fix broken YAML**

Broken:

```yaml
services:
web:
    image: docker/welcome-to-docker
   ports:
     - 8088:80
```

Rewrite it correctly and validate with `docker compose config`.

**Exercise B — Lists and nesting**

Add a second service `curlbox` with image `curlimages/curl:8.5.0` and command that sleeps (so Compose can start it). You may leave networking for Module 07.

**Exercise C — Substitution**

Use `${HOST_PORT:-8088}:80` and run `HOST_PORT=8099 docker compose -f ... config` to see substitution.

---

## Expected results

- You can spot tab/indent mistakes quickly.
- `docker compose config` becomes your first validation step.
- You quote port mappings consistently.

---

## Common mistakes

| Mistake | Symptom |
|---------|---------|
| Tabs | Cryptic parse errors |
| Missing quotes on `8088:80` | Sometimes OK, sometimes parsed oddly — quote them |
| Wrong nesting | Service keys become top-level keys |
| Mixing `docker-compose.yml` mental model with wrong version keys | Prefer modern Compose Spec without obsolete `version:` when using recent Compose |

> Modern Compose uses the **Compose Specification**. A top-level `version:` key is unnecessary on current Docker Compose and can be omitted.

---

## Troubleshooting guidance

1. Run `docker compose config` after every edit.
2. Compare your indentation to a known-good snippet character-by-character.
3. If substitution fails, export the variable or add `.env` beside the compose file.

---

## Knowledge-check questions

1. Why is indentation critical in YAML?
2. Should port mappings be quoted? Why?
3. What does `${HOST_PORT:-8088}` mean?
4. What does `docker compose config` do?
5. Is a `version:` field required in modern Compose files?

---

## Practical challenge

Write `compose.sandbox.yaml` that defines:

1. Service `web` using `docker/welcome-to-docker`
2. Port `${HOST_PORT:-8088}:80`
3. Restart policy `unless-stopped`
4. A comment describing that this is a learning file

Validate with `docker compose config`. Do **not** `up` yet if you prefer to wait for Module 07 — or `up` briefly and `down` if you want early feedback.

---

## Completion criteria

- [ ] Corrected at least one intentionally broken YAML sample.
- [ ] Validated a Compose file with `docker compose config`.
- [ ] Used variable substitution once successfully.

---

## Optional stretch exercise

Write a multiline `command` using `|` for a service and validate it.
