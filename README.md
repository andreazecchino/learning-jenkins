# Welcome to my Jenkins Learning Portfolio

This repository documents my journey through the Jenkins automation server. Each numbered folder is a self-contained lesson, ordered from the simplest setup to more advanced pipelines.

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/) installed and running (all lessons run Jenkins and its build tools in containers)
- A terminal with `bash`-compatible syntax (on Windows, use WSL or Git Bash)
- A Git repository with a small Python project to build. I used this personal project [poc-py-llm](https://github.com/andreazecchino/poc-py-llm) (any project with a `pyproject.toml`, a `uv.lock` and a `tests/` folder will works).

## Lessons

| # | Lesson | What you will learn |
|---|--------|---------------------|
| 00 | [Installation](00-installation/README.md) | Run Jenkins in Docker, with Docker-in-Docker so builds can use containers |
| 01 | [Freestyle project](01-freestyle-project/README.md) | Create a classic job from the UI and scan code with Trivy |
| 02 | [Pipeline](02-pipeline/README.md) | Pipeline as code: a `Jenkinsfile` with lint, unit tests and a security scan |

## Notes

- The setup in lesson 00 uses `--privileged` and exposes the Docker daemon port. It is meant for a local learning environment, not for production.
- Docker images are pinned to specific versions so the lessons stay reproducible.
