# Extension generated number series

[Context index](expressions-and-extensions-workflows.md) · [Setup and preceding context](extension-loading-uuid-and-regexp.md) · [Previous phase](extension-fulltext-csv-and-percentiles.md)

### generate_series

 The `generate_series` table-valued function produces an integer sequence.

```
SELECT value FROM generate_series(start, stop);
SELECT value FROM generate_series(start, stop, step);

```

| Parameter | Type | Description |
| --- | --- | --- |
| start | INTEGER | Starting value (inclusive) |
| stop | INTEGER | Ending value (inclusive) |
| step | INTEGER | Step increment (default: 1) |

```
SELECT value FROM generate_series(1, 5);
-- 1, 2, 3, 4, 5

SELECT value FROM generate_series(0, 10, 2);
-- 0, 2, 4, 6, 8, 10

SELECT value FROM generate_series(10, 1, -3);
-- 10, 7, 4, 1

```

### See Also

- [Vector Functions](https://docs.turso.tech/sql-reference/functions/vector) for the detailed vector function reference
- [FTS Functions](https://docs.turso.tech/sql-reference/functions/fts) for the full-text search function reference
- [Aggregate Functions](https://docs.turso.tech/sql-reference/functions/aggregate) for percentile functions used with GROUP BY
- [Compatibility](https://docs.turso.tech/sql-reference/compatibility) for extension support status
