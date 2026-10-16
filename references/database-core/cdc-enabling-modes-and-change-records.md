# Cdc enabling modes and change records

[Context index](change-data-capture-workflows.md) · [Continue](cdc-decoding-transactions-and-schema-changes.md)

Change Data Capture (CDC) records every table data change, including inserts, updates, and deletes. The change records are written to a local table that can be queried like any other table, which makes CDC useful for reactive apps, cross-system sync, auditing, and similar workflows.

### Enable CDC

 Turn on CDC for a connection with a PRAGMA:

```
PRAGMA capture_data_changes_conn('full');

```

 To disable:

```
PRAGMA capture_data_changes_conn('off');

```

 CDC cannot run on the same connection as [MVCC](https://docs.turso.tech/tursodb/concurrent-writes); the two modes are mutually exclusive.

### Capture Modes

| Mode | Description |
| --- | --- |
| `id` | Record only the primary key/rowid for changed rows |
| `before` | Record the row contents before updates or deletes |
| `after` | Record the row contents after inserts or updates |
| `full` | Record before and after row states plus per-column update metadata |

### CDC Table

 By default, change records go into the `turso_cdc` table. You can choose a different table name:

```
PRAGMA capture_data_changes_conn('full,my_changes_table');

```

 The CDC table uses this schema:

| Column | Type | Description |
| --- | --- | --- |
| `change_id` | INTEGER | Auto-incrementing unique identifier |
| `change_time` | INTEGER | Timestamp (Unix epoch) |
| `change_txn_id` | INTEGER | Transaction ID that groups rows into transactions |
| `change_type` | INTEGER | `1` = INSERT, `0` = UPDATE, `-1` = DELETE, `2` = COMMIT |
| `table_name` | TEXT | Table name for the table that changed |
| `id` | varies | Primary key/rowid for the changed row |
| `before` | BLOB | Row contents before the change for `before` and `full` modes |
| `after` | BLOB | Row contents after the change for `after` and `full` modes |
| `updates` | BLOB | Per-column change details (mode: `full`) |

### Querying Changes

```
-- All changes
SELECT * FROM turso_cdc;

-- Only inserts
SELECT * FROM turso_cdc WHERE change_type = 1;

-- Only updates
SELECT * FROM turso_cdc WHERE change_type = 0;

-- Only deletes
SELECT * FROM turso_cdc WHERE change_type = -1;

-- Changes for a specific table
SELECT * FROM turso_cdc WHERE table_name = 'users';

-- Changes in the last hour
SELECT * FROM turso_cdc WHERE change_time > unixepoch() - 3600;

```
