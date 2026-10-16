# Select named windows and set operations

[Context index](select-and-transaction-reference.md) · [Setup and preceding context](select-columns-aliases-and-from.md) · [Previous phase](select-exists-cte-and-window-queries.md) · [Continue](select-reporting-and-filtered-join-examples.md)

### Named Windows

 Use the WINDOW clause to create a reusable window definition.

```
SELECT name, department, salary,
  SUM(salary) OVER w AS dept_running_total,
  COUNT(*) OVER w AS dept_running_count
FROM employees
WINDOW w AS (PARTITION BY department ORDER BY salary)
ORDER BY department, salary;

```

### Window Clause Syntax

```
function_name() OVER (
  [PARTITION BY expression [, ...]]
  [ORDER BY expression [ASC | DESC] [, ...]]
)

```

| Component | Description |
| --- | --- |
| `PARTITION BY` | Splits rows into groups (partitions). The function starts over for each partition |
| `ORDER BY` | Sets row order inside each partition |

 All standard aggregate functions (`SUM`, `AVG`, `COUNT`, `MIN`, `MAX`, `TOTAL`, `GROUP_CONCAT`) can run as window functions. Turso's implicit frame is RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW. Explicit frame clauses are not available, including bounded ROWS, RANGE, or GROUPS forms. Built-in ranking functions (row_number, rank, dense_rank) are not yet available.

### Set Operations

 Merge results from two or more SELECT statements.

| Operator | Description |
| --- | --- |
| `UNION` | Combines results and removes duplicates |
| `UNION ALL` | Combines results and keeps duplicates |
| `INTERSECT` | Includes only rows found in both result sets |
| `EXCEPT` | Includes rows in the first result set but absent from the second |

 Every set operation requires each SELECT to have the same number of columns and compatible types.

```
-- Customers who are also employees
SELECT name FROM customers
INTERSECT
SELECT name FROM employees;

-- All people from both tables, no duplicates
SELECT name, email FROM customers
UNION
SELECT name, email FROM employees;

-- Customers who are not employees
SELECT name FROM customers
EXCEPT
SELECT name FROM employees;

```

### UNION ALL

 Use UNION ALL when duplicates may remain; it is faster because deduplication is skipped.

```
SELECT id, 'order' AS source FROM orders
UNION ALL
SELECT id, 'return' AS source FROM returns;

```

### Set Operations with ORDER BY

 ORDER BY affects the whole combined result set, and it belongs after the final SELECT.

```
SELECT name FROM customers
UNION
SELECT name FROM employees
ORDER BY name;

```
