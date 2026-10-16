# Turso 0.4.0: updated schema and constraints

Open when checking these changes in Turso 0.4.0, released 2026-01-05. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.4.0-index.md)

## Updated

- Block column drops when fk references are present (Preston Thorpe)
- Label triggers as experimental (Jussi Saurio)
- Obtain a mutable table reference from Schema so `Arc::make_mut` can modify it (Pedro Muniz)
- Execute BEFORE and AFTER update triggers for upserts (Mikaël Francoeur)
- Forbid index creation on the rowid pseudo-column (Mikaël Francoeur)
- Handle Col name in trigger subquery (Rohith Suresh)
- Drop triggers when their table drops (Pavan Nambi)
- Block DROP TABLE when foreign keys reference the table (Joao Faria)
- core/vdbe: handle child FK definition renames in rename table stmt (Preston Thorpe)
- Reject misuse of subqueries returning multiple columns (Jussi Saurio)
- Refactor and optimize the schema::Column type (Preston Thorpe)
