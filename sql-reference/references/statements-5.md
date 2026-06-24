# Explain, Insert, Replace

> Sql Reference reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: EXPLAIN; INSERT; REPLACE.

## EXPLAIN

### EXPLAIN

 EXPLAIN reports how Turso would run a SQL statement. It supports two modes: `EXPLAIN` returns virtual machine bytecode, while `EXPLAIN QUERY PLAN` summarizes the query execution plan.

### Syntax

```
EXPLAIN statement;
EXPLAIN QUERY PLAN statement;

```

| Parameter | Type | Description |
| --- | --- | --- |
| statement | SQL | Any SQL statement, such as SELECT, INSERT, UPDATE, DELETE, and similar statements. |

### EXPLAIN

 Adding the `EXPLAIN` prefix makes Turso report the virtual machine (VDBE) opcode sequence that would run the statement, without actually running that statement.

```
EXPLAIN SELECT * FROM users WHERE id = 1;
-- addr | opcode     | p1 | p2 | p3 | p4       | p5
-- 0    | Init       | 0  | 9  | 0  |          | 0
-- 1    | OpenRead   | 0  | 2  | 0  | 3        | 0
-- 2    | SeekRowid  | 0  | 8  | 1  |          | 0
-- ...

```

### Output Columns

| Column | Description |
| --- | --- |
| addr | Instruction address (sequential integer) |
| opcode | Name of the operation, such as OpenRead, SeekRowid, Column, or ResultRow. |
| p1 | First operand |
| p2 | Second operand |
| p3 | Third operand |
| p4 | The fourth operand, commonly text such as a table name or collation. |
| p5 | Fifth operand (flags) |

### EXPLAIN QUERY PLAN

 Use `EXPLAIN QUERY PLAN` before a statement to get a high-level summary of the optimizer's chosen execution approach. For performance analysis, this is usually easier to interpret than raw EXPLAIN output.

```
EXPLAIN QUERY PLAN SELECT * FROM users WHERE name = 'Alice';
-- id | parent | notused | detail
-- 2  | 0      | 0       | SCAN users

```

```
CREATE INDEX idx_name ON users(name);
EXPLAIN QUERY PLAN SELECT * FROM users WHERE name = 'Alice';
-- id | parent | notused | detail
-- 3  | 0      | 0       | SEARCH users USING INDEX idx_name (name=?)

```

### Output Columns

| Column | Description |
| --- | --- |
| id | Distinct step identifier. |
| parent | Parent step id, with 0 meaning top-level. |
| notused | Reserved for future use (always 0) |
| detail | Readable explanation of what this execution step does. |

### Common Detail Messages

| Detail Pattern | Meaning |
| --- | --- |
| `SCAN table` | Table-wide scan (no index used). |
| `SEARCH table USING INDEX idx (col=?)` | Lookup through an index on the named column. |
| `SEARCH table USING INTEGER PRIMARY KEY (rowid=?)` | Direct rowid lookup |
| `USE TEMP B-TREE FOR ORDER BY` | Sorting will use a temporary B-tree. |
| `USE TEMP B-TREE FOR DISTINCT` | Deduplication will use a temporary B-tree. |
| `COMPOUND SUBQUERY` | Signals a set operation: UNION, INTERSECT, or EXCEPT. |
| `CORRELATED SCALAR SUBQUERY` | Correlated subquery producing one scalar value. |

### Examples

### Comparing Query Plans

```
CREATE TABLE orders (
    id INTEGER PRIMARY KEY,
    customer_id INTEGER,
    total REAL
);

-- Without index: full table scan
EXPLAIN QUERY PLAN SELECT * FROM orders WHERE customer_id = 42;
-- SCAN orders

-- After creating an index
CREATE INDEX idx_customer ON orders(customer_id);
EXPLAIN QUERY PLAN SELECT * FROM orders WHERE customer_id = 42;
-- SEARCH orders USING INDEX idx_customer (customer_id=?)

```

### Join Order

```
EXPLAIN QUERY PLAN
SELECT o.id, c.name
FROM orders o
INNER JOIN customers c ON c.id = o.customer_id
WHERE c.name = 'Alice';
-- SCAN orders AS o
-- SEARCH customers AS c USING INTEGER PRIMARY KEY (rowid=?)

```

### Subqueries

