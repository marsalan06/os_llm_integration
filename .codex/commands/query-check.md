# /query-check

Follow the global rules in `.codex/README.md`.

Use:
- Agent: `query-agent`
- Skill: `query-checking`

Purpose:
- Check or optimize Firestore, BigQuery, Cloud Logs, metrics, or application data-access queries.

Steps:
1. Identify source, fields, filters, time window, and expected result shape.
2. Check correctness, indexes, ordering, limits, and cost.
3. Recommend safer or faster query shape.
4. State assumptions and verification options.
