---
name: dev-wordpress
description: Use when developing WordPress themes, plugins, customizing Gutenberg blocks, implementing WooCommerce features, or optimizing WordPress performance and security.
---

## Core Workflow

1. **Analyze requirements** - Understand WordPress context, existing setup, goals
2. **Design architecture** - Plan theme/plugin structure, hooks, data flow
3. **Implement** - Build using WordPress standards, security best practices
4. **Optimize** - Cache, query optimization, asset optimization
5. **Test & secure** - Security audit, performance testing, compatibility checks

## Reference Guide

| Topic | Reference | Load When |
|-------|-----------|-----------|
| Theme Development | `references/theme-development.md` | Templates, hierarchy, child themes, FSE |
| Plugin Architecture | `references/plugin-architecture.md` | Structure, activation, settings API, updates |
| Gutenberg Blocks | `references/gutenberg-blocks.md` | Block dev, patterns, FSE, dynamic blocks |
| Hooks & Filters | `references/hooks-filters.md` | Actions, filters, custom hooks, priorities |
| Performance & Security | `references/performance-security.md` | Caching, optimization, hardening, backups |

## Constraints

### MUST DO
- Follow WordPress Coding Standards (WPCS)
- Use nonces for form submissions
- Sanitize all user inputs with appropriate functions
- Escape all outputs (esc_html, esc_url, esc_attr)
- Use prepared statements for database queries
- Implement proper capability checks
- Enqueue scripts/styles properly (wp_enqueue_*)
- Use WordPress hooks instead of modifying core
- Write translatable strings with text domains
- Test across multiple WordPress versions

### MUST NOT DO
- Modify WordPress core files
- Use PHP short tags or deprecated functions
- Trust user input without sanitization
- Output data without escaping
- Hardcode database table names (use $wpdb->prefix)
- Skip capability checks in admin functions
- Ignore SQL injection vulnerabilities
- Bundle unnecessary libraries (use WordPress APIs)
- Create security vulnerabilities through file uploads
- Skip internationalization (i18n)
