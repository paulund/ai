---
name: docker
description: Use when writing or modifying Docker Compose files, configuring services, healthchecks, and container dependencies.
---

## Core Workflow

1. **Define services** — each service gets its own named block with image, ports, volumes, environment
2. **Add healthchecks** — on any service that others depend on being ready (databases, caches, search)
3. **Set depends_on conditions** — use `service_healthy` for services with healthchecks, `service_completed_successfully` for migration jobs
4. **Configure networking** — use custom networks to isolate internal services from public-facing ones
5. **Separate environments** — use override files to keep base config clean and environment differences explicit

## Reference Guide

| Topic | Reference | Load When |
|-------|-----------|-----------|
| Healthchecks | `references/healthchecks.md` | Configuring healthcheck commands, timing, depends_on conditions |
| Networking | `references/networking.md` | Custom networks, service DNS, port exposure vs publishing |
| Volumes | `references/volumes.md` | Named volumes, bind mounts, tmpfs, sharing between services |
| Environment | `references/environment.md` | .env files, variable substitution, secrets, env_file |
| Profiles | `references/profiles.md` | Optional services (Mailpit, Adminer) toggled with --profile |
| Override Files | `references/override.md` | Per-environment compose files for dev, CI, and production |

## Constraints

### MUST DO

- Add healthchecks to any service that dependent containers connect to (MySQL, Redis, Postgres)
- Use `service_healthy` in `depends_on` whenever a healthcheck is defined on the target service
- Use `service_completed_successfully` for one-off jobs (migrations, seed scripts)
- Use custom networks to prevent databases being reachable from public-facing services
- Use `expose` for internal ports, `ports` only for services that need host access
- Add `.env` and `docker-compose.override.yml` to `.gitignore`
- Commit `.env.example` and `docker-compose.override.yml.example` as templates

### MUST NOT DO

- Use `service_started` for databases — a running container is not a ready database
- Hardcode secrets in the compose file — use `.env` or Docker secrets
- Use bind mounts in production
- Publish database ports in production compose files
