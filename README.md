# claude-plugins

Personal Claude Code plugins for Laravel, Inertia.js, and related development workflows.

> **Note:** The `react` plugin has been removed. For React/Next.js frontend work, use the [Vercel best practices skill](https://vercel.com/docs) instead, which covers React, Next.js, and deployment patterns with first-party guidance.

## Plugins

| Plugin | Description |
|--------|-------------|
| `laravel` | Controllers, models, Eloquent, actions, enums, jobs, events, policies, API resources, migrations, and testing with Pest |
| `inertia` | Inertia page props, shared data, forms, navigation, partial reloads, deferred props |
| `laravel-sail` | Sail commands, docker-compose, service configuration, troubleshooting |
| `api` | RESTful API design conventions, HTTP methods, status codes, and best practices |
| `code-review` | Code review checklists for backend and frontend |
| `git` | Git workflow: commit-push-PR and GitHub release automation |
| `monitoring` | Observability, metrics, structured logging, and alerting patterns |
| `security` | Security auditing, vulnerability scanning, and secure coding practices |
| `webhook` | Webhook implementation with Standard Webhooks conventions |
| `wordpress` | WordPress theme and plugin development, Gutenberg blocks, hooks and filters |

## Installation

### 1. Add the marketplace

From within Claude Code, run:

```
/plugin marketplace add paulund/ai
```

### 2. Install plugins

Install individual plugins as needed:

```
/plugin install laravel@paulund-ai
/plugin install inertia@paulund-ai
/plugin install laravel-sail@paulund-ai
/plugin install api@paulund-ai
/plugin install code-review@paulund-ai
/plugin install git@paulund-ai
/plugin install monitoring@paulund-ai
/plugin install security@paulund-ai
/plugin install webhook@paulund-ai
/plugin install wordpress@paulund-ai
```

Or browse and install interactively via `/plugin` → **Discover** tab.

### 3. Use skills

Skills are namespaced by plugin name:

```
/laravel:laravel
/git:commit-push-pr
/git:github-release
```

## Updating

```
/plugin marketplace update paulund-ai
```

## Local development

To test plugins without installing, use `--plugin-dir`:

```bash
claude --plugin-dir ./laravel
```
