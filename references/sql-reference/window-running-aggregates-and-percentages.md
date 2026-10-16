# Window running aggregates and percentages

[Context index](vector-and-window-function-reference.md) · [Setup and preceding context](window-syntax-frames-and-partitioning.md) · [Previous phase](window-ordering-and-named-definitions.md) · [Continue](window-function-limitations.md)

### Running Total

```
SELECT
    order_date,
    amount,
    SUM(amount) OVER (ORDER BY order_date) AS running_total
FROM orders;

```

### Count per Group

```
-- Show each employee alongside their department headcount
SELECT
    name,
    department,
    COUNT(*) OVER (PARTITION BY department) AS dept_size
FROM employees
ORDER BY department, name;

```

### Running Average

```
SELECT
    date,
    temperature,
    AVG(temperature) OVER (ORDER BY date) AS running_avg_temp
FROM weather_readings
ORDER BY date;

```

### Percentage of Total

```
SELECT
    product_name,
    revenue,
    ROUND(100.0 * revenue / SUM(revenue) OVER (), 2) AS pct_of_total
FROM products
ORDER BY revenue DESC;

```

### Multiple Window Functions in One Query

```
SELECT
    department,
    name,
    salary,
    MIN(salary) OVER (PARTITION BY department) AS dept_min,
    MAX(salary) OVER (PARTITION BY department) AS dept_max,
    AVG(salary) OVER (PARTITION BY department) AS dept_avg,
    salary - AVG(salary) OVER (PARTITION BY department) AS diff_from_avg
FROM employees
ORDER BY department, salary DESC;

```

### Group Concatenation over a Window

```
SELECT
    department,
    name,
    GROUP_CONCAT(name, ', ') OVER (PARTITION BY department ORDER BY name) AS names_so_far
FROM employees;

```
