# Aggregate string joining minimum and maximum

[Context index](aggregate-function-reference.md) · [Setup and preceding context](aggregate-function-reference-and-average.md) · [Previous phase](aggregate-count-and-group-concatenation.md) · [Continue](aggregate-sum-and-total-semantics.md)

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
