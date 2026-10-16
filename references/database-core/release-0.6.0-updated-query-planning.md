# Turso 0.6.0: updated query planning

Open when checking these changes in Turso 0.6.0, released 2026-05-14. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.6.0-index.md)

## Updated

- Make self-reading WHERE subqueries more stable (Kumar Ujjawal)
- optimizer: Use partial indexes correctly across additional query plans (Preston Thorpe)
- core/translate: Keep changes() unchanged after UPDATE OR REPLACE conflict deletion (Ethan Balakumar)
- Respect USING deduplication when resolving outer query refs (Jussi Saurio)
- Select the right statement origin for pragma vtab helpers (Jussi Saurio)
- Lower allocations in the multi-index analysis optimizer (Pedro Muniz)
- core/translate: record scope depth for outer query references (Pekka Enberg)
- translate/upsert: use emit_column_or_rowid for the DO UPDATE row snapshot (ongyimeng)
- core/translate: plan correlated subqueries at the right scope during window restructuring (Flanderzz)
- core/translate: disallow aggregate function misuse in ORDER BY (Pekka Enberg)
- Reject outer-scope refs from GROUP BY subquery trees (Kumar Ujjawal)
- core/translate: avoid `BETWEEN` expansion inside partial indexes (Duy Dang)
- Preserve cached expression collations without leaking aggregate/window state (Kumar Ujjawal)
- Exclude deleted rows during reverse secondary index scans (Kumar Ujjawal)
- Reset DISTINCT aggregate state for correlated subqueries (Kumar Ujjawal)
- core/translate/perf: skip redundant Copy instructions for cached aggregate args (Jussi Saurio)
- core/translate: permit some datetime functions in expr indexes (Preston Thorpe)
- Optimizer spring cleaning (Jussi Saurio)
- optimizer: revise CTE materialization logic and subquery cost estimates (Jussi Saurio)
- core/translate: Enable CROSS JOINs (Preston Thorpe)
- optimizer: restructure access path selection across single-index and multi-index paths (Jussi Saurio)
- Skip sorting when a composite index satisfies ORDER BY (David Matějka)
- core/translate: main_loop refactor (Preston Thorpe)
- core: Optimize prepared statement recompilation check (Jussi Saurio)
- optimizer: tune statsless heuristics and improve EXPLAIN QUERY PLAN (Jussi Saurio)
- Preserve explicit LIMIT 0 handling in scalar subqueries (Vartan Babayan)
