# Select exists cte and window queries

[Context index](select-and-transaction-reference.md) · [Setup and preceding context](select-columns-aliases-and-from.md) · [Previous phase](select-order-limits-and-scalar-subqueries.md) · [Continue](select-named-windows-and-set-operations.md)

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
