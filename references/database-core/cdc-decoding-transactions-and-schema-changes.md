# Cdc decoding transactions and schema changes

[Context index](change-data-capture-workflows.md) · [Setup and preceding context](cdc-enabling-modes-and-change-records.md) · [Continue](cdc-complete-change-tracking-example.md)

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
