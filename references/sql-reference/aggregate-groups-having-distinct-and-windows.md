# Aggregate groups having distinct and windows

[Context index](aggregate-function-reference.md) · [Setup and preceding context](aggregate-function-reference-and-average.md) · [Previous phase](aggregate-sum-and-total-semantics.md) · [Continue](aggregate-arrays-and-standard-deviation.md)

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
