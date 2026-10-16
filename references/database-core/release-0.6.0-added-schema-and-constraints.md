# Turso 0.6.0: added schema and constraints

Open when checking these changes in Turso 0.6.0, released 2026-05-14. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.6.0-index.md)

## Added

- Make `WITHOUT ROWID` support experimental (Pekka Enberg)
- route FK NEW-image key reads through ColumnLayout (Mikaël Francoeur)
- Add STRUCT and UNION composite types for STRICT tables (Glauber Costa)
- Add CREATE DOMAIN / DROP DOMAIN support (Glauber Costa)
- Add support for CREATE TABLE AS SELECT (Glauber Costa)
- Implement Temporary tables (Preston Thorpe)
- cli: add sqlite-backed sqltests support for .schema and .tables (Rupesh Harode)
- fix: enforce FK constraints for columns introduced through ALTER TABLE ADD COLUMN (Harin)
- core/translate: Add emit_table_column for reading transparent virtual columns (Glauber Costa)
- Implement ALTER TABLE handling for virtual columns (Mikaël Francoeur)
- implement generated columns (Mikaël Francoeur)
- translate/triggers: support subqueries in WHEN position (Preston Thorpe)
- fix: Support ON CONFLICT definitions on PRIMARY KEY and UNIQUE columns (Jussi Saurio)
- Rewrite NEW/OLD inside nested trigger subqueries (Kumar Ujjawal)
- core/translate: Fix sqlite_sequence table initialization (Preston Thorpe)
