# Docker Compose Override Files

Compose merges multiple files in order. Use this to separate base config from environment-specific overrides without duplicating the full compose file.

## How Merging Works

```bash
docker compose up                              # merges docker-compose.yml + docker-compose.override.yml automatically
docker compose -f docker-compose.yml -f docker-compose.prod.yml up   # explicit files
```

Compose deep-merges the files: keys in later files override earlier ones, arrays (like `ports`, `volumes`, `environment`) are appended.

## Recommended File Structure

```
docker-compose.yml            # base — service definitions, images, networks, volumes
docker-compose.override.yml   # local dev — bind mounts, debug ports, hot reload (gitignored)
docker-compose.ci.yml         # CI — no ports, no volumes, fast startup
docker-compose.prod.yml       # production — resource limits, restart policies, secrets
```

## Base File (docker-compose.yml)

Define only what is common across all environments.

```yaml
services:
  app:
    image: myapp:${APP_VERSION:-latest}
    environment:
      - APP_ENV
      - DB_HOST=mysql
    depends_on:
      mysql:
        condition: service_healthy

  mysql:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: ${DB_ROOT_PASSWORD}
      MYSQL_DATABASE: ${DB_DATABASE}
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 10s
      retries: 5
      start_period: 30s

volumes:
  mysql_data:
```

## Local Dev Override (docker-compose.override.yml)

```yaml
services:
  app:
    build:
      context: .
      target: dev
    volumes:
      - ./src:/var/www/html          # bind mount for hot reload
    ports:
      - "8000:80"
    environment:
      APP_ENV: local
      XDEBUG_MODE: debug

  mysql:
    volumes:
      - mysql_data:/var/lib/mysql
    ports:
      - "3306:3306"                  # expose to host for local DB clients

  mailpit:
    image: axllent/mailpit
    ports:
      - "8025:8025"
      - "1025:1025"
```

## CI Override (docker-compose.ci.yml)

```yaml
services:
  app:
    build:
      context: .
      target: test
    environment:
      APP_ENV: testing
      DB_HOST: mysql

  mysql:
    tmpfs:
      - /var/lib/mysql               # in-memory — faster, no persistence needed
```

```bash
# In CI pipeline
docker compose -f docker-compose.yml -f docker-compose.ci.yml up -d
```

## Production Override (docker-compose.prod.yml)

```yaml
services:
  app:
    restart: unless-stopped
    deploy:
      resources:
        limits:
          cpus: "1.0"
          memory: 512m
    logging:
      driver: json-file
      options:
        max-size: "10m"
        max-file: "3"

  mysql:
    restart: unless-stopped
    volumes:
      - mysql_data:/var/lib/mysql
```

## .gitignore

```
docker-compose.override.yml    # local dev overrides are personal — don't commit
.env                           # secrets — don't commit
```

Commit `docker-compose.override.yml.example` as a template instead.
