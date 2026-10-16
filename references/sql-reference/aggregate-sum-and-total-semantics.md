# Aggregate sum and total semantics

[Context index](aggregate-function-reference.md) · [Setup and preceding context](aggregate-function-reference-and-average.md) · [Previous phase](aggregate-string-joining-minimum-and-maximum.md) · [Continue](aggregate-groups-having-distinct-and-windows.md)

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

 `sum(X)` produces an integer result if all inputs are integers and the value fits in a 64-bit signed integer. If an all-integer accumulation overflows, sum() raises an integer overflow error. Choose `total(X)` when a floating-point result is required in all cases.
