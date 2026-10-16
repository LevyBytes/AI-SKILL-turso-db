# Materialized view creation and incremental updates

[Context index](indexes-tables-and-materialized-views.md) · [Continue](materialized-view-filtered-summary.md)

## CREATE MATERIALIZED VIEW

 **Turso Extension**: Materialized views are specific to Turso and are absent from SQLite. This experimental feature must be [enabled before use](https://docs.turso.tech/sql-reference/experimental-features). A materialized view persists precomputed query results on disk and keeps them current automatically through Incremental View Maintenance (IVM). Standard views differ because reading a materialized view does not run the underlying SELECT again.

### Syntax

```
CREATE MATERIALIZED VIEW [IF NOT EXISTS] view-name AS select-statement;

```

### Parameters

| Parameter | Description |
| --- | --- |
| `IF NOT EXISTS` | Avoids an error when a materialized view using that name already exists. If the view is already present, the statement does nothing. |
| `view-name` | A database-unique name for the materialized view. |
| `select-statement` | The SELECT query used to define the materialized view's contents. |

### How Incremental View Maintenance Works

 When data changes in a table that a materialized view depends on, Turso updates the materialized view incrementally inside that same transaction. No manual `REFRESH` is required. The materialized view stays consistent with its base tables.
- Materialized-view changes commit or roll back along with the related base-table changes.
- Aggregations such as `COUNT`, `SUM`, and `AVG` are maintained efficiently; Turso avoids recomputing the complete result set for every change.

### Current Limitations

- Some SQL functions are unsupported in materialized view definitions.
- A materialized view is not allowed to reference another view or materialized view.
- Materialized views do not support the `TEMPORARY` modifier.

### Examples

### Precomputed Aggregations

```
CREATE TABLE orders (
    id INTEGER PRIMARY KEY,
    customer_id INTEGER,
    amount REAL,
    created_at TEXT
);

-- Precompute per-customer totals
CREATE MATERIALIZED VIEW customer_totals AS
SELECT
    customer_id,
    COUNT(*) AS order_count,
    SUM(amount) AS total_spent
FROM orders
GROUP BY customer_id;

-- Fast read — no aggregation at query time
SELECT * FROM customer_totals WHERE total_spent > 1000;

```

### Automatic Updates

```
-- When new orders are inserted, customer_totals updates automatically
INSERT INTO orders VALUES (1, 100, 59.99, '2025-01-15');
INSERT INTO orders VALUES (2, 100, 24.50, '2025-01-16');

-- Reflects both inserts without a manual refresh
SELECT * FROM customer_totals WHERE customer_id = 100;
-- customer_id: 100, order_count: 2, total_spent: 84.49

```
