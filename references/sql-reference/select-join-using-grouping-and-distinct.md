# Select join using grouping and distinct

[Context index](select-and-transaction-reference.md) · [Setup and preceding context](select-columns-aliases-and-from.md) · [Previous phase](select-case-and-join-types.md) · [Continue](select-order-limits-and-scalar-subqueries.md)

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
