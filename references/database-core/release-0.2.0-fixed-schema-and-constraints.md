# Turso 0.2.0: fixed schema and constraints

Open when checking these changes in Turso 0.2.0, released 2025-10-03. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.2.0-index.md)

## Fixed

- Correct JOIN USING column selection to use left table instead of right (Jussi Saurio)
- Resolve rowid alias/PK to the proper column name (Preston Thorpe)
- Correct DROP COLUMN index bookkeeping (Jussi Saurio)
- Repair joined-query column fetching (Glauber Costa)
- Correct spelling in the ForeignKey ast node (Preston Thorpe)
- Resolve column alias after rewritting column access in returning insert clause expression (Nikita Sivukhin)
- Fix various ALTER TABLE bugs (Jussi Saurio)
- Remove incorrect "column is ambiguous" error for USING clause (Jussi Saurio)
- Repair .schema command behavior on empty databases (Diego Reis)
