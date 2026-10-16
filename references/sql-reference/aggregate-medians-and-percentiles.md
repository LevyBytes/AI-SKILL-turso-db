# Aggregate medians and percentiles

[Context index](aggregate-function-reference.md) · [Setup and preceding context](aggregate-function-reference-and-average.md) · [Previous phase](aggregate-arrays-and-standard-deviation.md)

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
