# Turso 0.2.0: fixed query planning

Open when checking these changes in Turso 0.2.0, released 2025-10-03. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.2.0-index.md)

## Fixed

- Repair self-insert that uses a nested subquery (Mikaël Francoeur)
- Index search fixes (Nikita Sivukhin)
- translate/upsert: correct UPSERT explicit conflict target for non-rowid primary key (Preston Thorpe)
- Fix zero limit (Nikita Sivukhin)
- Correct where clause problems in materialized views (Glauber Costa)
- Fix offset variable handling (Nikita Sivukhin)
- Repair CREATE INDEX behavior for quoted identifiers (Iaroslav Zeigerman)
- Correct offset clause handling for ungrouped aggregate queries (Preston Thorpe)
- parser: correct LIMIT/OFFSET parsing for LIMIT x,y form (Jussi Saurio)
- Fix result columns binding precedence (Jussi Saurio)
- Repair INSERT INTO t DEFAULT VALUES (Jussi Saurio)
- fix: repair planner resolution for CTE aliases (Mayank)
- DBSP: Report a parse error for non-equality joins (Glauber Costa)
- translate/insert: correct `program.result_columns` for multi-row inserts (Preston Thorpe)
- Fixes views (Glauber Costa)
- Fix automatic indexes (Jussi Saurio)
