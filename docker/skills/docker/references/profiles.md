# Docker Compose Profiles

Profiles let you define optional services that only start when explicitly requested. Use them for dev tools (Mailpit, Adminer, phpMyAdmin) that should not run by default.

## Defining a Profile

```yaml
services:
  app:
    image: myapp          # no profile — always starts

  mailpit:
    image: axllent/mailpit
    profiles: [tools]
    ports:
      - "8025:8025"       # web UI
      - "1025:1025"       # SMTP

  adminer:
    image: adminer
    profiles: [tools]
    ports:
      - "8080:8080"
```

## Starting Services with a Profile

```bash
docker compose up -d                          # starts only app
docker compose --profile tools up -d         # starts app + mailpit + adminer
COMPOSE_PROFILES=tools docker compose up -d  # same, via env var
```

## Multiple Profiles per Service

A service can belong to multiple profiles.

```yaml
services:
  adminer:
    image: adminer
    profiles: [tools, debug]
```

## Profile Naming Conventions

| Profile | Services |
|---------|----------|
| `tools` | Adminer, Mailpit, phpMyAdmin |
| `debug` | Xdebug, profilers |
| `queue` | Horizon, queue workers (if not always needed) |
| `search` | Elasticsearch, Meilisearch (resource-heavy) |

## depends_on with Profiles

If a profiled service depends on another, the dependency must be in the same profile or always active.

```yaml
services:
  mysql:
    image: mysql:8                 # always starts

  adminer:
    image: adminer
    profiles: [tools]
    depends_on:
      mysql:
        condition: service_healthy  # mysql is always up — this works
```

A non-profiled service cannot depend on a profiled service.
