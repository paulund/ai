# Docker Compose Healthchecks

A container starting does not mean its service is ready. Healthchecks let Docker track actual service readiness so dependent containers wait for the right signal.

## Configuration Fields

```yaml
healthcheck:
  test: ["CMD", "command", "arg"]   # CMD or CMD-SHELL
  interval: 10s
  timeout: 5s
  retries: 5
  start_period: 30s
```

| Field | Purpose |
|-------|---------|
| `test` | Command to run — exit 0 = healthy, non-zero = unhealthy |
| `interval` | How often to run the check |
| `timeout` | Max time allowed for the check to complete |
| `retries` | Consecutive failures before marking unhealthy |
| `start_period` | Grace period after startup — failures don't count toward retries |

Use `CMD` array syntax by default. Use `CMD-SHELL` only when environment variable expansion is needed — prefix `$` with `$$` in Compose files.

## Service Examples

**MySQL**
```yaml
healthcheck:
  test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
  interval: 10s
  timeout: 5s
  retries: 5
  start_period: 30s
```

**Redis**
```yaml
healthcheck:
  test: ["CMD", "redis-cli", "ping"]
  interval: 10s
  timeout: 5s
  retries: 5
```

**PostgreSQL**
```yaml
healthcheck:
  test: ["CMD-SHELL", "pg_isready -U $$POSTGRES_USER -d $$POSTGRES_DB"]
  interval: 10s
  timeout: 5s
  retries: 5
  start_period: 30s
```

## depends_on Conditions

```yaml
depends_on:
  mysql:
    condition: service_healthy
  migrations:
    condition: service_completed_successfully
  redis:
    condition: service_started
```

| Condition | When to use |
|-----------|-------------|
| `service_started` | Container exists and starts quickly, no readiness check needed |
| `service_healthy` | Service must pass its healthcheck before dependents start |
| `service_completed_successfully` | One-off jobs like migrations or seed scripts must exit 0 |

## Debugging

```bash
# View health status in STATUS column
docker ps

# Full healthcheck history and last output
docker inspect <container> | jq '.[0].State.Health'
```
