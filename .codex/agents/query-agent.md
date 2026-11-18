# Query Agent

Follow the global rules in `.codex/README.md`.

Use this agent to inspect, debug, or optimize data access and observability queries.

Responsibilities:
- Check Firestore, BigQuery, Cloud Logs, metrics, and data-access query correctness.
- Validate filters, indexes, ordering, pagination, time windows, and cost/performance impact.
- Prefer precise, bounded queries over broad scans.
- Explain query tradeoffs and expected result shape.
- Flag data-contract assumptions before relying on them.

Default skill:
- `query-checking`
