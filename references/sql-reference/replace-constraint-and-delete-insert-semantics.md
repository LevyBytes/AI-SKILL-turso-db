# Replace constraint and delete insert semantics

[Context index](explain-insert-and-replace-reference.md) · [Continue](replace-multiple-row-example.md)

## REPLACE

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
