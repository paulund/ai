# Severity Classification — Exposure × Impact

Findings are scored on two axes. Drop findings that are neither reachable nor significant.

## Exposure (can the buggy path actually run in production?)

| Exposure   | Examples                                                                                     |
| ---------- | -------------------------------------------------------------------------------------------- |
| **High**   | Public API endpoint, web route, server action called from client, cron job, webhook handler. |
| **Medium** | Library function with non-trivial callers; service layer code reachable from actions.        |
| **Low**    | Internal helper; test-only path; gated behind a feature flag that's never enabled.           |
| **None**   | Dead code (no callers); commented-out code; unreleased feature.                              |

## Impact (what's the consequence if the path runs?)

| Impact          | Examples                                                                                                                        |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| **Severe**      | Data loss; silent corruption; security boundary breach (auth bypass, secret leak, injection); unaddressed AC; broken CI.        |
| **Significant** | Incorrect output; missing error handling on sensitive path; breaking change without migration; missing test for implemented AC. |
| **Moderate**    | Code-quality issue; observability hole; minor performance regression; wrong but non-crashing behavior on edge case.             |
| **Minor**       | Naming; style nit; missing comment; magic number.                                                                               |

## Combining

|                      | Impact: Severe | Impact: Significant | Impact: Moderate | Impact: Minor |
| -------------------- | -------------- | ------------------- | ---------------- | ------------- |
| **Exposure: High**   | critical       | high                | medium           | low           |
| **Exposure: Medium** | high           | high                | medium           | low           |
| **Exposure: Low**    | medium         | medium              | low              | low           |
| **Exposure: None**   | low            | low                 | drop             | drop          |

## Examples

- SQL syntax error in `/api/v1/posts` route handler → critical (High exposure × Severe impact).
- Missing rate limit on a webhook endpoint → medium (High exposure × Moderate impact).
- `console.log` of an already-public value → low (any exposure × Minor impact).
- Bug in dead code → low (No exposure × whatever impact).
- Off-by-one in test fixture → drop (No exposure × whatever impact).
- Auth check removed from a public endpoint → critical (High exposure × Severe impact).
- Lint nit in unused variable → drop.

## Severity labels in the comment

Use these labels exactly: `critical`, `high`, `medium`, `low`. Do not use abbreviations or different casing. The label drives the merge workflow — humans must verify every `critical`/`high` finding is fixed.
