# Docker Compose Volumes

## Types

| Type | Syntax | Use case |
|------|--------|----------|
| Named volume | `mydata:/var/lib/mysql` | Persist data across container restarts |
| Bind mount | `./src:/var/www/html` | Sync local files into container (dev only) |
| Anonymous volume | `/var/lib/mysql` | Temporary — lost on container removal |
| tmpfs | `type: tmpfs` | In-memory only — lost on stop |

## Named Volumes

Managed by Docker. Data persists when containers are removed and recreated.

```yaml
services:
  mysql:
    volumes:
      - mysql_data:/var/lib/mysql

volumes:
  mysql_data:
```

Always declare named volumes at the top-level `volumes:` key.

## Bind Mounts

Maps a host path into the container. Changes on either side are immediately visible.

```yaml
services:
  app:
    volumes:
      - ./src:/var/www/html          # relative to compose file
      - /absolute/host/path:/data    # absolute path
```

Use bind mounts for local development only. Do not use them in production — they tie the container to a specific host path.

## Read-Only Mounts

```yaml
volumes:
  - ./config:/etc/myapp/config:ro
```

## Sharing Volumes Between Services

```yaml
services:
  app:
    volumes:
      - shared_storage:/data
  worker:
    volumes:
      - shared_storage:/data

volumes:
  shared_storage:
```

## tmpfs (In-Memory)

Useful for test databases or ephemeral scratch space.

```yaml
services:
  app:
    tmpfs:
      - /tmp
      - /run
```

Or with full options:

```yaml
services:
  app:
    volumes:
      - type: tmpfs
        target: /tmp
        tmpfs:
          size: 100m
```

## External Volumes

Use a volume created outside Compose (e.g. pre-provisioned in production).

```yaml
volumes:
  production_data:
    external: true
    name: prod-mysql-data
```

## Removing Volumes

```bash
docker compose down -v        # remove containers and their volumes
docker volume rm <name>       # remove a specific named volume
docker volume prune           # remove all unused volumes
```
