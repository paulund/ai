# Docker Compose Networking

## Default Behaviour

Compose creates a single default network for each project. All services join it automatically and can reach each other by service name as the hostname.

```yaml
# app can connect to mysql:3306 — no extra config needed
services:
  app:
    image: myapp
  mysql:
    image: mysql:8
```

## Custom Networks

Define networks explicitly to control isolation and naming.

```yaml
services:
  app:
    networks:
      - frontend
      - backend
  mysql:
    networks:
      - backend
  nginx:
    networks:
      - frontend

networks:
  frontend:
  backend:
```

Services on different networks cannot communicate. Use this to isolate databases from public-facing services.

## Network Drivers

| Driver | Use case |
|--------|----------|
| `bridge` | Default — isolated network on a single host |
| `host` | Container shares host network stack — no isolation |
| `none` | No networking |
| `overlay` | Multi-host (Swarm) — not needed for local Compose |

```yaml
networks:
  backend:
    driver: bridge
```

## Aliases

Give a service an additional hostname on a specific network.

```yaml
services:
  mysql:
    networks:
      backend:
        aliases:
          - db
```

## External Networks

Connect to a network created outside Compose (e.g. shared across projects).

```yaml
networks:
  shared:
    external: true
    name: my-shared-network
```

## Port Exposure vs Publishing

- `expose` — makes a port available to other services on the same network only (not the host)
- `ports` — maps a container port to a host port

```yaml
services:
  mysql:
    expose:
      - "3306"       # reachable by other containers, not the host
  nginx:
    ports:
      - "80:80"      # reachable from the host
```

Avoid publishing ports for internal services (databases, caches) — use `expose` instead.