```
EXPLAIN QUERY PLAN
SELECT * FROM orders
WHERE customer_id IN (SELECT id FROM customers WHERE active = 1);
-- SCAN orders
-- LIST SUBQUERY
--   SCAN customers

```

### See Also

- [ANALYZE](https://docs.turso.tech/sql-reference/statements/analyze) for gathering statistics used to improve query plans.
- [CREATE INDEX](https://docs.turso.tech/sql-reference/statements/create-index) for defining indexes that make queries faster.

## INSERT

### INSERT

 Creates one or more table rows from literal VALUES, data returned by a SELECT, or column DEFAULT definitions.

### Syntax

```
INSERT [OR conflict_action] INTO table_name [(column_name [, ...])]
  VALUES (expression [, ...]) [, (expression [, ...]) ...]
  [RETURNING result_column [, ...]];

INSERT [OR conflict_action] INTO table_name [(column_name [, ...])]
  select_statement
  [RETURNING result_column [, ...]];

INSERT [OR conflict_action] INTO table_name [(column_name [, ...])]
  DEFAULT VALUES
  [RETURNING result_column [, ...]];

```

### Parameters

| Parameter | Type | Description |
| --- | --- | --- |
| `conflict_action` | keyword | One of REPLACE, IGNORE, ABORT, ROLLBACK, or FAIL; determines how constraint failures are handled. |
| `table_name` | identifier | The table that will receive the inserted rows. |
| `column_name` | identifier | Target column that receives a value; columns left out use their default value or NULL. |
| `expression` | expression | A value to insert, or the keyword `DEFAULT` when the column's default should be used. It must correspond to the target column by position or name. |
| `select_statement` | SELECT query | A query whose returned rows are added to the table. |
| `result_column` | expression | Column or expression returned for every inserted row. |

### Description

 INSERT creates rows in the named table and supports three variants:
- **VALUES**: Adds one or more rows using explicit values.
- **SELECT**: Adds rows produced by a query result set.
- **DEFAULT VALUES**: Creates one row where each column takes its default, or NULL when no default exists.
 If a column list appears, explicit data is assigned only to those columns; remaining columns fall back to defaults or NULL. Without a column list, the statement must include values for all columns, following the table's declared column order.

### Inserting Rows

### Single Row

```
INSERT INTO employees (name, department, salary)
VALUES ('Alice', 'Engineering', 95000);

```

### Multiple Rows

 Use several comma-delimited parenthesized groups.

```
INSERT INTO employees (name, department, salary) VALUES
  ('Bob', 'Design', 82000),
  ('Carol', 'Engineering', 97000),
  ('Dave', 'Marketing', 74000);

```

### All Columns

 If no column list is present, include one value for each column, ordered as defined in the table.

```
-- Assuming employees has columns: id, name, department, salary
INSERT INTO employees VALUES (1, 'Alice', 'Engineering', 95000);

```

### INSERT INTO … SELECT

 Copies rows produced by a SELECT statement. The SELECT result column count has to equal the target-column count.

```
INSERT INTO archive_employees (name, department, salary)
SELECT name, department, salary
FROM employees
WHERE termination_date IS NOT NULL;

```

 A SELECT used here may use any valid clause, including WHERE, JOIN, GROUP BY, ORDER BY, LIMIT, UNION, or subqueries.

```
INSERT INTO monthly_summary (department, headcount, total_salary)
SELECT department, COUNT(*), SUM(salary)
FROM employees
GROUP BY department;

```

### DEFAULT VALUES

 Creates one row with defaults assigned to every column. Columns lacking an explicit DEFAULT definition become NULL, and INTEGER PRIMARY KEY columns get an automatically generated rowid.

```
CREATE TABLE audit_log (
  id INTEGER PRIMARY KEY,
  created_at TEXT DEFAULT (datetime('now')),
  action TEXT DEFAULT 'unknown'
);

INSERT INTO audit_log DEFAULT VALUES;
-- Inserts: (auto_id, current_timestamp, 'unknown')

```

### Column Default Values

 With a column list, omitted columns fall back to their default values.

```
CREATE TABLE tasks (
  id INTEGER PRIMARY KEY,
  title TEXT NOT NULL,
  status TEXT DEFAULT 'pending',
  priority INTEGER DEFAULT 0,
  created_at TEXT DEFAULT (datetime('now'))
);

-- status, priority, and created_at use their defaults
INSERT INTO tasks (title) VALUES ('Review pull request');

```

### DEFAULT Keyword in VALUES

 **Turso extension** - This behavior matches SQL:2016 in the SQL standard, but SQLite does not implement it. In a VALUES list, the `DEFAULT` keyword may stand wherever a value expression would appear. It evaluates to the default declared for that column in the CREATE TABLE statement, or to NULL when no default exists. This helps with multi-row INSERT statements where defaults are needed in different columns on different rows, a case that column omission alone cannot express.

```
CREATE TABLE tasks (
  id INTEGER PRIMARY KEY,
  title TEXT NOT NULL,
  status TEXT DEFAULT 'pending',
  priority INTEGER DEFAULT 0
);

-- Use DEFAULT for specific columns
INSERT INTO tasks (id, title, status, priority)
VALUES (1, 'Fix bug', DEFAULT, 5);
-- Inserts: (1, 'Fix bug', 'pending', 5)

-- Different columns use DEFAULT in different rows
INSERT INTO tasks (id, title, status, priority) VALUES
  (2, 'Write docs', DEFAULT, 3),
  (3, 'Code review', 'in_progress', DEFAULT);
-- Inserts: (2, 'Write docs', 'pending', 3)
--          (3, 'Code review', 'in_progress', 0)

```

 The `DEFAULT` keyword may appear only in INSERT VALUES lists. Placing it in SELECT, WHERE, or UPDATE contexts raises an error.

### Conflict Handling

 The `OR` clause defines the response to INSERT conflicts involving uniqueness or NOT NULL constraints.
| Action | Behavior |
| --- | --- |
| `ABORT` | Cancel the current statement and report an error. Rows already added earlier in the same INSERT are reverted. This is the default. |
| `ROLLBACK` | Revert the whole transaction and report an error. |
| `FAIL` | Halt processing and report an error. Any rows this statement inserted before the failure remain. |
| `IGNORE` | Ignore the violating row and proceed to the following row. |
| `REPLACE` | Remove the conflicting row, then add the replacement row. |

```
-- Skip rows that violate a UNIQUE constraint
INSERT OR IGNORE INTO employees (id, name, department, salary)
VALUES (1, 'Alice', 'Engineering', 95000);

-- Replace the existing row on conflict
INSERT OR REPLACE INTO employees (id, name, department, salary)
VALUES (1, 'Alice', 'Engineering', 105000);

-- Roll back only this statement on conflict
INSERT OR ABORT INTO employees (id, name, department, salary)
VALUES (1, 'Alice', 'Engineering', 95000);

```

 For conflict rules tied to particular constraints, use the [ON CONFLICT](https://docs.turso.tech/sql-reference/statements/upsert) clause (UPSERT); it supports choosing actions according to the constraint that failed.

### RETURNING Clause

 Produces values from rows that were successfully inserted. It is handy when the caller needs generated data such as auto-incremented IDs or computed defaults.

```
INSERT INTO employees (name, department, salary)
VALUES ('Eve', 'Engineering', 91000)
RETURNING id, name;
-- Returns: 5|Eve

```

 RETURNING may contain any expression able to reference columns from the inserted row.

```
INSERT INTO employees (name, department, salary)
VALUES ('Frank', 'Design', 78000)
RETURNING id, name, salary * 12 AS annual_salary;
-- Returns: 6|Frank|936000

```

### RETURNING with Multiple Rows

```
INSERT INTO employees (name, department, salary) VALUES
  ('Grace', 'Engineering', 102000),
  ('Hank', 'Marketing', 68000)
RETURNING id, name, department;
-- Returns one row per inserted row

```

### RETURNING *

 Return every column from the inserted rows.

```
INSERT INTO employees (name, department, salary)
VALUES ('Ivy', 'Product', 88000)
RETURNING *;

```

### Examples

### Insert with Subquery Values

```
INSERT INTO department_stats (department, avg_salary)
SELECT department, AVG(salary)
FROM employees
GROUP BY department
HAVING COUNT(*) >= 3;

```

### Insert with Conflict Handling and RETURNING

```
INSERT OR REPLACE INTO settings (key, value)
VALUES ('theme', 'dark'), ('locale', 'en-US')
RETURNING key, value;

```

### Archival Pattern

```
-- Copy rows to archive, then delete originals
INSERT INTO orders_archive
SELECT * FROM orders WHERE order_date < '2023-01-01';

DELETE FROM orders WHERE order_date < '2023-01-01';

```

### See Also

- [INSERT ... ON CONFLICT (UPSERT)](https://docs.turso.tech/sql-reference/statements/upsert) for insert-or-update logic selected by conditions.
- [REPLACE](https://docs.turso.tech/sql-reference/statements/replace) for the abbreviated form of INSERT OR REPLACE.
- [SELECT](https://docs.turso.tech/sql-reference/statements/select) for the SELECT grammar used in INSERT INTO ... SELECT.
- [CREATE TABLE](https://docs.turso.tech/sql-reference/statements/create-table) for defaults and column declarations.

## REPLACE

### REPLACE

 The `REPLACE` statement adds a row to a table. If the new row conflicts with a `UNIQUE` or `PRIMARY KEY` constraint, the existing conflicting row is removed before the replacement is added.

### Syntax

```
REPLACE INTO table-name [(column-name [, column-name ...])]
VALUES (expression [, expression ...]) [, (expression [, expression ...]) ...]

REPLACE INTO table-name [(column-name [, column-name ...])]
select-statement

```

### Description

 `REPLACE` has the same effect as `INSERT OR REPLACE`. When a `REPLACE` row would breach a `UNIQUE` or `PRIMARY KEY` constraint, the row causing the conflict is removed and the replacement row is then added. Without such a violation, `REPLACE` works the same as `INSERT`.

### How REPLACE Differs from INSERT

 A normal `INSERT` stops with an error when a constraint violation occurs. With `REPLACE`, the database removes the conflicting row without warning and puts the new row there. Because that removal happens first, `REPLACE` may run `DELETE` triggers for the old row and `INSERT` triggers for the replacement row. `REPLACE` does not patch the old row; it removes the whole row and writes a new one. Any columns left out of the `REPLACE` therefore take default values instead of inheriting data from the removed row. To preserve existing column data while changing selected columns, use [INSERT ... ON CONFLICT DO UPDATE](https://docs.turso.tech/sql-reference/statements/upsert) instead.

### Constraint Behavior

 If a table has several `UNIQUE` constraints, `REPLACE` removes all rows that conflict with the incoming row before performing the insert. A single `REPLACE` can therefore delete multiple rows. If `REPLACE` violates a `NOT NULL` constraint on a column without a `DEFAULT` value, it errors.

### Examples

### Basic REPLACE

```
CREATE TABLE settings (key TEXT PRIMARY KEY, value TEXT);

INSERT INTO settings VALUES ('theme', 'light');
SELECT * FROM settings;
-- theme|light

-- The existing row is deleted and a new row is inserted
REPLACE INTO settings VALUES ('theme', 'dark');
SELECT * FROM settings;
-- theme|dark

```

### REPLACE deletes and re-inserts

 Unlike `UPDATE`, `REPLACE` discards the previous row as a whole. Omitted columns are filled from defaults:

```
CREATE TABLE users (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    email TEXT UNIQUE,
    created_at TEXT DEFAULT (datetime('now'))
);

INSERT INTO users VALUES (1, 'Alice', 'alice@example.com', '2025-01-01');

-- This deletes row with id=1 and inserts a completely new row.
-- The created_at column gets its DEFAULT value, not '2025-01-01'.
REPLACE INTO users (id, name, email) VALUES (1, 'Alice', 'alice@new.com');

SELECT id, name, email FROM users;
-- 1|Alice|alice@new.com

```

### Multi-row REPLACE

```
CREATE TABLE inventory (sku TEXT PRIMARY KEY, quantity INTEGER);
INSERT INTO inventory VALUES ('A001', 10);
INSERT INTO inventory VALUES ('B002', 20);

REPLACE INTO inventory VALUES
    ('A001', 15),
    ('B002', 25),
    ('C003', 30);

SELECT * FROM inventory;
-- A001|15
-- B002|25
-- C003|30

```

### See Also

- [INSERT](https://docs.turso.tech/sql-reference/statements/insert) for adding rows when conflict handling is not requested.
- [INSERT ... ON CONFLICT](https://docs.turso.tech/sql-reference/statements/upsert) for controlled insert-or-update behavior that chooses which columns change.
- [UPDATE](https://docs.turso.tech/sql-reference/statements/update) for changing existing rows without replacing them.
