# Update, Upsert, Vacuum

> Sql Reference reference. Original prose; identifiers preserved verbatim.

## Overview

Includes: UPDATE; INSERT â€¦ ON CONFLICT (Upsert); VACUUM.

## UPDATE

### UPDATE

 An `UPDATE` statement revises column values for rows already present in a table.

### Syntax

```
UPDATE [OR conflict-algorithm] table-name
SET column-name = expression [, column-name = expression ...]
[FROM from-clause]
[WHERE expression]
[RETURNING result-column [, result-column ...]]
[ORDER BY sort-expression [, sort-expression ...]]
[LIMIT expression [OFFSET expression]]

```

### Description

 `UPDATE` assigns different values to columns on rows selected by the `WHERE` condition. Without a `WHERE` clause, the statement applies to every row in the table.

### Conflict Handling

 Use the optional `OR` clause to choose the response to constraint violations during an update. If it is left out, `ABORT` is used.
| Algorithm | Behavior |
| --- | --- |
| `ABORT` | Undo the current statement and return an error. The transaction stays open. This is the default. |
| `ROLLBACK` | Undo the complete transaction and report an error. |
| `FAIL` | Return an error at the first constraint violation, while retaining changes already made by earlier rows in this statement. |
| `IGNORE` | Ignore the row that triggered the constraint violation and keep processing the rest of the rows. |
| `REPLACE` | Remove the conflicting row, then update the current row. If a NOT NULL constraint fails without a DEFAULT value, the statement falls back to `ABORT`. |

```
UPDATE OR IGNORE employees
SET department = 'Engineering'
WHERE department = 'IT';

```

### SET Clause

 With `SET`, one or more columns receive replacement values. Each assignment pairs a column name with an equals sign and an expression. That expression may use other columns from the same row, and it reads every column as it was before the update began.

```
UPDATE products
SET price = price * 1.10,
    updated_at = datetime('now');

```

 A parenthesized column list can be used to assign several columns at once:

```
UPDATE products
SET (price, updated_at) = (price * 1.10, datetime('now'));

```

### FROM Clause

 `FROM` lets an `UPDATE` pull in other tables. Joined-table columns are available in `SET` expressions and in the `WHERE` clause, which makes it possible to update one table from related rows in another.

```
UPDATE inventory
SET quantity = inventory.quantity - order_items.qty
FROM order_items
WHERE inventory.product_id = order_items.product_id
  AND order_items.order_id = 1001;

```

 If the target table is named again in `FROM`, that reference is a separate instance of the table and can be used for self-joins.

### WHERE Clause

 `WHERE` selects the rows to modify. Rows are changed only when the expression is true; without `WHERE`, the update touches every row in the table.

### RETURNING Clause

 `RETURNING` makes an `UPDATE` return the post-update values for each row it changes. Returned columns may be expressions that refer to the updated values.

```
UPDATE accounts
SET balance = balance - 100
WHERE account_id = 42
RETURNING account_id, balance;
-- Returns: 42|900

```

### ORDER BY and LIMIT

 `ORDER BY` and `LIMIT` narrow the set of rows changed. `ORDER BY` controls the consideration order, while `LIMIT` sets the maximum number of rows modified. `ORDER BY` is valid only when `LIMIT` is also present.

```
UPDATE logs
SET archived = 1
ORDER BY created_at ASC
LIMIT 1000;

```

 `LIMIT` can include an optional `OFFSET` that skips rows before the limit is applied:

```
UPDATE logs
SET archived = 1
ORDER BY created_at ASC
LIMIT 1000 OFFSET 500;

```

### Examples

### Update rows matching a condition

```
CREATE TABLE employees (id INTEGER PRIMARY KEY, name TEXT, salary REAL, department TEXT);
INSERT INTO employees VALUES (1, 'Alice', 90000, 'Engineering');
INSERT INTO employees VALUES (2, 'Bob', 75000, 'Marketing');
INSERT INTO employees VALUES (3, 'Carol', 85000, 'Engineering');

UPDATE employees
SET salary = salary * 1.05
WHERE department = 'Engineering';

SELECT name, salary FROM employees WHERE department = 'Engineering';
-- Alice|94500.0
-- Carol|89250.0

```

