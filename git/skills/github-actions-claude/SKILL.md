---
name: github-actions-claude
description: |
  Create GitHub Actions workflows that run Claude Code skills or prompts on a schedule or trigger. Use this skill when the user wants to automate a Claude Code task in CI, migrate a scheduled Claude task to GitHub Actions, or create a new workflow that uses claude-code-action.
allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
  - Grep
  - Bash
---

# GitHub Actions + Claude Code Skill

Build GitHub Actions workflows that run Claude Code skills or prompts automatically.

## Key concepts

**Claude Code scheduled tasks vs GitHub Actions**
- Claude Code scheduled tasks (CronCreate) run locally and require Claude Code to be running on your machine
- GitHub Actions workflows run on GitHub's servers — no local machine needed
- To run a Claude skill in CI, use `anthropics/claude-code-action@v1` inside a workflow

**Local skills are available automatically**
- Any skill in `.claude/skills/` is picked up by Claude Code when it runs in the checked-out repo
- No marketplace config needed for local skills
- Marketplace plugins are only needed for external plugins not in the repo

**`gh` CLI authentication**
- `GITHUB_TOKEN` is automatically injected into every GitHub Actions environment
- The `gh` CLI picks it up natively — never pass it explicitly as `GH_TOKEN` unless you need elevated permissions beyond the default token

## Authentication

Use `claude_code_oauth_token` (existing Claude subscription) instead of `anthropic_api_key`:

```yaml
claude_code_oauth_token: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}
```

The `CLAUDE_CODE_OAUTH_TOKEN` secret must be added to the GitHub repo settings. The token value comes from the user's local Claude Code config.

## Workflow structure

### Minimal workflow (no branch/PR)

```yaml
name: My Claude Task

on:
  schedule:
    - cron: '0 14 * * 5'  # Friday 2pm GMT
  workflow_dispatch:

jobs:
  run-claude:
    runs-on: ubuntu-latest
    permissions:
      contents: write
      pull-requests: write

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Run Claude
        uses: anthropics/claude-code-action@v1
        with:
          prompt: "Your prompt or skill invocation here"
          claude_code_oauth_token: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}
```

### Full workflow (branch + Claude does work + PR with reviewer)

Use this pattern when Claude should write files and you want the workflow to manage git and the PR:

```yaml
name: My Claude Task

on:
  schedule:
    - cron: '0 14 * * 5'
  workflow_dispatch:

jobs:
  run-claude:
    runs-on: ubuntu-latest
    permissions:
      contents: write
      pull-requests: write

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Configure git
        run: |
          git config --global user.name "github-actions[bot]"
          git config --global user.email "github-actions[bot]@users.noreply.github.com"

      - name: Create branch
        id: create-branch
        run: |
          BRANCH="task/$(date +%Y-%m-%d)"
          git checkout -b "$BRANCH"
          git push -u origin "$BRANCH"
          echo "branch=$BRANCH" >> "$GITHUB_OUTPUT"

      - name: Run Claude
        uses: anthropics/claude-code-action@v1
        with:
          prompt: "Your prompt here. Write files only — do not create a git branch or open a PR, the workflow will handle that."
          claude_code_oauth_token: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}

      - name: Create pull request
        env:
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          BRANCH: ${{ steps.create-branch.outputs.branch }}
        run: |
          git add <path-to-files>
          git diff --cached --quiet && echo "No changes generated." && exit 0
          git commit -m "feat: describe what was generated"
          git push
          gh pr create \
            --title "PR title: $(date +%Y-%m-%d)" \
            --body "Auto-generated. Review before merging." \
            --base main \
            --head "$BRANCH" \
            --reviewer $REVIEWER_NAME
```

**Why this split?** The workflow owns the branch and PR lifecycle. The prompt tells Claude to write files only. This gives reliable reviewer assignment and clean commit messages without depending on Claude to run git commands correctly.

## Plugins

Only add marketplace plugins when the prompt or skill references an external plugin:

```yaml
- name: Run Claude
  uses: anthropics/claude-code-action@v1
  with:
    plugin_marketplaces: 'https://github.com/paulund/ai.git'
    plugins: 'plugin@marketplace'
    prompt: "..."
    claude_code_oauth_token: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}
```

For multiple marketplaces or plugins, use YAML block style:

```yaml
plugin_marketplaces: |
  https://github.com/paulund/ai.git
  https://github.com/anthropics/claude-code.git
plugins: |
  writing@paulund-ai
  code-review@claude-code-plugins
```

## Cron schedule reference

Always add `workflow_dispatch:` alongside `schedule:` so the workflow can be triggered manually for testing.

## Step-by-step: creating a new workflow

1. **Identify the task** — what skill or prompt should run, and when
2. **Does it write files and need a PR?** — use the full branch+PR pattern; otherwise use the minimal pattern
3. **Does it use external plugins?** — add `plugin_marketplaces` and `plugins` only if needed
4. **Set the cron** — use the reference above; always include `workflow_dispatch`
5. **Tell Claude what NOT to do** — if the workflow manages git/PR, instruct Claude in the prompt to write files only
6. **Add reviewer** — always add `--reviewer $REVIEWER_NAME$` to `gh pr create`
7. **Required secret** — `CLAUDE_CODE_OAUTH_TOKEN` must exist in the repo's GitHub secrets
