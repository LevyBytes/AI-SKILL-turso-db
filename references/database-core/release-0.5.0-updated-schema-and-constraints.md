# Turso 0.5.0: updated schema and constraints

Open when checking these changes in Turso 0.5.0, released 2026-03-04. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.5.0-index.md)

## Updated

- core/translate: Apply explicit view column aliases (Karthik Koralla)
- Maintain sqlite_sequence during ALTER TABLE ... RENAME TO (Kumar Ujjawal)
- core/translate: honor usable constraint on a column even when an unusable one comes first (Jussi Saurio)
- compat: postpone CTE column count validation until use (Pedro Muniz)
- Permit trailing named column constraints in column definitions (Kumar Ujjawal)
- Refresh unique_sets during column rename to avoid schema corruption (Glauber Costa)
- core/schema: include table-level UNIQUE constraints when emitting BTreeTable::to_sql() (Jussi Saurio)
- Make strict mode non-experimental (Glauber Costa)
- core/translate: drop unnecessary Affinity instruction from integrity_check (Pekka Enberg)
- SQLite compat: limit ANY affinity special-casing to STRICT tables (Hossam Khero)
- Disallow triggers on virtual tables (Kumar Ujjawal)
- Reject column-level multi-column FK refs during ALTER TABLE (Kumar Ujjawal)
- Multi index operator constraint (Pedro Muniz)
- Drop custom index methods when parent table is dropped (Preston Thorpe)
- Disallow WITHOUT ROWID tables (Jussi Saurio)
