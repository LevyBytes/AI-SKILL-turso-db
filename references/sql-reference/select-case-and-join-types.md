# Select case and join types

[Context index](select-and-transaction-reference.md) · [Setup and preceding context](select-columns-aliases-and-from.md) · [Previous phase](select-filtering-comparison-and-null-tests.md) · [Continue](select-join-using-grouping-and-distinct.md)

### CASE Expressions

```
SELECT name,
  CASE
    WHEN salary >= 100000 THEN 'Senior'
    WHEN salary >= 60000 THEN 'Mid'
    ELSE 'Junior'
  END AS level
FROM employees;

-- Simple CASE form
SELECT name,
  CASE department
    WHEN 'Engineering' THEN 'Eng'
    WHEN 'Marketing' THEN 'Mkt'
    ELSE 'Other'
  END AS dept_code
FROM employees;

```

### JOIN Clause

 Uses a related column to combine rows from two or more tables.

### Supported Join Types

| Join Type | Description |
| --- | --- |
| `INNER JOIN` or `JOIN` | Includes rows with matching values in both tables |
| `LEFT JOIN` or `LEFT OUTER JOIN` | Includes every row from the left table, using NULLs for right-side columns without matches |
| `FULL OUTER JOIN` | Includes every row from both tables, placing NULLs on whichever side has no match |
| `NATURAL JOIN` | Matches using every column name shared by both tables |
| `JOIN ... USING (column)` | Matches on the specified column present in both tables |

### INNER JOIN

 Includes only rows for which both tables satisfy the join condition.

```
SELECT e.name, d.department_name
FROM employees e
INNER JOIN departments d ON e.department_id = d.id;

```

### LEFT OUTER JOIN

 Includes every row from the left table. If the right table has no matching row, NULL fills the right-side columns.

```
SELECT e.name, m.name AS manager_name
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.id;

```

### FULL OUTER JOIN

 Includes every row from both tables. If a row from either table lacks a match in the other table, NULL fills the columns on the missing side.

```
SELECT e.name, d.department_name
FROM employees e
FULL OUTER JOIN departments d ON e.department_id = d.id;
-- Returns all employees (even without a department)
-- and all departments (even without employees)

```

### NATURAL JOIN

 Automatically matches every identically named column in both tables. This is equivalent to JOIN ... USING over every shared column name.

```
SELECT * FROM orders NATURAL JOIN customers;
-- Joins on every column name shared between orders and customers

```