### Update using a join with FROM

```
CREATE TABLE departments (name TEXT PRIMARY KEY, budget REAL);
INSERT INTO departments VALUES ('Engineering', 500000);
INSERT INTO departments VALUES ('Marketing', 200000);

CREATE TABLE employees2 (id INTEGER PRIMARY KEY, name TEXT, bonus REAL, dept TEXT);
INSERT INTO employees2 VALUES (1, 'Alice', 0, 'Engineering');
INSERT INTO employees2 VALUES (2, 'Bob', 0, 'Marketing');

UPDATE employees2
SET bonus = departments.budget * 0.01
FROM departments
WHERE employees2.dept = departments.name;

SELECT name, bonus FROM employees2;
-- Alice|5000.0
-- Bob|2000.0

```

### Update with RETURNING

```
CREATE TABLE counters (name TEXT PRIMARY KEY, value INTEGER);
INSERT INTO counters VALUES ('page_views', 41);

UPDATE counters
SET value = value + 1
WHERE name = 'page_views'
RETURNING name, value;
-- page_views|42

```

### See Also

- [INSERT](https://docs.turso.tech/sql-reference/statements/insert) when adding rows to a table
- [DELETE](https://docs.turso.tech/sql-reference/statements/delete) when rows need to be removed
- [INSERT â€¦ ON CONFLICT](https://docs.turso.tech/sql-reference/statements/upsert) when an insert should conditionally update instead
- [SELECT](https://docs.turso.tech/sql-reference/statements/select) when reading rows

## INSERT … ON CONFLICT (Upsert)

### INSERT … ON CONFLICT (Upsert)

 The upsert clause extends `INSERT` by defining behavior for inserts that would break a `UNIQUE` or `PRIMARY KEY` constraint. Rather than returning an error, the statement may skip the conflicting row or update the existing one.

### Syntax

```
INSERT INTO table-name [(column-name [, column-name ...])]
VALUES (expression [, expression ...]) [, ...]
ON CONFLICT [(conflict-target)] DO NOTHING
[ON CONFLICT [(conflict-target)] DO NOTHING | DO UPDATE ...]

INSERT INTO table-name [(column-name [, column-name ...])]
VALUES (expression [, expression ...]) [, ...]
ON CONFLICT [(conflict-target)] DO UPDATE
    SET column-name = expression [, column-name = expression ...]
    [WHERE expression]
[ON CONFLICT [(conflict-target)] DO NOTHING | DO UPDATE ...]

```

 Where `conflict-target` is:

```
(column-name [, column-name ...]) [WHERE expression]

```

### Description

 Place an upsert clause at the end of an `INSERT` statement to deal with constraint violations without failing outright. It offers two actions: `DO NOTHING` skips the conflicting row without noise, and `DO UPDATE` changes the existing row in place.

### DO NOTHING

 `DO NOTHING` makes `INSERT` ignore any row that would violate the named constraint. It raises no error and keeps the existing row as is. With a conflict target, only violations of that constraint are skipped. Without a conflict target, any constraint violation is skipped.

```
INSERT INTO users (id, name) VALUES (1, 'Alice')
ON CONFLICT DO NOTHING;

```

### DO UPDATE

 `DO UPDATE` makes `INSERT` revise the existing row when a constraint violation happens. The `SET` clause identifies the columns to change and the values to write. `DO UPDATE` requires a conflict target.

```
INSERT INTO counters (name, value) VALUES ('hits', 1)
ON CONFLICT (name) DO UPDATE
    SET value = counters.value + 1;

```

### Conflict Target

 The conflict target states which `UNIQUE` or `PRIMARY KEY` constraint the clause handles. It is written as a parenthesized list of column names, and that list must exactly match an existing unique constraint on the table. For partial unique indexes, meaning indexes with a `WHERE` clause, the conflict target also needs a `WHERE` clause matching the index condition.

```
-- Given: CREATE UNIQUE INDEX idx ON t(a, b) WHERE c > 0
INSERT INTO t VALUES (1, 2, 3)
ON CONFLICT (a, b) WHERE c > 0 DO NOTHING;

```

### The `excluded` Table

 Within the `SET` and `WHERE` clauses of `DO UPDATE`, the special `excluded` table holds the values attempted by the insert. Update expressions can therefore refer to the current row through the table name and to the candidate row through `excluded`.
| Reference | Meaning |
| --- | --- |
| `table-name.column` | Current column value from the existing row |
| `excluded.column` | The attempted insert value that produced the conflict |

```
INSERT INTO products (sku, name, price) VALUES ('A001', 'Widget', 12.99)
ON CONFLICT (sku) DO UPDATE
    SET name = excluded.name,
        price = excluded.price;

```

### WHERE Clause on DO UPDATE

 `DO UPDATE` may add a `WHERE` expression to limit when the update is performed. When that `WHERE` condition is false for a conflicting row, the row is not changed and the insert is skipped silently, matching `DO NOTHING`.

```
INSERT INTO products (sku, name, price) VALUES ('A001', 'Widget', 8.99)
ON CONFLICT (sku) DO UPDATE
    SET price = excluded.price
    WHERE excluded.price < products.price;

```

 Here, the price changes only when the incoming price is below the current price.

### Multiple ON CONFLICT Clauses

 A single `INSERT` statement may include several `ON CONFLICT` clauses, with each one aimed at a different constraint. They run in sequence; on a constraint violation, the first matching clause applies.

```
INSERT INTO users (id, email, name) VALUES (1, 'alice@example.com', 'Alice')
ON CONFLICT (id) DO UPDATE SET name = excluded.name, email = excluded.email
ON CONFLICT (email) DO NOTHING;

```

 Only one `ON CONFLICT` clause may leave out the conflict target. The targetless clause is a catch-all, must be last, and can only use `DO NOTHING`.

### Examples

### Upsert a counter

```
CREATE TABLE counters (name TEXT PRIMARY KEY, value INTEGER NOT NULL DEFAULT 0);

-- First insert creates the row
INSERT INTO counters (name, value) VALUES ('logins', 1)
ON CONFLICT (name) DO UPDATE SET value = counters.value + excluded.value;

SELECT * FROM counters;
-- logins|1

-- Subsequent inserts increment the counter
INSERT INTO counters (name, value) VALUES ('logins', 1)
ON CONFLICT (name) DO UPDATE SET value = counters.value + excluded.value;

SELECT * FROM counters;
-- logins|2

```

### Bulk insert, skipping duplicates

```
CREATE TABLE tags (id INTEGER PRIMARY KEY, label TEXT UNIQUE);
INSERT INTO tags VALUES (1, 'rust');
INSERT INTO tags VALUES (2, 'sql');

-- Insert several tags, skipping any that already exist
INSERT INTO tags VALUES (2, 'sql'), (3, 'wasm'), (4, 'database')
ON CONFLICT DO NOTHING;

SELECT * FROM tags;
-- 1|rust
-- 2|sql
-- 3|wasm
-- 4|database

```

### Conditional update with excluded

```
CREATE TABLE prices (
    product_id INTEGER PRIMARY KEY,
    price REAL NOT NULL,
    updated_at TEXT NOT NULL
);
INSERT INTO prices VALUES (1, 29.99, '2025-01-01');

-- Only update the price if the new timestamp is more recent
INSERT INTO prices (product_id, price, updated_at) VALUES (1, 24.99, '2025-06-15')
ON CONFLICT (product_id) DO UPDATE
    SET price = excluded.price,
        updated_at = excluded.updated_at
    WHERE excluded.updated_at > prices.updated_at;

SELECT * FROM prices;
-- 1|24.99|2025-06-15

```

### See Also

- [INSERT](https://docs.turso.tech/sql-reference/statements/insert) for ordinary insert statements
- [REPLACE](https://docs.turso.tech/sql-reference/statements/replace) when conflict handling should delete and insert again
- [UPDATE](https://docs.turso.tech/sql-reference/statements/update) when existing rows should be changed directly
- [CREATE TABLE](https://docs.turso.tech/sql-reference/statements/create-table) when defining `UNIQUE` and `PRIMARY KEY` constraints

## VACUUM

### VACUUM

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
