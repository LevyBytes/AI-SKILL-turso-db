# Pragma change capture modes and records

[Context index](multiprocess-and-pragma-reference.md) · [Setup and preceding context](pragma-database-metadata-and-page-sizes.md) · [Previous phase](pragma-integrity-checks-and-wal-checkpoints.md) · [Continue](pragma-encryption-and-custom-types.md)

### capture_data_changes_conn

 Turns on CDC for the current connection and writes captured changes into a selected table.

```
PRAGMA capture_data_changes_conn('mode');
PRAGMA capture_data_changes_conn('mode,table_name');

```

 For backwards compatibility, Turso still accepts the legacy name `unstable_capture_data_changes_conn`.

| Parameter | Description |
| --- | --- |
| mode | Capture mode: `off`, `id`, `before`, `after`, `full` |
| table_name | Custom table used to store changes (default: `turso_cdc`) |

#### Capture Modes

| Mode | Description |
| --- | --- |
| `off` | Disable CDC for this connection |
| `id` | Capture only the primary key/rowid for rows that changed |
| `before` | Capture the row state before updates/deletes |
| `after` | Capture the row state after inserts/updates |
| `full` | Capture before and after row states along with update details |

#### CDC Table Structure

 The CDC table has these columns:

| Column | Type | Description |
| --- | --- | --- |
| change_id | INTEGER | Auto-incrementing unique identifier |
| change_time | INTEGER | Unix epoch timestamp |
| change_type | INTEGER | 1 (INSERT), 0 (UPDATE), -1 (DELETE) |
| table_name | TEXT | Name of the changed table |
| id | varies | Primary key/rowid for the row that changed |
| before | BLOB | Row data from before the change (modes: before, full) |
| after | BLOB | Row data from after the change (modes: after, full) |
| updates | BLOB | Updated column details (mode: full) |

#### CDC Examples

```
-- Enable full CDC
PRAGMA capture_data_changes_conn('full');

-- Make changes
CREATE TABLE users (id INTEGER PRIMARY KEY, name TEXT);
INSERT INTO users VALUES (1, 'Alice');
UPDATE users SET name = 'Alicia' WHERE id = 1;
DELETE FROM users WHERE id = 1;

-- Query the changes
SELECT change_type, table_name, id FROM turso_cdc;
-- 1  | users | 1    (INSERT)
-- 0  | users | 1    (UPDATE)
-- -1 | users | 1    (DELETE)

-- Use a custom table name
PRAGMA capture_data_changes_conn('full,audit_log');

-- Disable CDC
PRAGMA capture_data_changes_conn('off');

```

 CDC follows transaction boundaries. Turso records changes only when the transaction commits; a rollback creates no CDC entries.

### Encryption

 **Turso Extension**: At-rest encryption is specific to Turso. This experimental feature must be [enabled before use](https://docs.turso.tech/sql-reference/experimental-features).
