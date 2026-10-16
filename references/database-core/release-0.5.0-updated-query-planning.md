# Turso 0.5.0: updated query planning

Open when checking these changes in Turso 0.5.0, released 2026-03-04. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.5.0-index.md)

## Updated

- Revise update-or-replace ordering (Preston Thorpe)
- Hash joins: partition spilling improvements (Preston Thorpe)
- core/translate: correctly cache compound expressions from GROUP BY (Jussi Saurio)
- Allow DML statements to access tables via index method (Preston Thorpe)
- Decorrelate basic EXISTS / NOT EXISTS subqueries into semi/antijoins (Jussi Saurio)
- Turn off index-method access for DML (Kumar Ujjawal)
- Reject repeated CTE names in WITH clause (Kumar Ujjawal)
- Disallow FILTER clauses in view definitions (Kumar Ujjawal)
- Use Search on the WHERE rowid= predicate instead of a full scan (Preston Thorpe)
- cleanup: consolidate optimizer params and allow loading them from json (Jussi Saurio)
- Improve Insert performance (Preston Thorpe)
- Speed up hash joins (Preston Thorpe)
- Optimize more tpc-h queries (Pavan Nambi)
- feat/perf: use HashTable machinery for DISTINCT, replacing ephemeral btree (Jussi Saurio)
- Rewrite BETWEEN statements for partial indexes along DML paths (Preston Thorpe)
- planner: plan hash joins with safe build-side materialization (Preston Thorpe)
