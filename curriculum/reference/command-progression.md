# 4. Command Progression Reference

Safety legend: **✅ Safe** · **⚠️ Potentially disruptive** · **🛑 Destructive**

| Command | Module | Purpose | Example based on this repository | Safety |
|---------|--------|---------|----------------------------------|--------|
| `docker version` | 01 | Show client/server versions | `docker version` | ✅ |
| `docker info` | 01 | Daemon summary | `docker info` | ✅ |
| `docker run` | 01 | Create & start container | `docker run -d -p 8088:80 --name welcome-to-docker docker/welcome-to-docker` | ⚠️ |
| `docker ps` | 01 | List running containers | `docker ps` | ✅ |
| `docker stop` | 01 | Graceful stop | `docker stop welcome-to-docker` | ⚠️ |
| `docker rm` | 01 | Remove container | `docker rm welcome-to-docker` | 🛑 |
| `docker pull` | 02 | Download image | `docker pull docker/welcome-to-docker` | ✅ |
| `docker image ls` | 02 | List images | `docker image ls` | ✅ |
| `docker image inspect` | 02 | Image metadata JSON | `docker image inspect docker/welcome-to-docker` | ✅ |
| `docker image rm` | 02 | Delete image | `docker image rm welcome-to-docker:dev` | 🛑 |
| `docker container ls` | 02 | List containers | `docker container ls -a` | ✅ |
| `docker start` | 02 | Start existing container | `docker start welcome-to-docker` | ⚠️ |
| `docker restart` | 02 | Restart container | `docker restart welcome-to-docker` | ⚠️ |
| `docker exec` | 02 | Run cmd in container | `docker exec -it welcome-to-docker sh` | ⚠️ |
| `docker logs` | 02 | View logs | `docker logs welcome-to-docker` | ✅ |
| `docker inspect` | 02 | Object details | `docker inspect welcome-to-docker` | ✅ |
| `docker stats` | 02 | Resource usage | `docker stats --no-stream welcome-to-docker` | ✅ |
| `docker events` | 02 | Daemon event stream | `docker events` | ✅ |
| `docker system df` | 02 | Disk usage | `docker system df` | ✅ |
| `docker system prune` | 02 | Remove unused data | `docker system prune` | 🛑 |
| `docker build` | 03 | Build image from Dockerfile | `docker build -t welcome-to-docker:dev ./welcome-to-docker` | ⚠️ |
| `docker tag` | 03 | Retag image | `docker tag welcome-to-docker:dev welcome-to-docker:0.1.0` | ✅ |
| `docker history` | 03 | Show layers | `docker history welcome-to-docker:dev` | ✅ |
| `docker volume ls` | 04 | List volumes | `docker volume ls` | ✅ |
| `docker volume create` | 04 | Create volume | `docker volume create wtd-notes` | ⚠️ |
| `docker volume inspect` | 04 | Volume metadata | `docker volume inspect wtd-notes` | ✅ |
| `docker volume rm` | 04 | Delete volume | `docker volume rm wtd-notes` | 🛑 |
| `docker network ls` | 05 | List networks | `docker network ls` | ✅ |
| `docker network create` | 05 | Create network | `docker network create wtd-net` | ⚠️ |
| `docker network inspect` | 05 | Network details | `docker network inspect wtd-net` | ✅ |
| `docker network connect` | 05 | Attach container | `docker network connect wtd-net welcome-to-docker` | ⚠️ |
| `docker network rm` | 05 | Delete network | `docker network rm wtd-net` | ⚠️ |
| `docker compose config` | 06/07 | Validate Compose file | `docker compose config` | ✅ |
| `docker compose build` | 07 | Build project images | `docker compose build web` | ⚠️ |
| `docker compose up` | 07 | Start project | `docker compose up -d` | ⚠️ |
| `docker compose down` | 07 | Tear down project | `docker compose down` | ⚠️ |
| `docker compose down -v` | 07 | Tear down + volumes | `docker compose down -v` | 🛑 |
| `docker compose ps` | 07 | Project status | `docker compose ps` | ✅ |
| `docker compose logs` | 07 | Service logs | `docker compose logs -f web` | ✅ |
| `docker compose exec` | 07 | Exec in service | `docker compose exec web sh` | ⚠️ |
| `docker compose run` | 07 | One-off container | `docker compose run --rm web ...` | ⚠️ |
| `docker compose restart` | 07 | Restart services | `docker compose restart web` | ⚠️ |
| `docker compose stop` | 07 | Stop services | `docker compose stop` | ⚠️ |
| `docker compose start` | 07 | Start stopped services | `docker compose start` | ⚠️ |
| `docker compose pull` | 07 | Pull service images | `docker compose pull` | ✅ |
| `docker compose images` | 07 | Images used by project | `docker compose images` | ✅ |
| `docker compose top` | 07 | Processes in services | `docker compose top` | ✅ |
| `docker build --no-cache` | 08 | Force full rebuild | `docker build --no-cache -t welcome-to-docker:dev ./welcome-to-docker` | ⚠️ |
| `docker build --target` | 09 | Build specific stage | `docker build --target runner -t welcome-to-docker:hardened ./welcome-to-docker` | ⚠️ |
| `docker run --read-only` | 09 | Read-only root FS | `docker run --read-only -p 8088:3000 welcome-to-docker:hardened` | ⚠️ |
| `docker run --memory` / `--cpus` | 09 | Resource limits | `docker run --memory 256m --cpus 0.5 -p 8088:3000 welcome-to-docker:hardened` | ⚠️ |

## Flag progression (introduced gradually)

| Flag | Module | Meaning | Repo example |
|------|--------|---------|--------------|
| `--name` | 01 | Name container | `--name welcome-to-docker` |
| `-d` | 01 | Detached | `-d` |
| `-p` | 01 | Publish ports | `-p 8088:80` (Hub) / `-p 8088:3000` (build) |
| `-it` | 02 | Interactive TTY | `docker exec -it ... sh` |
| `--rm` | 02 | Auto-remove on exit | `docker run --rm alpine echo hi` |
| `-e` | 02 | Env var | `-e LEARNING_TRACK=docker` |
| `--env-file` | 02/07 | Env file | `--env-file .env` |
| `-v` / `--mount` | 04 | Storage | `-v wtd-notes:/data` |
| `--network` | 05 | Attach network | `--network wtd-net` |
| `--restart` | 02/07 | Restart policy | `--restart unless-stopped` |

## Notes

- Prefer **`docker compose`** (plugin) over legacy **`docker-compose`**.
- Destructive commands are marked **🛑** — pause and confirm targets before running.
- Exact log text and image digests vary; do not treat sample outputs as cryptographic truths.
