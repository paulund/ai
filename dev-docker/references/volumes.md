# Docker Compose Volumes

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
