# Cdc

> Database Core reference. Original prose; identifiers preserved verbatim.

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

### Decoding Binary Records

 The `before`, `after`, and `updates` columns keep binary-encoded data. Decode those values with the built-in helper functions:

```
SELECT
  change_type,
  table_name,
  id,
  bin_record_json_object(table_columns_json_array('users'), after) AS after_state,
  bin_record_json_object(table_columns_json_array('users'), before) AS before_state
FROM turso_cdc
WHERE table_name = 'users' AND change_type != 2;

```

| Function | Description |
| --- | --- |
| `table_columns_json_array(table_name)` | Returns a JSON array of table column names, such as `["id","name","email"]` |
| `bin_record_json_object(columns_json, blob)` | Decodes a binary record into a JSON object using the supplied column names |

### Transactions

 CDC follows transaction boundaries. Records are written only after a transaction commits; rolled-back transactions produce no CDC entries. Rows from one transaction share the same `change_txn_id`, with one final COMMIT record (`change_type = 2`):

```
BEGIN;
INSERT INTO users VALUES (1, 'Alice', 'alice@example.com');
INSERT INTO users VALUES (2, 'Bob', 'bob@example.com');
UPDATE users SET name = 'Charles' WHERE id = 1;
COMMIT;
-- All 3 changes + 1 COMMIT record share the same change_txn_id

```

### Schema Changes

 CDC also records DDL activity, such as CREATE TABLE, DROP TABLE, and CREATE INDEX, as changes on the `sqlite_schema` table:

```
PRAGMA capture_data_changes_conn('full');

CREATE TABLE products (id INTEGER PRIMARY KEY, name TEXT);
-- Recorded in turso_cdc as a change to sqlite_schema

```

### Multiple Connections

 CDC settings are independent for each connection. Separate connections can write change records to separate tables:

```
-- Connection 1: Capture to 'audit_log'
PRAGMA capture_data_changes_conn('full,audit_log');

-- Connection 2: Capture to 'sync_queue'
PRAGMA capture_data_changes_conn('id,sync_queue');

```

### Example

```
-- Create a table
CREATE TABLE users (
    id INTEGER PRIMARY KEY,
    name TEXT,
    email TEXT
);

-- Enable full CDC
PRAGMA capture_data_changes_conn('full');

-- Make some changes
INSERT INTO users VALUES (1, 'Alice', 'alice@example.com');
INSERT INTO users VALUES (2, 'Bob', 'bob@example.com');
UPDATE users SET email = 'alice@newdomain.com' WHERE id = 1;
DELETE FROM users WHERE id = 2;

-- View the captured changes with decoded data
SELECT
  change_id,
  CASE change_type
    WHEN 1 THEN 'INSERT'
    WHEN 0 THEN 'UPDATE'
    WHEN -1 THEN 'DELETE'
    WHEN 2 THEN 'COMMIT'
  END AS operation,
  table_name,
  id,
  bin_record_json_object(table_columns_json_array('users'), after) AS after_state
FROM turso_cdc
WHERE change_type != 2
ORDER BY change_id;

```
