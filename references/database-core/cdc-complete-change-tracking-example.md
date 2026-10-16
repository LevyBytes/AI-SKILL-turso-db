# Cdc complete change tracking example

[Context index](change-data-capture-workflows.md) · [Setup and preceding context](cdc-enabling-modes-and-change-records.md) · [Previous phase](cdc-decoding-transactions-and-schema-changes.md)

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
