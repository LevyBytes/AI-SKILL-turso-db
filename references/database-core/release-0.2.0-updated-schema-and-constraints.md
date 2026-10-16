# Turso 0.2.0: updated schema and constraints

Open when checking these changes in Turso 0.2.0, released 2025-10-03. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.2.0-index.md)

## Updated

- Use explicit null when it set instead of column default value (Nikita Sivukhin)
- core/translate: Convert default column value from identifier to string literal (Preston Thorpe)
- core/translate: Save NOT NULL column constraint in schema table (Preston Thorpe)
- translate: disallow creating/dropping internal tables (Jussi Saurio)
- Reject multiple primary keys in table definition (Jussi Saurio)
- Return empty output for .schema command when table not found (Preston Thorpe)
- Autoincrement (Pavan Nambi)
- Normalize target table name identifier during table or column rename (Iaroslav Zeigerman)
- core: Protect Connection::schema with RwLock (Pekka Enberg)
- Block alter table with materialized views (Glauber Costa)
- core/schema: Speed up get_dependent_materialized_views() when no views exist (Pekka Enberg)
- Refactor parseschema (Jussi Saurio)
- Make Connection::query() verify its schema is up to date (Jussi Saurio)
