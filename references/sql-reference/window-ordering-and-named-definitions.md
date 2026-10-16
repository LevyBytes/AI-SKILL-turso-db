# Window ordering and named definitions

[Context index](vector-and-window-function-reference.md) · [Setup and preceding context](window-syntax-frames-and-partitioning.md) · [Continue](window-running-aggregates-and-percentages.md)

### ORDER BY

 ORDER BY inside OVER sets the row order for each partition. Together with the default frame, RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW, it yields running calculations.

```
SELECT
    name,
    salary,
    SUM(salary) OVER (ORDER BY salary) AS running_total
FROM employees;

```

| name | salary | running_total |
| --- | --- | --- |
| Eve | 60000 | 60000 |
| Dave | 70000 | 130000 |
| Carol | 75000 | 205000 |
| Bob | 85000 | 290000 |
| Alice | 90000 | 380000 |

### PARTITION BY with ORDER BY

 Combine both clauses to compute running values within each group:

```
SELECT
    department,
    name,
    salary,
    SUM(salary) OVER (
        PARTITION BY department
        ORDER BY salary
    ) AS dept_running_total
FROM employees;

```

| department | name | salary | dept_running_total |
| --- | --- | --- | --- |
| Engineering | Carol | 75000 | 75000 |
| Engineering | Bob | 85000 | 160000 |
| Engineering | Alice | 90000 | 250000 |
| Marketing | Eve | 60000 | 60000 |
| Marketing | Dave | 70000 | 130000 |

### Named Windows

 The WINDOW clause creates a named window specification that several window functions in one query can reuse. This keeps the same OVER definition from being written multiple times.

```
SELECT
    department,
    name,
    salary,
    SUM(salary) OVER w AS running_total,
    AVG(salary) OVER w AS running_avg,
    COUNT(*) OVER w AS running_count
FROM employees
WINDOW w AS (PARTITION BY department ORDER BY salary)
ORDER BY department, salary;

```

 A query can declare more than one named window:

```
SELECT
    department,
    name,
    salary,
    SUM(salary) OVER dept AS dept_total,
    SUM(salary) OVER company AS company_total
FROM employees
WINDOW
    dept AS (PARTITION BY department),
    company AS ()
ORDER BY department, name;

```

### Examples
