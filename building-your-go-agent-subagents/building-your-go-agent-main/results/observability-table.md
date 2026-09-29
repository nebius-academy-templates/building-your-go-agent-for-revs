# Observability table
Fill from real traces. Never estimate tokens or invent trace URLs. Record tool/plugin versions
and how totals were computed. Sum LLM leaf usage once; do not sum parent aggregates again.
If usage is unavailable, write N/A with a reason. Most expensive span means highest measured
cost, or highest token usage if cost is unavailable; state the metric.

| Input | Trace URL / ID | Total tokens | Most expensive span | security-reviewer called? |
| --- | --- | --- | --- | --- |
| PR-01 | [PLACEHOLDER] | | | |
| PR-02 | [PLACEHOLDER] | | | |
| PR-03 | [PLACEHOLDER] | | | |
| PR-04 | [PLACEHOLDER] | | | |
| PR-05 | [PLACEHOLDER] | | | |

## PR-03 diagnostic
- Was security-reviewer called? [PLACEHOLDER]
- Its output (redact credentials): [PLACEHOLDER]
- Did the final review preserve the security finding? [PLACEHOLDER]
- Supported failure mode or no failure, with trace evidence: [PLACEHOLDER]
