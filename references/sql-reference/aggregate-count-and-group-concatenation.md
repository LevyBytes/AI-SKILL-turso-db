# Aggregate count and group concatenation

[Context index](aggregate-function-reference.md) · [Setup and preceding context](aggregate-function-reference-and-average.md) · [Continue](aggregate-string-joining-minimum-and-maximum.md)

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
| North | Widget \| Gadget \| Widget |
| South | Widget \| Gadget \| Widget |
