# Vacuum rebuild copy and MVCC requirements

[Context index](update-upsert-and-vacuum-reference.md) · [Continue](vacuum-backups-rebuilds-and-errors.md)

## VACUUM

 VACUUM rebuilds the database file so unused space is reclaimed, tables and indexes are defragmented, and the file can shrink. It has two supported forms: `VACUUM` rebuilds the current database in place, and `VACUUM INTO` creates a compacted copy at a new file path while leaving the source alone.

### Syntax

```
VACUUM [schema-name];
VACUUM [schema-name] INTO filename;

```

| Parameter | Description |
| --- | --- |
| `schema-name` | Schema to vacuum. In-place `VACUUM` supports only `main`; `VACUUM INTO` accepts any attached schema. `temp` performs no work and creates no file. The default is `main`. |
| `filename` | String literal naming the destination file path for `VACUUM INTO`. Bind parameters are not allowed. |

### Common Requirements

 The following rules are shared by `VACUUM` and `VACUUM INTO`:
- The connection has to be in autocommit mode; neither form is allowed inside an explicit `BEGIN` transaction.
- The same connection cannot have another active statement.
- The connection cannot be in `query_only` mode.
- The source database cannot use `auto_vacuum = incremental`; incremental autovacuum is unsupported.

### VACUUM

 In-place `VACUUM` is experimental and must be [enabled before use](https://docs.turso.tech/sql-reference/experimental-features). `VACUUM INTO` is always available and needs no flag. In-place `VACUUM` rebuilds the main database by creating a compacted image in an internal temp database, then copying those pages over the original file. After it finishes, unused pages are gone and every storage-backed object has been recreated.

### Effect

- Deleted-row and dropped-object pages are discarded, reducing the file size.
- Every storage-backed table is recreated and its rows are inserted again, rebuilding the related indexes.
- `sqlite_sequence` counters for `AUTOINCREMENT` columns are kept intact.
- The schema cookie is incremented so other connections refresh cached schema data the next time they access it.
- Page size, reserved space, text encoding, user version, and application ID remain exactly the same.

### Requirements

 Beyond the common requirements:
- The database has to be opened with WAL journal mode.
- The database cannot be in-memory.
- The database cannot be read-only.
- Only the `main` database works. In-place vacuuming of attached schemas is not supported yet.
- The multi-process WAL cannot currently be held by any other process.

### MVCC Databases

 For databases using MVCC (`PRAGMA journal_mode = mvcc`), in-place `VACUUM` has extra requirements:
- Checkpoint all MVCC changes before running it. If the MVCC log has uncheckpointed changes, `VACUUM` returns an error; run `PRAGMA wal_checkpoint(TRUNCATE)` first.
- No MVCC transaction can be active on any connection. If one exists, `VACUUM` returns a busy error.
- The MVCC subsystem pauses during the operation so the rebuilt schema and log can be reconciled atomically.

### VACUUM INTO

 `VACUUM INTO` creates a compacted database copy at the supplied path and does not alter the source database. The destination is a complete standalone database file.

### Effect

- A new database file appears at `filename` with all user tables, indexes, triggers, views, and virtual table content copied from the source.
- After data copying completes, indexes, triggers, and views are recreated, preventing triggers from firing during the copy.
- Custom index methods, such as FTS and vector, rebuild their backing structures from the copied rows.
- `sqlite_sequence` counters for `AUTOINCREMENT` columns are kept intact.
- The destination copies the source page size, reserved space, text encoding, user version, and application ID.
- When the source uses MVCC, the destination starts with MVCC enabled and fresh state. The MVCC metadata table from the source is not copied.
- Before the statement returns, the file is fully synced and a TRUNCATE checkpoint is run, making the destination durable with no extra step.
 `VACUUM INTO` does not propagate encryption to the destination. If the source is encrypted, the compacted copy is still written as a regular unencrypted database file and opens without a key. Reserved header bytes are copied, so the destination page layout still matches the source.

### Requirements

 Beyond the common requirements:
- The destination file cannot already exist. Remove or move it before overwriting.
- The SQL statement must supply the path as a string literal.
- `VACUUM temp INTO filename` performs no work and creates no file, matching SQLite behavior.
 `VACUUM INTO` gets a consistent view of the source by opening an implicit read transaction for the copy. As a result, the destination reflects one snapshot of the source even when other connections write during the operation.

### Examples
