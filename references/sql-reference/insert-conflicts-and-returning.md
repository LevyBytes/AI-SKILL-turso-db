# Insert conflicts and returning

[Context index](explain-insert-and-replace-reference.md) · [Setup and preceding context](insert-syntax-and-row-values.md) · [Previous phase](insert-select-and-default-values.md) · [Continue](insert-subquery-and-archival-examples.md)

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
