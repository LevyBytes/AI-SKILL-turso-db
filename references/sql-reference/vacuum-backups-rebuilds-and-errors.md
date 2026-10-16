# Vacuum backups rebuilds and errors

[Context index](update-upsert-and-vacuum-reference.md) · [Setup and preceding context](vacuum-rebuild-copy-and-mvcc-requirements.md)

### Rebuild the Main Database

```
-- Reclaim space and defragment the current database
VACUUM;

```

### Write a Compacted Copy to a New File

```
VACUUM INTO 'backup.db';

```

 The source database remains unchanged. `backup.db` is usable as an independent database:

```
tursodb backup.db

```

### Vacuum an Attached Database Into a New File

```
ATTACH DATABASE 'archive.db' AS archive;

-- Write a compacted copy of the archive schema to a new file
VACUUM archive INTO 'archive-compact.db';

```

### Use VACUUM INTO for a Backup Snapshot

 Since `VACUUM INTO` reads from a consistent snapshot, it provides a straightforward point-in-time backup for an active database:

```
VACUUM INTO '/var/backups/app-2026-04-23.db';

```

### Restore Performance After Heavy Churn

 After large deletes or long-running workloads fragment indexes, `VACUUM` rebuilds those indexes and can improve scan performance:

```
DELETE FROM events WHERE created_at < '2025-01-01';
VACUUM;

```

### Errors

| Error | Cause |
| --- | --- |
| `VACUUM is an experimental feature. Enable with --experimental-vacuum flag` | In-place `VACUUM` was run in a release build without the experimental flag enabled. |
| `VACUUM is only supported for the main database; schema '<name>' is not supported yet` | In-place `VACUUM schema-name` targeted a schema other than `main`. |
| `Cannot execute VACUUM in query_only mode` | `VACUUM` or `VACUUM INTO` ran on a connection with `PRAGMA query_only` enabled. |
| `cannot VACUUM from within a transaction` / `cannot VACUUM INTO from within a transaction` | Run inside an explicit `BEGIN`. |
| `cannot VACUUM - SQL statements in progress` | Another statement remained active on the same connection. |
| `Incremental auto-vacuum is not supported` | The source database used `auto_vacuum = incremental`; this blocks both `VACUUM` and `VACUUM INTO`. |
| `VACUUM requires a WAL-mode database` | In-place `VACUUM` was attempted on a database not using WAL. |
| `cannot VACUUM an in-memory database` | In-place `VACUUM` was attempted on an in-memory database. |
| `ReadOnly` | In-place `VACUUM` was attempted on a read-only database. |
| `cannot VACUUM while experimental multiprocess WAL is active in another process` | In-place `VACUUM` was attempted while another process held the multi-process WAL. |
| `cannot VACUUM an MVCC database with uncheckpointed changes; run PRAGMA wal_checkpoint(TRUNCATE) first` | In-place `VACUUM` was attempted on an MVCC database with pending log entries. |
| `output file already exists: <path>` | The destination for `VACUUM INTO` already exists. |
| `VACUUM INTO path cannot be empty` | `VACUUM INTO ''` was used. |
| `VACUUM INTO requires a string literal path` | The destination was supplied with a non-literal expression, such as a bind parameter. |
| `no such database: <name>` | `VACUUM <schema> INTO` referenced an unknown schema. |

### See Also

- [Experimental Features](https://docs.turso.tech/sql-reference/experimental-features) for turning on in-place `VACUUM`
- [PRAGMAs](https://docs.turso.tech/sql-reference/pragmas) for `auto_vacuum`, `journal_mode`, `query_only`, and `wal_checkpoint` settings
- [ATTACH DATABASE](https://docs.turso.tech/sql-reference/statements/attach-database) when attaching a schema for use with `VACUUM INTO`
- [ANALYZE](https://docs.turso.tech/sql-reference/statements/analyze) when query planner statistics should be refreshed after a vacuum
