---
name: dev-security-review
description: Use when reviewing code that handles auth, payments, user input, or sensitive data. Trigger phrases - "/dev-security-review", "security check".
category: dev
---

## Core Workflow

1. **Read the PR diff** — load all changed files and understand the scope
2. **Research repository context** — identify existing security frameworks, sanitization patterns, and the project's threat model
3. **Comparative analysis** — compare new changes against existing secure patterns; flag deviations and new attack surfaces
4. **Vulnerability assessment** — examine each modified file for security implications, tracing data flow from user inputs to sensitive operations
5. **Filter false positives** — apply the exclusion list below; only report findings with confidence ≥ 0.8
6. **Output markdown report** — file, line number, severity, category, description, exploit scenario, and fix recommendation

## Security Categories to Examine

- **Input Validation** — SQL injection, command injection, XXE, template injection, NoSQL injection, path traversal
- **Authentication & Authorization** — auth bypass, privilege escalation, session flaws, JWT vulnerabilities, authorization bypasses
- **Crypto & Secrets** — hardcoded keys, weak algorithms, improper key storage, randomness issues, certificate validation bypasses
- **Injection & Code Execution** — RCE via deserialization, pickle injection, YAML deserialization, eval injection, XSS
- **Data Exposure** — sensitive data logging, PII violations, API endpoint leakage, debug information exposure

## Severity Guidelines

- **HIGH** — Directly exploitable: RCE, data breach, authentication bypass
- **MEDIUM** — Requires specific conditions but with significant impact
- **LOW** — Defense-in-depth or lower-impact issues (report sparingly)

## Constraints

### MUST DO
- Minimize false positives: only flag issues where you are >80% confident of actual exploitability
- Focus on impact: prioritize unauthorized access, data breaches, or system compromise
- Report in markdown with: file, line, severity, category, description, exploit scenario, and fix recommendation

### MUST NOT DO
- Do NOT flag Denial of Service (DoS) vulnerabilities
- Do NOT flag secrets stored on disk if handled by other processes
- Do NOT flag rate limiting or resource exhaustion issues
- Do NOT flag memory safety issues in memory-safe languages (e.g., Rust)
- Do NOT flag XSS in React or Angular unless using `dangerouslySetInnerHTML` or similar bypasses
- Do NOT flag regex injection or regex DoS
- Do NOT flag missing audit logs
- Do NOT flag issues in documentation or unit-test-only files
- Do NOT flag vulnerabilities requiring control of environment variables or CLI flags
- Do NOT run commands to reproduce vulnerabilities; read code only
- Do NOT use the bash tool or write to any files
