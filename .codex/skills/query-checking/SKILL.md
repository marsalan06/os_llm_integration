---
name: query-checking
description: Use for checking and optimizing Firestore, BigQuery, Cloud Logs, metrics, and application data-access queries for correctness, cost, and performance.
---

# Query Checking

Follow the global rules in `.codex/README.md`.

Workflow:
1. Identify the data source, expected result shape, time window, and filters.
2. Verify field names, types, indexes, ordering, limits, pagination, and null/missing-field behavior.
3. Check cost and performance risks, especially broad scans and unbounded time ranges.
4. Prefer precise filters and explicit limits.
5. For logs and metrics, include project/service labels and freshness/window constraints.
6. Report the safer query shape and any index or schema assumptions.

Review for:
- Incorrect filters or joins.
- Missing time bounds.
- Missing ordering for pagination.
- Query cost surprises.
- Data-contract assumptions.
