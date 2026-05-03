---
name: dev-pre-commit
description: Use when setting up pre-commit hooks, configuring Husky, adding lint-staged, or enabling commit-time formatting, type checking, or testing. Installs and wires up Husky v9, lint-staged with Prettier, and optionally typecheck and test scripts.
category: standards
---

# Setup Pre-Commit Hooks

## Core Workflow

### 1. Detect package manager

Check for `package-lock.json` (npm), `pnpm-lock.yaml` (pnpm), `yarn.lock` (yarn), `bun.lockb` (bun). Use whichever is present. Default to npm if unclear.

### 2. Install dependencies

Install as devDependencies:

```
husky lint-staged prettier
```

### 3. Initialize Husky

```bash
npx husky init
```

This creates `.husky/` and adds `prepare: "husky"` to package.json.

### 4. Create `.husky/pre-commit`

Write this file (no shebang needed for Husky v9+):

```
npx lint-staged
npm run typecheck
npm run test
```

Adapt: replace `npm` with the detected package manager. If `typecheck` or `test` scripts are absent from package.json, omit those lines and inform the user.

### 5. Create `.lintstagedrc`

```json
{
  "*": "prettier --ignore-unknown --write"
}
```

### 6. Create `.prettierrc` (if missing)

Only create if no Prettier config exists:

```json
{
  "useTabs": false,
  "tabWidth": 2,
  "printWidth": 80,
  "singleQuote": false,
  "trailingComma": "es5",
  "semi": true,
  "arrowParens": "always"
}
```

### 7. Verify

- [ ] `.husky/pre-commit` exists and is executable
- [ ] `.lintstagedrc` exists
- [ ] `prepare` script in package.json is `"husky"`
- [ ] Prettier config exists
- [ ] Run `npx lint-staged` to confirm it works

### 8. Commit

Stage all changed/created files and commit with message: `Add pre-commit hooks (husky + lint-staged + prettier)`

This runs through the new hooks as a smoke test.

## Constraints

**MUST DO**
- Use Husky v9+ (no shebangs in hook files)
- Use `prettier --ignore-unknown` to skip unparseable files (images, etc.)
- Omit typecheck/test lines if those scripts don't exist in package.json — tell the user what was skipped
- Match the detected package manager in all hook commands

**MUST NOT DO**
- Do not add a shebang line to `.husky/pre-commit`
- Do not create `.prettierrc` if a Prettier config already exists
