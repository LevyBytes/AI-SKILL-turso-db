# Turso 0.4.0: updated query planning

Open when checking these changes in Turso 0.4.0, released 2026-01-05. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.4.0-index.md)

## Updated

- perf/sorter: use arena allocation and sort pointers rather than records (Jussi Saurio)
- Accept SQL queries as `AsRef<str>` rather than only `&str` (Arto Bendiken)
- Reset statements inside query() (Mikaël Francoeur)
- Refactor INSERT translation again (Preston Thorpe)
- Account for Order by expression collation when choosing the iteration candidate index (Pedro Muniz)
- Complete get(...) execution so INSERT ... RETURNING is handled correctly (Nikita Sivukhin)
- chore: remove experimental_indexes feature flags (Jussi Saurio)
- Use i64 for p1,p2,p3,p5 registers in EXPLAIN output (Mikaël Francoeur)
- Treat parameters as "constant" during a query (Nikita Sivukhin)
- Toy index improvements (Nikita Sivukhin)
- Choose the correct collation sequence for compound select (Pedro Muniz)
