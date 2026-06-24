# Select, Transactions

> Sql Reference reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: SELECT; Transactions.

## SELECT

### SELECT

 Reads rows from one table or several tables. In Turso, SELECT is the main read operation and can filter, join, aggregate, sort, use subqueries, and perform set operations.

### Syntax

```
[WITH cte_name AS (select_statement) [, ...]]
SELECT [DISTINCT] result_column [, ...]
  [FROM table_or_subquery [join_clause ...]]
  [WHERE expression]
  [GROUP BY expression [, ...]]
  [HAVING expression]
  [WINDOW window_name AS (window_definition) [, ...]]
  [compound_operator select_statement]
  [ORDER BY ordering_term [, ...]]
  [LIMIT expression [OFFSET expression]]

```

### Parameters

| Parameter | Type | Description |
| --- | --- | --- |
| `result_column` | expression, `*`, or `table.*` | Return columns or expressions. Use `*` to include every column |
| `table_or_subquery` | identifier or subquery | A table name, an aliased table, or a parenthesized SELECT |
| `expression` | expression | Any valid SQL expression |
| `ordering_term` | expression + direction | Expression plus optional ASC/DESC and NULLS FIRST/LAST |
| `compound_operator` | keyword | UNION, UNION ALL, INTERSECT, or EXCEPT |
| `cte_name` | identifier | Common Table Expression name |
| `window_name` | identifier | Reusable window definition name |

### Basic Queries

### Selecting Columns

```
-- All columns
SELECT * FROM employees;

-- Specific columns
SELECT name, department FROM employees;

-- Expressions and aliases
SELECT name, salary * 12 AS annual_salary FROM employees;

```

### Column and Table Aliases

 Apply `AS` when giving columns or tables aliases. For column aliases, the `AS` keyword can be omitted.

```
SELECT e.name, e.salary * 12 annual_pay
FROM employees AS e;

```

### FROM Clause

 The FROM clause identifies which tables supply the query data. It can take table names, aliased tables, subqueries, and join expressions.

```
-- Single table
SELECT * FROM employees;

-- Subquery as table source
SELECT * FROM (SELECT name, salary FROM employees WHERE salary > 50000) AS high_earners;

```

### WHERE Clause

 Uses a condition to filter rows. The result includes only rows for which the expression evaluates to true.

```
SELECT * FROM employees WHERE department = 'Engineering';

SELECT * FROM orders WHERE total > 100 AND status != 'cancelled';

```

### Comparison Operators

| Operator | Description |
| --- | --- |
| `=` | Equal |
| `!=` or `<>` | Not equal |
| `<` | Less than |
| `>` | Greater than |
| `<=` | Less than or equal |
| `>=` | Greater than or equal |
| `IS` | Equal (NULL-safe) |
| `IS NOT` | Not equal (NULL-safe) |
| `IS DISTINCT FROM` | Not identical, with NULL-safe SQL standard behavior |
| `IS NOT DISTINCT FROM` | Identical (NULL-safe, SQL standard) |

### Logical Operators

 Use `AND`, `OR`, and `NOT` to combine conditions.

```
SELECT * FROM products
WHERE (category = 'Electronics' OR category = 'Books')
  AND price < 50
  AND NOT discontinued;

```

### Pattern Matching

```
-- LIKE: case-insensitive pattern matching (% = any chars, _ = one char)
SELECT * FROM employees WHERE name LIKE 'J%';

-- GLOB: case-sensitive pattern matching (* = any chars, ? = one char)
SELECT * FROM files WHERE path GLOB '*.txt';

-- REGEXP: regular expression matching
SELECT * FROM logs WHERE message REGEXP '^ERROR:';

```

### Range and Membership Tests

```
-- BETWEEN (inclusive on both ends)
SELECT * FROM orders WHERE total BETWEEN 100 AND 500;

-- IN with a list
SELECT * FROM employees WHERE department IN ('Engineering', 'Design', 'Product');

-- IN with a subquery
SELECT * FROM employees WHERE department_id IN (SELECT id FROM departments WHERE active = 1);

```

### NULL Tests

```
SELECT * FROM employees WHERE manager_id IS NULL;

SELECT * FROM employees WHERE phone IS NOT NULL;

```

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

### JOIN … USING

 Matches by the specified column, which must be present in both tables. The result shows that shared column a single time.

```
SELECT * FROM orders JOIN customers USING (customer_id);

```

### Multi-Table Joins

