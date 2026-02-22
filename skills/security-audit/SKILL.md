---
name: security-audit
description: Use when running a comprehensive security audit on a web application to produce a structured report with severity-ranked findings and remediation guidance.
---

# Security Auditor

Expert application security engineer conducting thorough, evidence-based security audits of web applications.

## Core Workflow

1. **Scope** — Identify the application stack, entry points, trust boundaries, and data flows.
2. **Audit** — Systematically inspect each security domain (see Reference Guide).
3. **Classify** — Rate each finding: Critical / High / Medium / Low / Informational.
4. **Report** — Write a structured markdown report with: description, location, severity, and recommendation.
5. **Summarise** — Include a summary table and positive findings section.
6. **Save** — Write the report to `plans/security/security-audit-{date}.md`.

## Reference Guide

| Topic | Reference | Load When |
|-------|-----------|-----------|
| Audit Checklist | `references/audit-checklist.md` | Always — primary audit guide |
| Laravel-Specific Checks | `references/laravel-checks.md` | When auditing Laravel applications |
| React/Frontend Checks | `references/frontend-checks.md` | When auditing React/Inertia frontends |

## Report Structure

Every audit report must follow this structure:

```markdown
# Security Audit Report — {AppName}

**Audit Date:** {date}
**Branch:** {branch}
**Auditor:** Claude Code (automated analysis)

## Executive Summary
[2-4 sentence overview: overall posture, highest risks, key strengths]

## Findings
[One H3 section per finding, in severity order]

### N. {Title}
**Severity: {Critical|High|Medium|Low|Informational}**
**Location:** `file:line`

**Description:** ...

**Recommendation:** ...

## Summary Table
| # | Title | Severity | Location |

## Positive Findings
[Bullet list of things that are done correctly]
```

## Severity Definitions

| Severity | Definition |
|----------|-----------|
| **Critical** | Remotely exploitable with no auth; direct data breach or RCE |
| **High** | Exploitable with low privilege; significant data/account compromise |
| **Medium** | Requires specific conditions; meaningful security impact |
| **Low** | Minor hardening gap; limited exploitability or impact |
| **Informational** | Best practice deviation; no direct exploitability |

## Constraints

### MUST DO

- Provide exact file paths and line numbers for every finding.
- Give a concrete, actionable recommendation for each finding.
- Include positive findings — what the application does correctly.
- Save the final report to `plans/security/security-audit-{YYYY-MM-DD}.md`.
- Cover all domains in the audit checklist, even if no issues are found.

### MUST NOT DO

- Modify application source files — audit only, no fixes.
- Invent findings without evidence from the actual code.
- Skip any audit domain without explicitly noting it was reviewed and found clean.
- Report findings that rely on hypothetical future code changes (unless current code makes them trivially easy to introduce).
