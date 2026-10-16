# Turso 0.3.0: updated schema and constraints

Open when checking these changes in Turso 0.3.0, released 2025-10-30. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.3.0-index.md)

## Updated

- parser: During column constraint parsing, convert boolean values to literal (Preston Thorpe)
- Block on conflict column definitions during CREATE TABLE or DB open (Preston Thorpe)
- Prevent name sharing across tables,indexs,vtabs,views (Pavan Nambi)
- Limit joins to max 63 tables and permit arbitrary number of table columns (Jussi Saurio)
- Emit correct column information for explain prepared statements (Nikita Sivukhin)
