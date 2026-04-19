---
name: laravel-sail
description: Use when working with Laravel Sail, including docker-compose configuration, service management, and running commands inside containers.
---

## Core Workflow

1. **Identify the command context** — determine if the command should run inside Sail or on the host
2. **Use Sail wrappers** — always use `./vendor/bin/sail` instead of raw docker commands
3. **Manage services** — start, stop, and configure docker-compose services

## Common Commands

| Task | Command |
|------|---------|
| Start | `./vendor/bin/sail up -d` |
| Stop | `./vendor/bin/sail down` |
| Artisan | `./vendor/bin/sail artisan migrate` |
| Composer | `./vendor/bin/sail composer install` |
| NPM | `./vendor/bin/sail npm run dev` |
| Tests | `./vendor/bin/sail composer run test` |
| Tinker | `./vendor/bin/sail artisan tinker` |
| Shell | `./vendor/bin/sail shell` |

## Constraints

### MUST DO

- Always prefix commands with `./vendor/bin/sail` when they need to run inside the container
- Use `sail up -d` (detached) to avoid blocking the terminal
- Run database commands through Sail (migrations, seeds, tinker)
- Use Sail's built-in service commands for Redis, MySQL, etc.

### MUST NOT DO

- Run `php artisan` directly on the host — use `sail artisan`
- Run `composer` directly on the host — use `sail composer`
- Modify `docker-compose.yml` directly — use `sail:publish` and edit the published files
- Use `docker exec` when Sail provides a wrapper command
