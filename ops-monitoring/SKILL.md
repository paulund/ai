---
name: ops-monitoring
description: Review monitoring data and logs for critical issues or anomalies. Use during on-call, post-deployment, or scheduled health checks. Trigger phrases - "/ops-monitoring", "check monitoring", "review logs".
category: workflow
---

## Core Workflow

1. **Gather monitoring data** — logs, metrics, alerts, dashboards
2. **Identify anomalies** — errors spikes, latency increases, resource exhaustion
3. **Triage severity** — critical (user-facing) vs warning vs info
4. **Create issues** — document findings as GitHub issues for tracking
5. **Escalate if needed** — flag critical issues immediately

## Monitoring Checklist

- [ ] Error rate within normal bounds
- [ ] Response times acceptable (p50, p95, p99)
- [ ] Infrastructure resources healthy (CPU, memory, disk)
- [ ] Database connections and query performance normal
- [ ] Queue depths and job processing rates healthy
- [ ] External dependencies responding normally
- [ ] Security events reviewed (failed logins, unusual traffic)
- [ ] Cost metrics within budget

## Output Format

1. **Health summary** — green / yellow / red per system
2. **Anomalies found** — what, when, severity, impact
3. **GitHub issues created** — links to new issues labeled `bug` and `needs-triage`
4. **Recommendations** — preventive actions or deeper investigation needed
