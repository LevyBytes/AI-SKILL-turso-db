# Turso 0.0.2: changes

Open when checking these changes in Turso 0.0.2, released 2024-07-24. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.0.2-index.md)

## Added

- Partial `LEFT JOIN` support.
- Partial `ORDER BY` support.
- Partial scalar function support.
## Fixed

- Use a POSIX filesystem advisory lock at database open time so concurrent processes cannot corrupt the file. This lock model is not SQLite's transaction-by-transaction POSIX advisory locking; Limbo locks on open because that path is faster. (Issue #94)
## Changed

- Install under `~/.limbo/` rather than `CARGO_HOME`.
