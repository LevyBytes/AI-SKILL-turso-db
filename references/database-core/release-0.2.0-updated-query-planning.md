# Turso 0.2.0: updated query planning

Open when checking these changes in Turso 0.2.0, released 2025-10-03. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.2.0-index.md)

## Updated

- Reject INDEXED BY / NOT INDEXED in select (Jussi Saurio)
- Make connect() method optional by invoking it implicitly on first query execution (Nikita Sivukhin)
- Declare Sorter Send and Sync (Pekka Enberg)
- Improve DBSP view serialization (Glauber Costa)
- Reject ORDER BY and LIMIT inside a non-compound VALUES() statement (Jussi Saurio)
- Stop raising n_changes for idx delete (Preston Thorpe)
- Lower allocation use in `break_predicate_at_and_boundaries` (Lâm Hoàng Phúc)
- translation: Rewrite expressions and handle quoted identifiers in UPSERT correctly (Preston Thorpe)
- core/incremental: Store ViewTransactionState in Arc (Pekka Enberg)
- Handle `EXPLAIN QUERY PLAN` like SQLite (Lâm Hoàng Phúc)
- Change UPSERT to walk AST with wal_expr_mut. (Preston Thorpe)
- Allow indexes to be used in DELETE statements (Jussi Saurio)
