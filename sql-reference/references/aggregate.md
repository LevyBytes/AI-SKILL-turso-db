# Aggregate

> Sql Reference reference. Original prose; identifiers preserved verbatim.

### Aggregate Functions

 Aggregate functions reduce multiple input rows to one computed value. They commonly appear in SELECT statements that use `GROUP BY`, and they can also summarize the full row set when `GROUP BY` is omitted. If a SELECT mixes aggregate output with non-aggregate columns and has no `GROUP BY`, it produces one row. Standard aggregates skip NULL values, except for `count(*)`. When all supplied values are NULL, the result is NULL except for `count()`, which yields 0, and `total()`, which yields 0.0.

### Function Reference

| Function | Return Type | Description |
| --- | --- | --- |
| `array_agg(X)` | BLOB (array) | Builds an array from every X value (Turso extension) |
| `avg(X)` | REAL | Mean computed from X values that are not NULL |
| `count(X)` | INTEGER | Number of rows for which X is not NULL |
| `count(*)` | INTEGER | Total row count within the group |
| `group_concat(X)` | TEXT | Joins X values that are not NULL, using commas as separators |
| `group_concat(X, Y)` | TEXT | Joins X values that are not NULL, using Y as the separator |
| `string_agg(X, Y)` | TEXT | Alias for `group_concat(X, Y)` |
| `max(X)` | same as X | Largest X value that is not NULL |
| `min(X)` | same as X | Smallest X value that is not NULL |
| `sum(X)` | INTEGER or REAL | Adds X values that are not NULL. Produces NULL when every value is NULL |
| `total(X)` | REAL | Adds X values that are not NULL. The result is always REAL, with 0.0 returned when every value is NULL |

### Detailed Descriptions and Examples

 The examples that follow are based on this table:

```
CREATE TABLE sales (
    id INTEGER PRIMARY KEY,
    region TEXT,
    product TEXT,
    amount REAL,
    quantity INTEGER
);

INSERT INTO sales VALUES
    (1, 'North', 'Widget', 100.00, 5),
    (2, 'North', 'Gadget', 250.00, 2),
    (3, 'South', 'Widget', 150.00, 8),
    (4, 'South', 'Gadget', NULL, 3),
    (5, 'North', 'Widget', 200.00, NULL),
    (6, 'South', 'Widget', 175.00, 6);

```

### avg(X)

```
avg(X)

```

 Computes the mean of the X values that are not NULL and returns it as a REAL floating-point value. The result is NULL when every value is NULL.
| Parameter | Type | Description |
| --- | --- | --- |
| X | any numeric | The expression to average |

 **Return type:** REAL

```
SELECT avg(amount) FROM sales;
-- 175.0 (sum of non-NULL amounts / count of non-NULL amounts = 875.0 / 5)

SELECT region, avg(amount) AS avg_amount
FROM sales
GROUP BY region;

```

| region | avg_amount |
| --- | --- |
| North | 183.333333333333 |
| South | 162.5 |

 `avg(X)` leaves NULL values out of both the summation and the divisor. In the example above, the South region has three amount entries that are not NULL (150 + 175 = 325, while the NULL row is omitted), so only the values that are not NULL contribute to the average.

### count(X) and count(*)

```
count(X)
count(*)

```

 `count(X)` reports how many rows have X not NULL. `count(*)` reports every row in the group, including rows that contain NULL values.
| Parameter | Type | Description |
| --- | --- | --- |
| X | any | Expression used to count values that are not NULL |
| `*` | special | Counts every row without considering NULL |

 **Return type:** INTEGER

```
SELECT count(*) FROM sales;
-- 6 (all rows)

SELECT count(amount) FROM sales;
-- 5 (excludes the row where amount is NULL)

SELECT region, count(*) AS total_rows, count(amount) AS rows_with_amount
FROM sales
GROUP BY region;

```

| region | total_rows | rows_with_amount |
| --- | --- | --- |
| North | 3 | 3 |
| South | 3 | 2 |

### group_concat(X) and group_concat(X, Y)

```
group_concat(X)
group_concat(X, Y)

```

 Merges all X values that are not NULL into one string. If no separator is specified, a comma (`,`) is used. Supplying Y makes Y the separator instead.
| Parameter | Type | Description |
| --- | --- | --- |
| X | any | Expression providing the values to join |
| Y | TEXT | Separator string (default: `","`) |

 **Return type:** TEXT

```
SELECT group_concat(product) FROM sales;
-- 'Widget,Gadget,Widget,Gadget,Widget,Widget'

SELECT group_concat(DISTINCT product) FROM sales;
-- 'Widget,Gadget'

SELECT region, group_concat(product, ' | ') AS products
FROM sales
GROUP BY region;

```

| region | products |
| --- | --- |
| North | Widget | Gadget | Widget |
| South | Widget | Gadget | Widget |

### string_agg(X, Y)

```
string_agg(X, Y)

```

 Alternative name for `group_concat(X, Y)`, included for PostgreSQL compatibility.
