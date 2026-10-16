# Turso 0.5.0: added query planning

Open when checking these changes in Turso 0.5.0, released 2026-03-04. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.5.0-index.md)

## Added

- partially build out FULL OUTER + LEFT and RIGHT hash joins (Preston Thorpe)
- Introduce support for FROM-clause subquery materialization and CTE materialization (Jussi Saurio)
- Implement multi-index scan access method (Preston Thorpe)
- fix: initialize result registers only for subqueries that have not been evaluated yet (Pedro Muniz)
- implement `PRAGMA temp_store` optimization (Preston Thorpe)
- Complete remaining ON CONFLICT ResolveTypes for INSERT and UPDATE statements (Preston Thorpe)
- Introduce partial ON CONFLICT support for UPDATE statements (Preston Thorpe)
- Enable CTE visibility in VALUES+RETURNING subqueries and permit explicit col names (Preston Thorpe)
- Introduce support for subqueries in the WHERE position for DML statements (Preston Thorpe)
