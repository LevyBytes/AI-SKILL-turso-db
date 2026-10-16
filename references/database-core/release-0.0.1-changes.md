# Turso 0.0.1: changes

Open when checking these changes in Turso 0.0.1, released 2024-07-17. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.0.1-index.md)

## Added

- Add partial `SELECT` statement handling with `WHERE`, `LIKE`, `LIMIT`, `CROSS JOIN`, and `INNER JOIN`.
- Aggregate function support.
- `EXPLAIN` statement support.
- Partial `PRAGMA` statement support, including `cache_size`.
- Add asynchronous I/O through Linux io_uring direct I/O and Darwin kqueue.
- First implementation pass for the command line shell and its commands.
