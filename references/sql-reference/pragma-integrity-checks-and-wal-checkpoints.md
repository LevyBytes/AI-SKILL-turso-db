# Pragma integrity checks and wal checkpoints

[Context index](multiprocess-and-pragma-reference.md) · [Setup and preceding context](pragma-database-metadata-and-page-sizes.md) · [Previous phase](pragma-cache-synchronization-and-query-settings.md) · [Continue](pragma-change-capture-modes-and-records.md)

### legacy_file_format

 Outputs the legacy file format flag.

```
PRAGMA legacy_file_format;

```

### ignore_check_constraints

 When this setting is enabled, CHECK constraints are skipped.

```
PRAGMA ignore_check_constraints = 1;   -- disable CHECK constraints
PRAGMA ignore_check_constraints = 0;   -- enable CHECK constraints

```

### Integrity Checks

### integrity_check

 Runs a comprehensive integrity check over the full database.

```
PRAGMA integrity_check;
-- ok

PRAGMA integrity_check(N);    -- check only the first N errors

```

 Outputs `ok` when no problems are detected; otherwise, it emits one row for each error.

### quick_check

 Runs a faster integrity check than `integrity_check`, with less coverage.

```
PRAGMA quick_check;

```

### WAL Operations

### wal_checkpoint

 Forces a WAL checkpoint.

```
PRAGMA wal_checkpoint;

```

 A checkpoint copies pages from the WAL file back into the database file.

### Change Data Capture

 **Turso Extension**: Change Data Capture (CDC) is specific to Turso and records all data changes for replication, auditing, and reactive applications.