```
SELECT o.id, c.name, p.product_name, oi.quantity
FROM orders o
JOIN customers c ON o.customer_id = c.id
JOIN order_items oi ON o.id = oi.order_id
JOIN products p ON oi.product_id = p.id
WHERE o.order_date > '2024-01-01';

```

### GROUP BY and HAVING

### GROUP BY

 Collects rows whose specified column values are the same. It is commonly paired with aggregate functions.

```
SELECT department, COUNT(*) AS employee_count, AVG(salary) AS avg_salary
FROM employees
GROUP BY department;

```

 Aggregate functions usable with GROUP BY:
| Function | Description |
| --- | --- |
| `COUNT(*)` | Row count for the group |
| `COUNT(expression)` | Number of non-NULL values |
| `SUM(expression)` | Sum of non-NULL values |
| `AVG(expression)` | Average of non-NULL values |
| `MIN(expression)` | Minimum value |
| `MAX(expression)` | Maximum value |
| `TOTAL(expression)` | REAL-valued sum (returns 0.0 rather than NULL for empty sets) |
| `GROUP_CONCAT(expression, separator)` | Concatenation of values |
| `STRING_AGG(expression, separator)` | Alias for GROUP_CONCAT |

### HAVING

 Applies filtering to groups after aggregation. WHERE filters individual rows before grouping; HAVING applies after groups are formed.

```
SELECT department, COUNT(*) AS headcount
FROM employees
GROUP BY department
HAVING COUNT(*) >= 5;

```

### GROUP BY with Expressions

```
SELECT
  CASE WHEN salary >= 80000 THEN 'High' ELSE 'Standard' END AS band,
  COUNT(*) AS count
FROM employees
GROUP BY CASE WHEN salary >= 80000 THEN 'High' ELSE 'Standard' END;

```

### DISTINCT

 Eliminates duplicate rows in the result set.

```
SELECT DISTINCT department FROM employees;

SELECT DISTINCT department, title FROM employees;

```

 DISTINCT is evaluated over the whole result row rather than one column. Rows count as duplicates only when all column values are identical.

### ORDER BY

 Orders the result set. If ORDER BY is absent, row order is unspecified.

```
SELECT * FROM employees ORDER BY salary DESC;

-- Multiple sort keys
SELECT * FROM employees ORDER BY department ASC, salary DESC;

-- Ordering by column position
SELECT name, salary FROM employees ORDER BY 2 DESC;

-- Ordering by alias
SELECT name, salary * 12 AS annual FROM employees ORDER BY annual DESC;

```

### NULLS FIRST / NULLS LAST

 Sets the position of NULL values within the sorted result.

```
-- NULLs at the end (default for ASC is NULLS LAST, for DESC is NULLS FIRST)
SELECT * FROM employees ORDER BY manager_id ASC NULLS LAST;

-- NULLs at the beginning
SELECT * FROM employees ORDER BY manager_id DESC NULLS FIRST;

```

| Direction | Default NULL Placement |
| --- | --- |
| ASC | NULLS LAST |
| DESC | NULLS FIRST |

### LIMIT and OFFSET

 Limits how many rows are returned and can also skip rows.

```
-- Return at most 10 rows
SELECT * FROM employees ORDER BY salary DESC LIMIT 10;

-- Skip 20 rows, then return 10
SELECT * FROM employees ORDER BY salary DESC LIMIT 10 OFFSET 20;

```

 Pair LIMIT and OFFSET with ORDER BY every time. If ORDER BY is missing, which rows are skipped or returned is arbitrary.

### Subqueries

 A subquery is a SELECT statement placed inside another query.

### Scalar Subqueries

 Produces one value and is valid anywhere an expression is expected.

```
SELECT name, salary,
  salary - (SELECT AVG(salary) FROM employees) AS diff_from_avg
FROM employees;

```

### Subqueries with IN

 Checks whether the value matches any row produced by the subquery.

```
SELECT * FROM products
WHERE category_id IN (SELECT id FROM categories WHERE active = 1);

SELECT * FROM products
WHERE category_id NOT IN (SELECT id FROM categories WHERE discontinued = 1);

```

### Subqueries with EXISTS

 Checks whether the subquery produces at least one row. The returned values themselves are ignored.

```
SELECT * FROM customers c
WHERE EXISTS (
  SELECT 1 FROM orders o WHERE o.customer_id = c.id AND o.total > 1000
);

```

