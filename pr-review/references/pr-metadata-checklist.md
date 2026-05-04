# PR Metadata Checklist

Use this checklist during Step 3 of the PR review workflow. For each item, mark **PASS** or **FLAG** with a brief rationale.

---

## 1. Title

**Criteria:**
- Present and not empty
- Describes *what* the change does in plain language
- Ideally hints at *why* if space permits
- Uses imperative mood (e.g., "Add user authentication", "Fix null pointer in checkout")

**Flag if:**
- Title is missing, empty, or auto-generated (e.g., "Update file.ts")
- Title is vague (e.g., "Fix bug", "Changes")
- Title describes multiple unrelated concerns

---

## 2. Description

**Criteria:**
- Present and substantive (more than one sentence)
- Explains *what* changed and *why* it was needed
- Links to the related issue (`Closes #N`, `Fixes #N`, `Relates to #N`)
- No AI-generated filler or template noise left unedited

**Flag if:**
- Description is empty or minimal
- No linked issue or ticket reference
- Description only restates the title without adding context
- Contains unremoved template placeholders

---

## 3. Single Concern

**Criteria:**
- The diff addresses one logical change or one closely related set of changes
- All modified files support the same stated purpose

**Flag if:**
- Changes span clearly unrelated domains (e.g., auth refactor + CSS fix + dependency bump in one PR)
- The diff stats (`gh pr diff <N> --stat`) show files that have no obvious connection to the title/description

---

## 4. Out-of-Scope Changes

**Criteria:**
- Every file change is justified by the PR's stated purpose
- No drive-by refactors, formatting changes, or dependency bumps unrelated to the main change

**Flag if:**
- Formatting or lint fixes appear in files unrelated to the PR
- Dependencies are bumped without explanation
- Refactors in unrelated modules are included

**Note:** If out-of-scope changes are minor and beneficial, note them as LOW severity rather than blocking.

---

## 5. Acceptance Criteria Coverage

**Criteria:**
- The PR has a linked issue with defined acceptance criteria
- Each criterion is visibly addressed in the code, tests, or PR description
- Any unaddressed criteria are explicitly called out with a reason

**Flag if:**
- No linked issue exists (flag in metadata check)
- Acceptance criteria exist but appear unaddressed in the diff
- Criteria are partially addressed without explanation

---

## Output Format

Present the metadata check as a markdown table:

```markdown
| Check | Status | Notes |
|-------|--------|-------|
| Title | PASS | Clear and descriptive |
| Description | FLAG | Missing linked issue |
| Single Concern | PASS | Focused on one auth flow |
| Out-of-Scope | FLAG | Lint fixes in unrelated files (LOW) |
| Acceptance Criteria | FLAG | Criterion 3 (error handling) not addressed |
```
