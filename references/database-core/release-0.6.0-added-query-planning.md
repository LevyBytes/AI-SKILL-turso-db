# Turso 0.6.0: added query planning

Open when checking these changes in Turso 0.6.0, released 2026-05-14. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.6.0-index.md)

## Added

- core/translate: Add support for `UPDATE FROM` (Pekka Enberg)
- core/translate: Support compound SELECT inside WHERE-clause subqueries (Pekka Enberg)
- core/translate: Implement ORDER BY NULLS FIRST/LAST (Glauber Costa)
- Support ROW_NUMBER() (Kumar)
- Support ROW_NUMBER() (Kumar Ujjawal)
- Add DEFAULT keyword support in INSERT VALUES expressions (Glauber Costa)
- core: implement ORDER BY for compound SELECT statements (Glauber Costa)
- Support ROW_NUMBER() (Kumar Ujjawal)
- translate: implement INDEXED BY (Jussi Saurio)
- perf: Implement the Grace algorithm for hash joins to improve disk-spilling performance (Preston Thorpe)
- core: implement sqlite minmax optimization & unify simple agg handling & fix ordering cost model bug (Jussi Saurio)
- optimizer: support IN (list / uncorrelated subquery) as the driver for scan+seek (Jussi Saurio)
- Add PRAGMA requiring UPDATE/DELETE queries to include a WHERE clause (Avinash Sajjanshetty)
