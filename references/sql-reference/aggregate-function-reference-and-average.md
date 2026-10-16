# Aggregate function reference and average

[Context index](aggregate-function-reference.md) · [Continue](aggregate-count-and-group-concatenation.md)

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

 `avg(X)` leaves NULL values out of both the summation and the divisor. In the example above, the South region has two amount entries that are not NULL (150 + 175 = 325, while the NULL row is omitted), so only the values that are not NULL contribute to the average.
