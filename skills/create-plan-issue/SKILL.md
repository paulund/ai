# /create-plan-issue

Creates a GitHub issue that records a planned feature as a trackable work item.

## Prerequisites

A plan file must exist at `plans/<feature>/plan.md`. This is created by Plan Mode.

## Usage

```
/create-plan-issue
/create-plan-issue <feature-name>
```

## Steps

### 1. Find the Plan File

If a feature name is provided: use `plans/<feature-name>/plan.md`.

If no feature name, find the most recently modified:

```bash
ls -t plans/*/plan.md | head -1
```

Read the full plan file content. Extract:
- **Feature name**: the value after `# Feature:` on the first heading line

If multiple recently modified plans exist, confirm with the user which one to use.

### 2. Create the GitHub Issue

Use `--body-file` to avoid shell escaping issues with the plan's markdown content.
Write the plan file path directly as the body source:

```bash
gh issue create \
  --title "feat: <Feature Name>" \
  --label "enhancement" \
  --body-file "plans/<feature-directory>/plan.md"
```

### 3. Confirm to the User

After the issue is created, output:
- The issue URL (printed by `gh issue create`)
- A reminder that they can assign the issue to Claude later with `@claude` in a comment, or implement locally with `/implement`
