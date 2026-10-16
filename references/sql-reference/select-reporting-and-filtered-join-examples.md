# Select reporting and filtered join examples

[Context index](select-and-transaction-reference.md) · [Setup and preceding context](select-columns-aliases-and-from.md) · [Previous phase](select-named-windows-and-set-operations.md)

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
