# Window syntax frames and partitioning

[Context index](vector-and-window-function-reference.md) · [Continue](window-ordering-and-named-definitions.md)

## Window Functions

 A window function returns a value for each input row while also looking at rows in that row's window. With GROUP BY aggregates, rows are grouped down into fewer output rows; with a window calculation, the original rows remain visible. Turso currently lets aggregate functions run through OVER with the default frame rules. It does not yet support explicit ROWS, RANGE, or GROUPS frame bounds, and it also does not provide dedicated window functions such as row_number, rank, dense_rank, ntile, lag, lead, first_value, last_value, and nth_value.

### Syntax

```
aggregate_function(expression) OVER (
    [PARTITION BY expression [, ...]]
    [ORDER BY expression [ASC | DESC] [, ...]]
)

```

| Clause | Description |
| --- | --- |
| `aggregate_function` | Any supported aggregate function: `count`, `sum`, `avg`, `min`, `max`, `total`, `group_concat` |
| `OVER (...)` | Specifies the window used by the function |
| `PARTITION BY` | Splits the result set into partitions. The function runs separately inside each partition. If this clause is left out, the full result set is treated as one partition |
| `ORDER BY` | Specifies row ordering within each partition. This ordering controls which rows belong to the frame for each calculation |

### Default Frame

 If ORDER BY is present, this is the default frame:

```
RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW

```

 The function therefore evaluates rows from the beginning of the partition through the current row, including peers with matching ORDER BY values because the frame mode is RANGE. Without ORDER BY, the default frame spans the full partition.

### Supported Aggregate Functions as Window Functions

 Adding an OVER clause lets any aggregate function operate as a window function.

| Function | Description |
| --- | --- |
| `count(*)` | Count of rows in the frame |
| `count(expression)` | Count of non-NULL entries in the frame |
| `sum(expression)` | Total of non-NULL entries in the frame |
| `avg(expression)` | Mean of non-NULL entries in the frame |
| `min(expression)` | Minimum value in the frame |
| `max(expression)` | Maximum value in the frame |
| `total(expression)` | Total as REAL, returning 0.0 for empty frames rather than NULL |
| `group_concat(expression, separator)` | Frame values joined together |

### PARTITION BY

 PARTITION BY separates rows into groups. For each partition, the window function starts over and computes its result independently.

```
SELECT
    department,
    name,
    salary,
    SUM(salary) OVER (PARTITION BY department) AS dept_total
FROM employees;

```

| department | name | salary | dept_total |
| --- | --- | --- | --- |
| Engineering | Alice | 90000 | 250000 |
| Engineering | Bob | 85000 | 250000 |
| Engineering | Carol | 75000 | 250000 |
| Marketing | Dave | 70000 | 130000 |
| Marketing | Eve | 60000 | 130000 |

 If PARTITION BY is absent, the function uses the full result set as a single partition:

```
SELECT
    name,
    salary,
    SUM(salary) OVER () AS company_total
FROM employees;

```
