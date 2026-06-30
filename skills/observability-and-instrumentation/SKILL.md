---
name: observability-and-instrumentation
description: Use when shipping any feature that runs in production. Use when adding logging, metrics, tracing, or alerting. Use when production issues are reported but you can't tell what happened from the available data.
---

## Core Workflow

1. **Define on-call questions first** — before adding any instrumentation, write down 2–4 questions an engineer will ask about this feature when it breaks:
   ```
   FEATURE: checkout payment retry
   QUESTIONS ON-CALL WILL ASK:
   1. What fraction of payments succeed on first attempt vs after retry?
   2. When a payment fails permanently, why? (provider error? timeout? validation?)
   3. Is the payment provider slower than usual?
   → Every signal below must help answer one of these.
   ```
   If you can't name the questions, you're not ready to instrument — you'll log everything and learn nothing.

2. **Pick the right signal for each question:**

   | Signal | Answers | Example |
   |---|---|---|
   | Structured log | "What happened in this specific case?" | `payment_failed` with provider error code + attempt count |
   | Metric | "How often / how fast in aggregate?" | p99 latency histogram of provider calls |
   | Trace | "Where did time go across services?" | One slow checkout broken down by hop |

3. **Structured logs** — log events not prose. Every log line is a JSON object with a stable event name and machine-readable fields. Attach a `requestId` to every line. Use consistent log levels: `error` (someone may need to act), `warn` (degraded but handled), `info` (significant business event), `debug` (off in production by default). Never log secrets, tokens, passwords, or full PII.

4. **RED metrics on every endpoint and external dependency** — Rate (requests/sec), Errors (failure rate), Duration (latency histogram, p95/p99 — never averages). Labels must come from small fixed sets; never use user IDs, raw URLs, or error message text as labels (cardinality bomb).

5. **Alert on symptoms users feel, not infrastructure causes:**
   - Page-worthy: error rate > 1% for 5 min, p99 latency > 2s, queue age > 10 min
   - Not page-worthy: CPU at 85%, one pod restarted, disk at 70%
   Every alert must be actionable and link to a runbook.

6. **Verify the telemetry before shipping** — trigger the path in staging, find the log line by `requestId`, confirm metric series appear with correct labels, follow one trace end-to-end without broken spans.

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "I'll add logging after it works" | "After" becomes "after the first incident" — the most expensive moment to discover you're blind. |
| "More logs = more observability" | Unstructured noise makes incidents slower. Three queryable events beat three hundred prose lines. |
| "`console.log` is fine for now" | Unstructured output can't be filtered, correlated, or alerted on. |
| "Alert on everything important, we'll tune later" | A noisy pager trains people to ignore it. The tuning never happens; the missed real alert does. |

## Red Flags

- [ ] A feature PR with retries, queues, or external calls and zero new telemetry
- [ ] Log lines built by string interpolation instead of structured fields
- [ ] No correlation/request ID — each log line is an orphan
- [ ] Metrics labelled with user IDs, raw URLs, or error message text (cardinality bomb)
- [ ] Latency tracked as an average with no percentiles
- [ ] Alerts on causes (CPU, memory) while user-facing error rate is unmonitored
- [ ] Secrets, tokens, or PII appearing in logs

## Verification

- [ ] On-call questions for this feature are written down; each signal maps to one of them
- [ ] All log output is structured (JSON) with a stable event name and correlation ID on every line
- [ ] No secrets, tokens, or PII in any log line (spot-checked against actual output)
- [ ] RED metrics exist for every new endpoint and external dependency
- [ ] Latency is a histogram; p95/p99 are queryable
- [ ] An induced failure in staging was located via telemetry alone, without reading source

## Constraints

### MUST DO
- Write down on-call questions before adding any instrumentation.
- Log events with stable names and structured fields.
- Attach a `requestId`/`correlationId` to every log line.
- Use histograms for latency; read p95/p99 — never averages.
- Alert on symptoms users feel, not on infrastructure causes.

### MUST NOT DO
- Log secrets, tokens, passwords, or full PII.
- Use string interpolation for log messages.
- Add metrics with unbounded label values (user IDs, raw URLs, error messages).
- Leave `console.log` calls in production code paths.
