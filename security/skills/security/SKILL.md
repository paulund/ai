---
name: security
description: Use when conducting security audits, reviewing code for vulnerabilities, running SAST scans, analyzing infrastructure security, or performing penetration testing on web applications.
---

## Core Workflow

1. **Scope** — Identify the application stack, entry points, trust boundaries, data flows, and attack surface.
2. **Automated Scan** — Run SAST tools, dependency checks, and secret scanning before manual review.
3. **Manual Review** — Systematically inspect each security domain: authentication, authorization, input handling, crypto, data exposure.
4. **Active Testing** — Validate findings and test for exploitability (authorized targets only).
5. **Classify** — Rate each finding using consistent severity levels: Critical / High / Medium / Low / Informational.
6. **Report** — Write a structured markdown report with executive summary, detailed findings (description, location, severity, impact, remediation), summary table, and positive findings.
7. **Save** — Write the report to `plans/security/security-audit-{YYYY-MM-DD}.md`.

## Reference Guide

| Topic | Reference | Load When |
|-------|-----------|-----------|
| Audit Checklist | `references/audit-checklist.md` | Always — primary audit guide covering all security domains |
| Laravel Checks | `references/laravel-checks.md` | Auditing Laravel applications |
| Frontend Checks | `references/frontend-checks.md` | Auditing React, TypeScript, or Inertia.js frontends |
| Vulnerability Patterns | `references/vulnerability-patterns.md` | Manual code review for injection, XSS, IDOR, and other common flaws |
| SAST Tools | `references/sast-tools.md` | Running automated static analysis scans |
| Secret Scanning | `references/secret-scanning.md` | Scanning for hardcoded secrets, API keys, or credentials |
| Penetration Testing | `references/penetration-testing.md` | Active testing, reconnaissance, or exploitation validation |
| Infrastructure Security | `references/infrastructure-security.md` | Reviewing DevSecOps pipelines, cloud security, containers, or compliance |
| Report Template | `references/report-template.md` | Writing the final security report |

## Constraints

### MUST DO

- Provide exact file paths and line numbers for every finding.
- Give a concrete, actionable remediation recommendation for each finding.
- Include positive findings — document what the application does correctly.
- Cover all domains in the audit checklist, even if no issues are found.
- Check authentication and authorization first.
- Run automated tools before manual review.
- Check for secrets in code and git history.
- Rate severity consistently using CVSS-aligned definitions (Critical: 9.0-10.0, High: 7.0-8.9, Medium: 4.0-6.9, Low: 0.1-3.9).
- Verify scope and authorization before any active testing.
- Document all testing activities.
- Report critical findings immediately.
- Save the final report to `plans/security/security-audit-{YYYY-MM-DD}.md`.

### MUST NOT DO

- Modify application source files — audit only, no fixes.
- Invent findings without evidence from the actual code.
- Skip any audit domain without explicitly noting it was reviewed and found clean.
- Skip manual review — automated tools miss things.
- Test on production systems without authorization.
- Ignore low-severity issues.
- Assume frameworks handle all security automatically.
- Exploit beyond proof of concept.
- Cause service disruption or data loss.
- Test outside the defined scope.
- Share detailed exploits publicly.