### Subqueries in FROM

 Inside the FROM clause, a subquery behaves as a derived table and requires an alias.

```
SELECT dept, avg_salary
FROM (
  SELECT department AS dept, AVG(salary) AS avg_salary
  FROM employees
  GROUP BY department
) AS dept_stats
WHERE avg_salary > 70000;

```

### Common Table Expressions (CTE)

 A WITH clause creates one or more named temporary result sets for the query's duration.

```
WITH regional_sales AS (
  SELECT region, SUM(amount) AS total_sales
  FROM orders
  GROUP BY region
)
SELECT region, total_sales
FROM regional_sales
WHERE total_sales > 100000
ORDER BY total_sales DESC;

```

### Multiple CTEs

```
WITH
  active_customers AS (
    SELECT * FROM customers WHERE active = 1
  ),
  recent_orders AS (
    SELECT * FROM orders WHERE order_date > '2024-01-01'
  )
SELECT ac.name, COUNT(ro.id) AS order_count
FROM active_customers ac
JOIN recent_orders ro ON ac.id = ro.customer_id
GROUP BY ac.name;

```

 In Turso, CTEs are limited to SELECT. RECURSIVE CTEs and MATERIALIZED/NOT MATERIALIZED hints are unsupported.

### Window Functions

 A window function calculates over rows associated with the current row while leaving those rows as separate output rows.

```
SELECT name, department, salary,
  SUM(salary) OVER (PARTITION BY department ORDER BY salary) AS running_total
FROM employees;

```

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

### Examples

### Paginated Report with Aggregation

```
SELECT
  d.department_name,
  COUNT(e.id) AS headcount,
  ROUND(AVG(e.salary), 2) AS avg_salary,
  MIN(e.hire_date) AS earliest_hire
FROM departments d
LEFT JOIN employees e ON d.id = e.department_id
GROUP BY d.department_name
HAVING COUNT(e.id) > 0
ORDER BY avg_salary DESC
LIMIT 10 OFFSET 0;

```

### CTE with Filtered Join

```
WITH high_value_orders AS (
  SELECT customer_id, COUNT(*) AS order_count, SUM(total) AS total_spent
  FROM orders
  WHERE order_date >= '2024-01-01'
  GROUP BY customer_id
  HAVING SUM(total) > 5000
)
SELECT c.name, h.order_count, h.total_spent
FROM customers c
JOIN high_value_orders h ON c.id = h.customer_id
ORDER BY h.total_spent DESC;

```

### Subquery with EXISTS and LEFT JOIN

```
SELECT p.product_name, p.price, c.category_name
FROM products p
LEFT JOIN categories c ON p.category_id = c.id
WHERE EXISTS (
  SELECT 1 FROM order_items oi WHERE oi.product_id = p.id
)
ORDER BY p.price DESC
LIMIT 20;

```

### See Also

