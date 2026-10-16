# Aggregate arrays and standard deviation

[Context index](aggregate-function-reference.md) · [Setup and preceding context](aggregate-function-reference-and-average.md) · [Previous phase](aggregate-groups-having-distinct-and-windows.md) · [Continue](aggregate-medians-and-percentiles.md)

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
