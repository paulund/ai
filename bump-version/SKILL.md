---
name: bump-version
description: Use when you want to version-bump changed plugins and cut a GitHub release for the paulund/ai plugin marketplace. Triggers on /bump-version, "bump versions", "cut a release", "release the plugins", or any request to publish plugin updates.
---

# Bump Version

## Core Workflow

### 1. Find the baseline

If there are no commits since the last tag, stop and tell the user there is nothing to release.

```bash
git describe --tags --abbrev=0 2>/dev/null || git rev-list --max-parents=0 HEAD
```

This gives you either the last release tag or the initial commit — whichever is the reference point for "what changed".

### 2. Identify changed plugin directories

```bash
git diff --name-only <baseline>..HEAD
```

Extract the unique top-level directory names from the results (e.g. `docker/skills/...` → `docker`). Ignore non-plugin paths like `.claude-plugin/`, `CLAUDE.md`, `README.md`.

### 3. Classify each changed plugin

For each changed plugin directory, determine whether it existed at the baseline:

```bash
git show <baseline>:<plugin>/.claude-plugin/plugin.json 2>/dev/null
```

- **If the file didn't exist at baseline** → the plugin is new. Leave its version at `1.0.0`.
- **If the file existed at baseline** → the plugin was updated. Read the current version from `<plugin>/.claude-plugin/plugin.json` and bump it:
  - New skills or reference files added → **minor** bump (1.0.0 → 1.1.0)
  - Bug fixes or small edits to existing skills → **patch** bump (1.0.0 → 1.0.1)
  - Use the commit messages for the plugin's changed files to decide which applies.

### 4. Show a summary and confirm

Before writing anything, present the proposed changes:

```
Plugin version bumps:
  docker   1.0.0 → 1.1.0  (minor: new reference files added)
  git      1.0.0 → 1.0.1  (patch: updated existing skill)
  marketing 1.0.0          (new plugin, no bump)

Proceed? [y/n]
```

Wait for confirmation before continuing.

### 5. Apply the bumps

For each plugin being bumped, update **both**:
- `<plugin>/.claude-plugin/plugin.json` — the `version` field
- `.claude-plugin/marketplace.json` — the matching entry's `version` field

### 6. Commit the version changes

Stage only the modified JSON files and commit:

```bash
git add .claude-plugin/marketplace.json <plugin1>/.claude-plugin/plugin.json ...
git commit -m "chore: bump plugin versions for release"
```

### 7. Cut the release

Invoke `/git:github-release` to tag and publish the GitHub release. Use the highest version bumped across all plugins as the release tag (e.g. if docker went to 1.1.0 and git to 1.0.1, tag the release `v1.1.0`). Include a changelog in the release notes listing which plugins changed and what their new versions are.

---

## Edge cases

- **Nothing changed since last tag** -- stop immediately and tell the user
- **Multiple plugins changed** -- bump and release all at once; the release tag is the highest version across all plugins
- **Plugin exists but has no `plugin.json`** -- skip with a warning; do not attempt to bump

## Constraints

- **MUST** confirm with the user before writing any version changes.
- **MUST NOT** bump versions for newly added plugins (ones that didn't exist at the last tag).
- **MUST** keep `plugin.json` and `marketplace.json` versions in sync for every plugin.
- Use minor bumps for new skills/references, patch bumps for edits to existing content.
