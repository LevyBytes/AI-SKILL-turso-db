# Create Virtual Table, Delete, Detach Database, Drop Domain ...

> Sql Reference reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: CREATE VIRTUAL TABLE; DELETE; DETACH DATABASE; DROP DOMAIN; DROP INDEX; DROP TABLE; DROP TRIGGER; DROP TYPE; DROP VIEW.

## CREATE VIRTUAL TABLE

### CREATE VIRTUAL TABLE

 CREATE VIRTUAL TABLE defines a table whose rows are produced dynamically by a module instead of being stored in the database file. Virtual tables let SQL query external sources, generated data, and table-valued functions.

### Syntax

```
CREATE VIRTUAL TABLE [IF NOT EXISTS] [schema-name.]table-name
    USING module-name [(module-arguments)];

```

### Description

 Row storage and lookup for a virtual table are handled by its module. That module defines the columns, returned data, and query-planning choices. You can query virtual tables with SELECT as you would ordinary tables, though INSERT, UPDATE, and DELETE work only when the module provides write behavior.

### Clauses

| Clause | Description |
| --- | --- |
| `IF NOT EXISTS` | Prevents the error normally raised when a table using that name is already present. |
| `schema-name` | Attached database where the virtual table should be created. If omitted, this uses `main`. |
| `table-name` | Name to assign to the virtual table. |
| `module-name` | Module name that supplies the virtual table implementation. |
| `module-arguments` | Comma-separated values provided to the module. Their syntax and interpretation depend on the selected module. |

### Available Modules

### csv

 The `csv` module reads a CSV file and presents the file contents as a read-only table. Before using it, load the csv extension with `load_extension('csv')`.
| Argument | Description |
| --- | --- |
| `filename` | Path of the CSV file that csv should read. |
| `header` | Use `yes` when row one supplies column names. The default is `no`. |
| `columns` | Expected column count when `header` is `no`. |
| `schema` | Explicit CREATE TABLE schema that declares column names and column types. |

### generate_series

 `generate_series` is a built-in table-valued function that emits integer values in sequence. No extension load step is required.
| Argument | Description |
| --- | --- |
| `start` | Inclusive starting value for the sequence. |
| `stop` | Inclusive ending value for the sequence. |
| `step` | Step size between generated values. Defaults to `1`. |

 `generate_series` may be called from the FROM clause as either a virtual table or a table-valued function.

### Examples

### Read a CSV File

```
-- Load the csv extension
SELECT load_extension('csv');

-- Create a virtual table from a CSV file with a header row
CREATE VIRTUAL TABLE employees USING csv(
    filename='employees.csv',
    header=yes
);

-- Query the CSV data with SQL
SELECT * FROM employees WHERE department = 'Engineering';

```

### Generate a Sequence of Integers

```
-- Use generate_series as a table-valued function
SELECT value FROM generate_series(1, 10);
-- value
-- 1
-- 2
-- ...
-- 10

-- Use with a step argument
SELECT value FROM generate_series(0, 100, 10);
-- value
-- 0
-- 10
-- 20
-- ...
-- 100

```

### Generate a Date Series with generate_series

```
-- Generate dates for the next 7 days
SELECT date('now', '+' || value || ' days') AS day
FROM generate_series(0, 6);

```

### See Also

