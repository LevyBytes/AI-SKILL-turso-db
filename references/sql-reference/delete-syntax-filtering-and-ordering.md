# Delete syntax filtering and ordering

[Context index](virtual-tables-delete-and-drop-reference.md) · [Continue](delete-returning-and-row-limits.md)

## DELETE

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
