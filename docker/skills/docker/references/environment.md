# Docker Compose Environment Variables

## .env File (Compose-level)

Compose automatically loads `.env` from the same directory as the compose file. Values are available for variable substitution in the compose file itself — they are NOT automatically passed into containers.

```bash
# .env
DB_PASSWORD=secret
APP_PORT=8080
```

```yaml
# docker-compose.yml — substitution in the file
services:
  app:
    ports:
      - "${APP_PORT}:80"
```

## Passing Variables into Containers

### Inline

```yaml
services:
  app:
    environment:
      APP_ENV: production
      DB_HOST: mysql
```

### From the host environment

```yaml
services:
  app:
    environment:
      - DB_PASSWORD      # no value — taken from host shell
```

### From an env file

```yaml
services:
  app:
    env_file:
      - .env.app         # passed into the container
      - .env.secrets
```

`env_file` passes variables into the container. This is different from Compose's `.env` which is only for compose-file substitution.

## Variable Substitution

```yaml
services:
  app:
    image: myapp:${APP_VERSION:-latest}   # default to "latest" if unset
    ports:
      - "${APP_PORT:?APP_PORT must be set}:80"  # error if unset
```

| Syntax | Behaviour |
|--------|-----------|
| `${VAR}` | Value of VAR, empty string if unset |
| `${VAR:-default}` | Value of VAR, or "default" if unset/empty |
| `${VAR:?message}` | Value of VAR, or error with message if unset/empty |

## Secrets (Production)

For sensitive values in production, use Docker secrets instead of environment variables.

```yaml
services:
  app:
    secrets:
      - db_password
    environment:
      DB_PASSWORD_FILE: /run/secrets/db_password

secrets:
  db_password:
    file: ./secrets/db_password.txt   # dev
    # external: true                  # production — pre-created secret
```

The app reads the value from the file path, not directly from the environment.

## Constraints

- Never commit `.env` files containing real secrets — add to `.gitignore`
- Use `.env.example` with placeholder values as a template committed to the repo
- Prefer `env_file` over inline `environment` for secrets to keep the compose file clean
- Use `${VAR:?message}` for required variables so Compose fails fast with a clear error
