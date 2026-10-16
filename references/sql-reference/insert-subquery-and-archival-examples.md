# Insert subquery and archival examples

[Context index](explain-insert-and-replace-reference.md) · [Setup and preceding context](insert-syntax-and-row-values.md) · [Previous phase](insert-conflicts-and-returning.md)

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
-- Run in one transaction; on any failure, ROLLBACK instead of continuing.
BEGIN IMMEDIATE;
-- Copy rows to archive, then delete originals
INSERT INTO orders_archive
SELECT * FROM orders WHERE order_date < '2023-01-01';

DELETE FROM orders WHERE order_date < '2023-01-01';
COMMIT;

```

### See Also

- [INSERT ... ON CONFLICT (UPSERT)](https://docs.turso.tech/sql-reference/statements/upsert) for insert-or-update logic selected by conditions.
- [REPLACE](https://docs.turso.tech/sql-reference/statements/replace) for the abbreviated form of INSERT OR REPLACE.
- [SELECT](https://docs.turso.tech/sql-reference/statements/select) for the SELECT grammar used in INSERT INTO ... SELECT.
- [CREATE TABLE](https://docs.turso.tech/sql-reference/statements/create-table) for defaults and column declarations.
