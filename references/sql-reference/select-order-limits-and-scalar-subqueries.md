# Select order limits and scalar subqueries

[Context index](select-and-transaction-reference.md) · [Setup and preceding context](select-columns-aliases-and-from.md) · [Previous phase](select-join-using-grouping-and-distinct.md) · [Continue](select-exists-cte-and-window-queries.md)

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
-- NULLs at the end (default for ASC is NULLS FIRST, for DESC is NULLS LAST)
SELECT * FROM employees ORDER BY manager_id ASC NULLS LAST;

-- NULLs at the beginning
SELECT * FROM employees ORDER BY manager_id DESC NULLS FIRST;

```

| Direction | Default NULL Placement |
| --- | --- |
| ASC | NULLS FIRST |
| DESC | NULLS LAST |

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