| Parameter | Type | Description |
| --- | --- | --- |
| X | any | Expression providing the values to join |
| Y | TEXT | Separator string |

 **Return type:** TEXT

```
SELECT region, string_agg(product, ', ') AS products
FROM sales
GROUP BY region;

```

| region | products |
| --- | --- |
| North | Widget, Gadget, Widget |
| South | Widget, Gadget, Widget |

### max(X) and min(X)

```
max(X)
min(X)

```

 `max(X)` gives the largest X value that is not NULL. `min(X)` gives the smallest X value that is not NULL. Comparisons follow the usual SQLite comparison rules. If every value is NULL, the result is NULL.
| Parameter | Type | Description |
| --- | --- | --- |
| X | any | Expression inspected for its largest or smallest value |

 **Return type:** Matches the input type

```
SELECT max(amount), min(amount) FROM sales;
-- max: 250.0, min: 100.0

SELECT region, max(amount) AS highest, min(amount) AS lowest
FROM sales
GROUP BY region;

```

| region | highest | lowest |
| --- | --- | --- |
| North | 250.0 | 100.0 |
| South | 175.0 | 150.0 |

 With one argument in an aggregate context, `max(X)` or `min(X)` behaves as an aggregate. With two or more arguments, such as `max(a, b, c)`, it behaves as a [scalar function](https://docs.turso.tech/sql-reference/functions/scalar) and returns the largest supplied argument.

### sum(X) and total(X)

```
sum(X)
total(X)

```

 Both functions add every X value that is not NULL. Their differences are the returned type and the result produced when every value is NULL.
| Parameter | Type | Description |
| --- | --- | --- |
| X | any numeric | The expression to sum |

 **Return type:**
- `sum(X)`: INTEGER when each input that is not NULL is an integer and no overflow happens; otherwise REAL. Produces NULL if every value is NULL.
- `total(X)`: Always REAL. Produces 0.0 if every value is NULL.

```
SELECT sum(amount), total(amount) FROM sales;
-- sum: 875.0, total: 875.0

SELECT sum(quantity), total(quantity) FROM sales;
-- sum: 24, total: 24.0

```

#### Difference between sum() and total()

 The main behavioral difference shows up when the group contains only NULL values:

```
CREATE TABLE empty_amounts (val REAL);
INSERT INTO empty_amounts VALUES (NULL), (NULL);

SELECT sum(val) FROM empty_amounts;
-- NULL

SELECT total(val) FROM empty_amounts;
-- 0.0

```

 Because of that behavior, `total()` is useful when empty groups or groups containing only NULL should still produce a numeric value:

```
SELECT
    region,
    sum(amount) AS sum_amount,
    total(amount) AS total_amount
FROM sales
GROUP BY region;

```

| region | sum_amount | total_amount |
| --- | --- | --- |
| North | 550.0 | 550.0 |
| South | 325.0 | 325.0 |

```
-- total() is useful in arithmetic to avoid NULL propagation
SELECT total(amount) * 1.1 AS with_tax FROM sales;
-- 962.5

-- sum() with all NULLs would produce NULL, making the multiplication NULL too

```

 `sum(X)` produces an integer result if all inputs are integers and the value fits in a 64-bit signed integer. On overflow, the result changes to REAL automatically. Choose `total(X)` when a floating-point result is required in all cases.

### Using Aggregates with GROUP BY

 `GROUP BY` divides rows into groups, and each aggregate is evaluated separately within each group.

```
SELECT
    region,
    product,
    count(*) AS order_count,
    sum(amount) AS total_sales,
    avg(amount) AS avg_sale,
    min(amount) AS min_sale,
    max(amount) AS max_sale
FROM sales
GROUP BY region, product;

```

| region | product | order_count | total_sales | avg_sale | min_sale | max_sale |
| --- | --- | --- | --- | --- | --- | --- |
| North | Gadget | 1 | 250.0 | 250.0 | 250.0 | 250.0 |
| North | Widget | 2 | 300.0 | 150.0 | 100.0 | 200.0 |
| South | Gadget | 1 | NULL | NULL | NULL | NULL |
| South | Widget | 2 | 325.0 | 162.5 | 150.0 | 175.0 |

### Filtering Groups with HAVING

 `HAVING` removes groups after aggregates have been calculated. Use `WHERE` for row filtering before aggregation, and `HAVING` for group filtering afterward.

```
SELECT region, sum(amount) AS total_sales
FROM sales
WHERE amount IS NOT NULL
GROUP BY region
HAVING sum(amount) > 400;

```

| region | total_sales |
| --- | --- |
| North | 550.0 |

### Aggregates with DISTINCT

 `DISTINCT` makes the aggregate operate only on unique values that are not NULL.

```
SELECT count(product) FROM sales;
-- 6 (all non-NULL product values)

SELECT count(DISTINCT product) FROM sales;
-- 2 (only 'Widget' and 'Gadget')

SELECT group_concat(DISTINCT product) FROM sales;
-- 'Widget,Gadget'

```

### Aggregates as Window Functions

 Standard aggregate functions may also be used as window functions. With an `OVER` clause, they can calculate running or partition-level results while keeping the original rows.

```
SELECT
    id,
    region,
    amount,
    sum(amount) OVER (PARTITION BY region ORDER BY id) AS running_total,
    count(*) OVER (PARTITION BY region) AS region_count
FROM sales
ORDER BY region, id;

```

| id | region | amount | running_total | region_count |
| --- | --- | --- | --- | --- |
| 1 | North | 100.0 | 100.0 | 3 |
| 2 | North | 250.0 | 350.0 | 3 |
| 5 | North | 200.0 | 550.0 | 3 |
| 3 | South | 150.0 | 150.0 | 3 |
| 4 | South | NULL | 150.0 | 3 |
| 6 | South | 175.0 | 325.0 | 3 |

### Turso Extension: array_agg(X)

 `array_agg(X)` is provided by Turso and is outside standard SQLite. Turso enables it by default, so no extra extension loading is needed.

```
array_agg(X)

```

 Builds an array containing every X value, including NULLs. The result is NULL for an empty group.
| Parameter | Type | Description |
| --- | --- | --- |
| X | any | Expression that supplies the values for the array |

 **Return type:** BLOB (array)

```
SELECT array_agg(name) FROM users;
-- ["Alice","Bob","Charlie"]

SELECT department, array_agg(name) FROM employees GROUP BY department;

```

| department | array_agg(name) |
| --- | --- |
| Engineering | [“Alice”,“Bob”] |
| Sales | [“Charlie”,“Diana”,“Eve”] |

```
-- Control ordering with a subquery
SELECT array_agg(name) FROM (SELECT name FROM users ORDER BY name);

-- Combine with array_length
SELECT array_length(array_agg(name)) FROM users;
-- 3

```

 For additional array helpers, refer to [Array Functions](https://docs.turso.tech/sql-reference/functions/array).

### Turso Extension: stddev(X)

 `stddev(X)` is provided by Turso and is outside standard SQLite. Turso enables it by default, so no extra extension loading is needed.

```
stddev(X)

```

 Computes the population standard deviation across X values that are not NULL. Produces NULL when there are no values that are not NULL.
| Parameter | Type | Description |
| --- | --- | --- |
| X | any numeric | Expression used for the standard deviation calculation |

 **Return type:** REAL

```
SELECT stddev(amount) FROM sales;

SELECT region, avg(amount) AS mean, stddev(amount) AS std_dev
FROM sales
GROUP BY region;

```

### Extension Aggregate Functions

 The aggregate functions below come from the **percentile** extension and must be loaded before use. They require the `percentile` extension, which can be loaded with `SELECT load_extension('./percentile');` or configured to auto-load through the connection.

### median(X)

```
median(X)

```

 Gives the median, or middle value, among X values that are not NULL.
| Parameter | Type | Description |
| --- | --- | --- |
| X | any numeric | Expression whose median should be found |

 **Return type:** REAL

```
SELECT median(amount) FROM sales;
-- 175.0

SELECT region, median(amount) AS median_amount
FROM sales
GROUP BY region;

```

### percentile(Y, P)

```
percentile(Y, P)

```

 Computes the P-th percentile across Y values that are not NULL. The calculation uses linear interpolation between neighboring values.
| Parameter | Type | Description |
| --- | --- | --- |
| Y | any numeric | Values used as the percentile input |
| P | REAL | The percentile to compute (0.0 to 100.0) |

 **Return type:** REAL

```
SELECT percentile(amount, 50) FROM sales;   -- 50th percentile (median)
SELECT percentile(amount, 90) FROM sales;   -- 90th percentile
SELECT percentile(amount, 25) FROM sales;   -- 25th percentile (Q1)

```

### percentile_cont(Y, P) and percentile_disc(Y, P)

```
percentile_cont(Y, P)
percentile_disc(Y, P)

```

 These are SQL-standard percentile functions. `percentile_cont` uses a continuous interpolated distribution, whereas `percentile_disc` chooses the nearest discrete input value.
| Parameter | Type | Description |
| --- | --- | --- |
| Y | any numeric | Values used as the percentile input |
| P | REAL | The percentile fraction (0.0 to 1.0) |

 **Return type:** REAL

```
-- Continuous percentile (interpolates between values)
SELECT percentile_cont(amount, 0.5) FROM sales;

-- Discrete percentile (returns an actual input value)
SELECT percentile_disc(amount, 0.5) FROM sales;

```

 The P range differs: `percentile(Y, P)` accepts P from 0 to 100, while `percentile_cont` and `percentile_disc` accept P from 0.0 to 1.0.

### See Also

- [Scalar Functions](https://docs.turso.tech/sql-reference/functions/scalar) for functions evaluated per row
- [Expressions](https://docs.turso.tech/sql-reference/expressions) for operator syntax, CAST, CASE, and subqueries
- [SELECT](https://docs.turso.tech/sql-reference/statements/select) for GROUP BY, HAVING, and window function syntax