- [INSERT](https://docs.turso.tech/sql-reference/statements/insert) covers adding rows to a table
- [UPDATE](https://docs.turso.tech/sql-reference/statements/update) covers modifying existing rows
- [DELETE](https://docs.turso.tech/sql-reference/statements/delete) covers removing rows
- [CREATE VIEW](https://docs.turso.tech/sql-reference/statements/create-view) covers saving a query as a view
- [Data Types](https://docs.turso.tech/sql-reference/data-types) covers storage classes and type affinity

## Transactions

### Transactions

 Transactions combine multiple SQL statements into one atomic unit of work. In a transaction, all statements either succeed and get committed, or the whole transaction rolls back with no changes applied. Turso supports explicit transactions using BEGIN, COMMIT, and ROLLBACK, and each standalone statement also runs inside an implicit transaction (autocommit mode).

### Syntax

```
BEGIN [DEFERRED | IMMEDIATE | EXCLUSIVE] [TRANSACTION];

COMMIT [TRANSACTION];
END [TRANSACTION];

ROLLBACK [TRANSACTION];

```

### BEGIN

 BEGIN starts a new explicit transaction. After it is open, every following statement belongs to that transaction until COMMIT or ROLLBACK is issued.

### Transaction Types

| Type | Description |
| --- | --- |
| `DEFERRED` | Default. Locks are not acquired until the first read or write operation. Reads acquire a shared lock; writes acquire a write lock. |
| `IMMEDIATE` | Gets a write lock as soon as BEGIN executes. Other connections may continue reading, but they cannot write until the transaction completes. |
| `EXCLUSIVE` | In Turso, this behaves the same as IMMEDIATE. Turso always uses WAL mode, which makes EXCLUSIVE and IMMEDIATE equivalent. |

 When no type is specified, the transaction uses `DEFERRED` by default.

```
-- Start a deferred transaction (default)
BEGIN;

-- Start an immediate transaction
BEGIN IMMEDIATE;

-- The TRANSACTION keyword is optional
BEGIN IMMEDIATE TRANSACTION;

```

### COMMIT / END

 COMMIT, or its END alias, finalizes the transaction and writes every change to the database. After commit, changes are durable and visible to other connections.

```
BEGIN;
INSERT INTO accounts (name, balance) VALUES ('Alice', 1000);
INSERT INTO accounts (name, balance) VALUES ('Bob', 500);
COMMIT;

```

 COMMIT and END are interchangeable.

```
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE name = 'Alice';
END;

```

### ROLLBACK

 ROLLBACK aborts the active transaction and undoes every change made since BEGIN. The database returns to the state it had before that transaction began.

```
BEGIN;
DELETE FROM accounts WHERE balance < 0;
-- Changed our mind, undo everything
ROLLBACK;

```

### Implicit Transactions

 If no explicit transaction is active, Turso runs each individual statement inside an implicit transaction. This is autocommit mode. A statement is automatically committed when it succeeds and automatically rolled back when it fails.

```
-- Each of these runs in its own implicit transaction
INSERT INTO logs (msg) VALUES ('first');
INSERT INTO logs (msg) VALUES ('second');
-- If the second INSERT fails, the first INSERT is still committed

```

### Transaction Lifecycle

 A typical transaction lifecycle:

```
-- 1. Begin the transaction
BEGIN;

-- 2. Perform operations
UPDATE inventory SET quantity = quantity - 1 WHERE product_id = 42;
INSERT INTO orders (product_id, customer_id) VALUES (42, 7);

-- 3a. Commit if everything succeeded
COMMIT;

-- OR 3b. Rollback if something went wrong
ROLLBACK;

```

 Turso does not support SAVEPOINT or nested transactions. A connection may have at most one active transaction at any time.

### BEGIN CONCURRENT

 **Turso Extension**: BEGIN CONCURRENT is specific to Turso and is not available in standard SQLite. MVCC mode is required. BEGIN CONCURRENT starts an optimistic concurrent write transaction, allowing multiple connections to write at the same time.

### Syntax

```
BEGIN CONCURRENT [TRANSACTION];

```

### Requirements

 BEGIN CONCURRENT requires MVCC mode. Turn it on by setting the journal mode before any transactions are opened:

```
PRAGMA journal_mode = mvcc;

```

### How It Works

 A concurrent transaction uses a database snapshot captured when BEGIN CONCURRENT starts. Each connection reads its own snapshot and writes independently. On COMMIT, Turso checks whether another transaction modified the same rows after that snapshot:
- If no conflict exists, the transaction commits successfully.
- If Turso detects a write-write conflict, COMMIT fails, and the transaction must be rolled back and retried.
 This gives **snapshot isolation**: every transaction sees a consistent database view from its start time, and other writers are not blocked.

### Example

```
-- Connection 1
PRAGMA journal_mode = mvcc;
BEGIN CONCURRENT;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
COMMIT;

-- Connection 2 (running simultaneously)
PRAGMA journal_mode = mvcc;
BEGIN CONCURRENT;
UPDATE accounts SET balance = balance + 50 WHERE id = 2;
COMMIT;
-- Both succeed because they modified different rows

```

 When both connections modify the same row, the second COMMIT fails:

```
-- Connection 1
BEGIN CONCURRENT;
UPDATE accounts SET balance = 900 WHERE id = 1;
COMMIT;  -- succeeds

-- Connection 2 (started before Connection 1 committed)
BEGIN CONCURRENT;
UPDATE accounts SET balance = 800 WHERE id = 1;
COMMIT;  -- fails: write-write conflict detected
ROLLBACK;
-- Retry the transaction from BEGIN CONCURRENT

```

### See Also

- [INSERT](https://docs.turso.tech/sql-reference/statements/insert) covers inserting rows
- [UPDATE](https://docs.turso.tech/sql-reference/statements/update) covers updating rows
- [DELETE](https://docs.turso.tech/sql-reference/statements/delete) covers deleting rows
- [Compatibility](https://docs.turso.tech/sql-reference/compatibility) covers differences from SQLite
