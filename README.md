# Learn Docker

This repository is a learning project for Docker: setup, containers, volumes, and practical containerization.

## Start learning

The full beginner→intermediate curriculum lives in [`curriculum/`](curriculum/README.md).

**Begin immediately:** open [`curriculum/08-first-lesson.md`](curriculum/08-first-lesson.md).

## Application under study

The intended sample app is Docker’s official [`welcome-to-docker`](https://github.com/docker/welcome-to-docker) (React SPA).

| Path | Notes |
|------|--------|
| `welcome-to-docker/` | Intended app directory (may be an uninitialized git submodule in some clones) |
| Hub quick start | `docker run -d -p 8088:80 --name welcome-to-docker docker/welcome-to-docker` |
| Built-from-source port | Container **3000** (see upstream `Dockerfile`) |

See the [repository assessment](curriculum/01-repository-assessment.md) for stack details, gaps, and assumptions.