- [SELECT](https://docs.turso.tech/sql-reference/statements/select) when you need to query virtual tables
- [Compatibility](https://docs.turso.tech/sql-reference/compatibility) for supported modules and related compatibility details

## DELETE

### DELETE

 Use the `DELETE` statement to remove table rows.

### Syntax

```
DELETE FROM table-name
[WHERE expression]
[RETURNING result-column [, result-column ...]]
[ORDER BY sort-expression [, sort-expression ...]]
[LIMIT expression [OFFSET expression]]

```

### Description

 `DELETE` deletes rows satisfying the `WHERE` condition. When `WHERE` is omitted, the operation deletes every row in the table. The table object remains; remove it with [DROP TABLE](https://docs.turso.tech/sql-reference/statements/drop-table).

### WHERE Clause

 `WHERE` controls which rows DELETE removes. Rows are affected only when the expression is true. Omitting `WHERE` makes every table row eligible for deletion.

```
DELETE FROM logs WHERE created_at < date('now', '-90 days');

```

### RETURNING Clause

 `RETURNING` makes `DELETE` output values from rows it deleted. Result columns may be expressions that refer to columns from the deleted row.

```
DELETE FROM sessions
WHERE expires_at < datetime('now')
RETURNING session_id, user_id;

```

### ORDER BY and LIMIT

 `ORDER BY` and `LIMIT` narrow the rows affected by `DELETE`. `ORDER BY` sets the consideration order, while `LIMIT` sets the maximum count removed. `ORDER BY` is valid only when `LIMIT` is also present.

```
DELETE FROM logs
ORDER BY created_at ASC
LIMIT 1000;

```

 `LIMIT` can include `OFFSET` to skip rows before the limit is applied:

```
DELETE FROM logs
ORDER BY created_at ASC
LIMIT 500 OFFSET 100;

```

 `ORDER BY` without `LIMIT` is not allowed on `DELETE` statements. To remove all matching rows in a defined order, supply a `LIMIT` greater than the expected match count.

### Examples

### Delete rows matching a condition

```
CREATE TABLE tasks (id INTEGER PRIMARY KEY, title TEXT, completed INTEGER);
INSERT INTO tasks VALUES (1, 'Write docs', 1);
INSERT INTO tasks VALUES (2, 'Review PR', 0);
INSERT INTO tasks VALUES (3, 'Fix bug', 1);

DELETE FROM tasks WHERE completed = 1;

SELECT * FROM tasks;
-- 2|Review PR|0

```

### Delete with RETURNING

```
CREATE TABLE queue (id INTEGER PRIMARY KEY, payload TEXT, priority INTEGER);
INSERT INTO queue VALUES (1, 'job_a', 10);
INSERT INTO queue VALUES (2, 'job_b', 5);
INSERT INTO queue VALUES (3, 'job_c', 10);

DELETE FROM queue
WHERE id = (SELECT id FROM queue ORDER BY priority DESC, id ASC LIMIT 1)
RETURNING id, payload;
-- 1|job_a

```

### Delete the oldest rows with ORDER BY and LIMIT

```
CREATE TABLE events (id INTEGER PRIMARY KEY, message TEXT, created_at TEXT);
INSERT INTO events VALUES (1, 'startup', '2025-01-01');
INSERT INTO events VALUES (2, 'request', '2025-01-02');
INSERT INTO events VALUES (3, 'shutdown', '2025-01-03');
INSERT INTO events VALUES (4, 'startup', '2025-01-04');

DELETE FROM events
ORDER BY created_at ASC
LIMIT 2;

SELECT * FROM events;
-- 3|shutdown|2025-01-03
-- 4|startup|2025-01-04

```

### See Also

- [UPDATE](https://docs.turso.tech/sql-reference/statements/update) when changing rows that already exist
- [INSERT](https://docs.turso.tech/sql-reference/statements/insert) when adding rows to a table
- [DROP TABLE](https://docs.turso.tech/sql-reference/statements/drop-table) when the entire table should be removed
- [SELECT](https://docs.turso.tech/sql-reference/statements/select) when reading data

## DETACH DATABASE

### DETACH DATABASE

 DETACH DATABASE disconnects a database that was attached to the current connection. After DETACH DATABASE runs, tables in that database cannot be reached through its schema name.

### Syntax

```
DETACH [DATABASE] schema-name;

```

### Description

 DETACH closes the attached-database connection named by `schema-name` and releases associated resources. Later references to `schema-name.table-name` fail.

### Clauses

| Clause | Description |
| --- | --- |
| `DATABASE` | Optional keyword with no behavioral effect. |
| `schema-name` | Schema name given to the database at ATTACH time. |

 `main` and `temp` cannot be detached. DETACH is also invalid during an active transaction. Commit or roll back any open transaction before running DETACH.

### Examples

### Detach a Database

```
ATTACH 'archive.db' AS archive;

-- Use the attached database
SELECT count(*) FROM archive.events;

-- Detach when done
DETACH DATABASE archive;

```

### Detach Without the DATABASE Keyword

```
DETACH archive;

```

### See Also

- [ATTACH DATABASE](https://docs.turso.tech/sql-reference/statements/attach-database) when attaching another database

## DROP DOMAIN

### DROP DOMAIN

 **Turso Extension**: DROP DOMAIN is Turso-specific and is not part of standard SQLite. This experimental feature must be [enabled explicitly](https://docs.turso.tech/sql-reference/experimental-features) before it is used. DROP DOMAIN deletes a user-defined domain from the database. After removal, new column definitions cannot use that domain name.

### Syntax

```
DROP DOMAIN [IF EXISTS] domain-name;

```

### Description

 DROP DOMAIN deletes the specified domain from the database. It must already exist unless `IF EXISTS` is included.

### Clauses

| Clause | Description |
| --- | --- |
| `IF EXISTS` | Avoids the error raised when the domain is missing. If no matching domain is found, the statement does nothing. |
| `domain-name` | Domain name to remove. |

### Restrictions

 You cannot drop a domain while a table column uses it or another domain has it as a base type. Remove or update all dependent definitions first.

```
CREATE DOMAIN base_d AS integer;
CREATE DOMAIN child_d AS base_d CHECK (value > 0);

-- Fails: child_d depends on base_d
DROP DOMAIN base_d;

-- Drop child first, then base
DROP DOMAIN child_d;
DROP DOMAIN base_d;

```

### DROP DOMAIN vs DROP TYPE

 DROP DOMAIN applies only to domains made with CREATE DOMAIN. Using DROP DOMAIN for a type made with CREATE TYPE raises an error, as does the reverse case.

```
CREATE TYPE my_type BASE integer ENCODE value DECODE value;
DROP DOMAIN my_type;
-- Error: 'my_type' is a type, not a domain. Use DROP TYPE instead

CREATE DOMAIN my_domain AS integer;
DROP TYPE my_domain;
-- Error: 'my_domain' is a domain, not a type. Use DROP DOMAIN instead

```

### Examples

### Drop a Domain

```
CREATE DOMAIN positive_int AS integer CHECK (value > 0);
DROP DOMAIN positive_int;

```

### Drop with IF EXISTS

```
-- Safe to run even if the domain does not exist
DROP DOMAIN IF EXISTS nonexistent_domain;

```

### Drop After Removing Table Dependency

```
CREATE DOMAIN score AS integer CHECK (value >= 0);
CREATE TABLE results (id INTEGER PRIMARY KEY, val score) STRICT;

-- Fails: table 'results' uses domain 'score'
-- DROP DOMAIN score;

DROP TABLE results;
DROP DOMAIN score;
-- OK

```

### See Also

- [CREATE DOMAIN](https://docs.turso.tech/sql-reference/statements/create-domain) when defining domains
- [DROP TYPE](https://docs.turso.tech/sql-reference/statements/drop-type) when removing custom types

## DROP INDEX

### DROP INDEX

 Drops an index from the database without changing the indexed table or its rows.

### Syntax

```
DROP INDEX [IF EXISTS] [schema-name.]index-name;

```

### Description

 `DROP INDEX` deletes an existing index. Once it is gone, the query planner cannot choose that index for optimization. Table contents stay the same.

### Parameters

| Parameter | Description |
| --- | --- |
| `IF EXISTS` | Avoids an error when the index is missing. If no such index exists, the statement does nothing. |
| `schema-name` | Attached database that contains the index. If omitted, this resolves to the main database. |
| `index-name` | Index name to drop. |

### Behavior

- You can drop only indexes that were created explicitly. Indexes generated for PRIMARY KEY and UNIQUE constraints cannot be removed directly - drop the table or use ALTER TABLE instead.
- Removing an index can slow queries that depended on it, but query results remain correct.

### Examples

### Drop an Index

```
DROP INDEX idx_users_email;

```

### Drop an Index Only if It Exists

```
DROP INDEX IF EXISTS idx_old_search;

```

### Drop an Index in an Attached Database

```
DROP INDEX aux.idx_sessions_token;

```

### See Also

- [CREATE INDEX](https://docs.turso.tech/sql-reference/statements/create-index) when creating indexes
- [EXPLAIN](https://docs.turso.tech/sql-reference/statements/explain) when checking query plans after an index drop

## DROP TABLE

### DROP TABLE

 Drops a table from the database together with its data, indexes, triggers, and constraints.

### Syntax

```
DROP TABLE [IF EXISTS] [schema-name.]table-name;

```

### Description

 `DROP TABLE` deletes the table definition and every row stored in that table. Related indexes, triggers, and constraints are deleted too.

### Parameters

| Parameter | Description |
| --- | --- |
| `IF EXISTS` | Avoids an error when the table is missing. If no such table exists, the statement does nothing. |
| `schema-name` | Attached database that contains the table. If omitted, this resolves to the main database. |
| `table-name` | Table name to drop. |

### Behavior

- Every row in the table is deleted.
- Indexes built on the table are removed.
- Triggers tied to the table are removed.
- The entry for the table is deleted from `sqlite_schema`.
- Foreign key constraints that point at the dropped table become invalid. Turso allows dropping a table even when other tables reference it through foreign keys.
- `DROP TABLE` cannot run while another statement in the same connection is reading from or writing to that table.

### Examples

### Drop a Table

```
DROP TABLE users;

```

### Drop a Table Only if It Exists

```
DROP TABLE IF EXISTS temporary_results;

```

### Drop a Table in an Attached Database

```
DROP TABLE aux.sessions;

```

### See Also

- [CREATE TABLE](https://docs.turso.tech/sql-reference/statements/create-table) when creating tables
- [ALTER TABLE](https://docs.turso.tech/sql-reference/statements/alter-table) when modifying tables
- [DELETE](https://docs.turso.tech/sql-reference/statements/delete) when rows should be removed while keeping the table

## DROP TRIGGER

### DROP TRIGGER

 DROP TRIGGER deletes a trigger from the database schema. Afterward, that trigger no longer fires for the table events it was associated with.

### Syntax

```
DROP TRIGGER [IF EXISTS] [schema-name.]trigger-name;

```

### Description

 DROP TRIGGER deletes the specified trigger from the database. It must exist unless `IF EXISTS` is present.

### Clauses

| Clause | Description |
| --- | --- |
| `IF EXISTS` | Avoids the error raised when the trigger is missing. If no matching trigger is found, the statement does nothing. |
| `schema-name` | Attached database that contains the trigger. If omitted, Turso looks for the trigger in the main database. |
| `trigger-name` | Trigger name to remove. |

 Dropping a trigger leaves the associated table's data unchanged. Only the trigger definition is deleted from the schema.

### Examples

### Drop a Trigger

```
CREATE TRIGGER log_insert AFTER INSERT ON users
BEGIN
    INSERT INTO audit_log (action, user_id) VALUES ('insert', NEW.id);
END;

-- Remove the trigger
DROP TRIGGER log_insert;

```

### Drop a Trigger with IF EXISTS

```
-- Safe to run even if the trigger does not exist
DROP TRIGGER IF EXISTS log_insert;

```

### Drop a Trigger in an Attached Database

```
DROP TRIGGER IF EXISTS aux.log_insert;

```

### See Also

- [CREATE TRIGGER](https://docs.turso.tech/sql-reference/statements/create-trigger) when creating triggers
- [CREATE TABLE](https://docs.turso.tech/sql-reference/statements/create-table) when working with table definitions

## DROP TYPE

### DROP TYPE

 **Turso Extension**: DROP TYPE is a Turso-specific statement and is not part of standard SQLite. This experimental feature must be [enabled explicitly](https://docs.turso.tech/sql-reference/experimental-features) before use. DROP TYPE deletes a user-defined custom type from the database. After removal, new column definitions cannot use that type name.

### Syntax

```
DROP TYPE [IF EXISTS] type-name;

```

### Description

 DROP TYPE deletes the specified custom type from the database. It must already exist unless `IF EXISTS` is included.

### Clauses

| Clause | Description |
| --- | --- |
| `IF EXISTS` | Avoids the error raised when the type is missing. If no matching type is found, the statement does nothing. |
| `type-name` | Custom type name to remove. |

### Restrictions

 A type cannot be dropped while any table column still uses it. Remove those columns or drop the table before dropping the type.

```
CREATE TYPE cents BASE integer ENCODE value * 100 DECODE value / 100;

CREATE TABLE prices (
    id INTEGER PRIMARY KEY,
    amount cents
) STRICT;

-- This fails because the 'prices' table has a column of type 'cents'
DROP TYPE cents;
-- Error: type 'cents' is in use

-- Drop the table first, then the type
DROP TABLE prices;
DROP TYPE cents;

```

### Examples

### Drop a Custom Type

```
CREATE TYPE reversed_text BASE text
    ENCODE turso_reverse(value)
    DECODE turso_reverse(value);

DROP TYPE reversed_text;

```

### Drop a Type with IF EXISTS

```
-- Safe to run even if the type does not exist
DROP TYPE IF EXISTS reversed_text;

```

### See Also

- [CREATE TYPE](https://docs.turso.tech/sql-reference/statements/create-type) when defining custom types
- [DROP DOMAIN](https://docs.turso.tech/sql-reference/statements/drop-domain) when removing domains
- [Data Types](https://docs.turso.tech/sql-reference/data-types) for a broader view of the type system

## DROP VIEW

### DROP VIEW

 Drops a view definition from the database without changing the underlying tables or their data.

### Syntax

```
DROP VIEW [IF EXISTS] [schema-name.]view-name;

```

### Description

 `DROP VIEW` deletes an existing view. Afterward, queries that use the view name fail. The tables named by the view are unchanged.

### Parameters

| Parameter | Description |
| --- | --- |
| `IF EXISTS` | Avoids an error when the view is missing. If no such view exists, the statement does nothing. |
| `schema-name` | Attached database that contains the view. If omitted, this resolves to the main database. |
| `view-name` | View name to drop. |

### Behavior

- INSTEAD OF triggers defined on the dropped view are removed as well.
- For a materialized view, stored data is removed too.

### Examples

### Drop a View

```
DROP VIEW active_users;

```

### Drop a View Only if It Exists

```
DROP VIEW IF EXISTS old_report;

```

### Drop a View in an Attached Database

```
DROP VIEW aux.cached_stats;

```

### See Also

- [CREATE VIEW](https://docs.turso.tech/sql-reference/statements/create-view) when creating views or materialized views
