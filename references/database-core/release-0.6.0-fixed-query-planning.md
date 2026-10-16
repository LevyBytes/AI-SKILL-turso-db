# Turso 0.6.0: fixed query planning

Open when checking these changes in Turso 0.6.0, released 2026-05-14. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.6.0-index.md)

## Fixed

- core/translate: fix hash joins emitting incorrect cross products (Preston Thorpe)
- fix/optimizer: resolve indexes by table reference instead of table name (fixes attach) (Jussi Saurio)
- fix(update): populate registers for chained virtual gen col values before rebuilding indexes (Mikaël Francoeur)
- core/translate: Correct `GROUP BY` infinite loop (H)
- core/translate: Correct panic in window-to-subquery rewrite with multiple window functions (Jussi Saurio)
- core/translate: restore missing affinity for IN with compound subquery (Hossam Khero)
- Correct UPDATE Halloween bug for mutable index scans (Mikaël Francoeur)
- translate/refactor: remove resolve_label() footgun (Jussi Saurio)
- Correct infinite loop on INSERT with two partial indexes (Jussi Saurio)
- core/translate: Correct out of bounds caused by spurious covering index (Mikaël Francoeur)
- Correct CTE sharing choices for correlated subqueries (Jussi Saurio)
- Correct expression index behavior and integrity check handling. (Glauber Costa)
- fix: permit dropping user-created UNIQUE indexes (#6359) (Srinivas A)
- core/translate: Correct GROUP BY out-of-range error message so it says "GROUP BY" (Pekka Enberg)
- Fix/6078 join limit offset false corruption (H0TB0X420)
- core: bind compound SELECT ORDER BY against all constituent SELECTs (Pekka Enberg)
- core/translate: Correct upsert being ignored when PK has ON CONFLICT IGNORE (Mikaël Francoeur)
- correct aggregate functions panicking on extreme float values (Alberto Moretti)
- core/translate: Correct GROUP BY alias resolution precedence (Alberto Moretti)
- fix: remove JSON subtypes at subquery/view boundaries (V. Can Keklik)
- perf/translate: additional optimizer bugfixes (Jussi Saurio)
- core/translate: Fix subquery evaluation timing (Preston Thorpe)
- fix: prevent CREATE INDEX from corrupting materialized views with sqlite_master data (Martin Mauch)
- Fix multiple hash join bugs (Jussi Saurio)
- Correct INSERT index versus NOT NULL DEFAULT mismatch (Martin Mauch)
- Fix window aggregate row value (practicat)
- Correct GROUP BY COLLATE sort-elision with implicit BINARY indexes (Kumar Ujjawal)
