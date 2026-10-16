# Date range filtering and elapsed time

[Context index](date-and-time-function-reference.md) · [Setup and preceding context](date-and-time-construction.md) · [Previous phase](date-arithmetic-age-and-grouping.md)

### Filter by Date Range

```
-- Records from the last 30 days
SELECT * FROM logs
WHERE timestamp >= datetime('now', '-30 days');

-- Records from a specific month
SELECT * FROM orders
WHERE order_date >= '2025-06-01'
  AND order_date < '2025-07-01';

-- Records from this year
SELECT * FROM sales
WHERE date(sale_date) >= date('now', 'start of year')
  AND date(sale_date) < date('now', 'start of year', '+1 year');

```

### Compute Elapsed Time

```
-- Days between two dates
SELECT julianday('2025-12-25') - julianday('now') AS days_until_christmas;

-- Seconds between two timestamps
SELECT unixepoch('2025-06-15 18:00:00') - unixepoch('2025-06-15 14:30:00') AS seconds_elapsed;
-- 12600

```

### See Also

- [Expressions](https://docs.turso.tech/sql-reference/expressions) explains how to use functions inside expressions
- [Data Types](https://docs.turso.tech/sql-reference/data-types) covers the TEXT, REAL, and INTEGER storage classes
- [Scalar Functions](https://docs.turso.tech/sql-reference/functions/scalar) lists additional built-in functions
