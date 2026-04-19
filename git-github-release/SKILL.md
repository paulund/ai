---
name: github-release
description: Use when the user wants to publish a new GitHub release, cut a version tag, or ship a new version using semver.
---

## Core Workflow

1. Gather context by running in parallel: `git fetch --tags`, latest five tags via `git tag --sort=-v:refname | head -5`, and commits since the last tag via `git log <last-tag>..HEAD --oneline`. If no tags exist, treat the base version as `v0.0.0` and show all commits.
2. Determine the next semver version by inspecting commits since the last tag:
   - Any `feat:` commit bumps minor.
   - Only `fix:`, `perf:`, `chore:`, etc. bumps patch.
   - A breaking change (`BREAKING CHANGE` in body or `!` suffix) bumps major.
   - Present the proposed version to the user and ask for confirmation before proceeding.
3. Write customer-facing release notes:
   - Audience is end-users, not engineers. Use clear, friendly, benefit-focused language.
   - Structure: brief summary sentence, then optional `## What's new`, `## Bug fixes`, and `## Improvements` sections (omit any that are empty).
   - Skip dependency bumps, CI changes, and internal tooling commits.
   - Show the draft notes to the user and ask for approval before creating the release.
4. Create the release once version and notes are approved:
   ```bash
   git checkout main && git pull origin main
   gh release create <version> \
     --title "<version>" \
     --notes "<approved release notes>" \
     --target main
   ```
5. Print the URL of the published release so the user can verify it.

## Constraints

### MUST DO
- Always tag off `main`.
- Get explicit user approval for both the version number and the release notes before creating the release.
- Use semver for all version tags.

### MUST NOT DO
- Release from a feature branch.
- Create the release without user approval of version and notes.
- Include internal IDs, PR numbers, branch names, or technical implementation details in release notes.
- Use `--draft` or `--prerelease` unless the user explicitly requests it.
