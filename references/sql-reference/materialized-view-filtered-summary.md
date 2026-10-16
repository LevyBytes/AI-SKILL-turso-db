# Materialized view filtered summary

[Context index](indexes-tables-and-materialized-views.md) · [Setup and preceding context](materialized-view-creation-and-incremental-updates.md)

### Filtered Summary

```
CREATE MATERIALIZED VIEW recent_activity AS
SELECT
    customer_id,
    COUNT(*) AS recent_orders
FROM orders
WHERE created_at >= '2025-01-01'
GROUP BY customer_id;

```

### See Also

- [CREATE VIEW](https://docs.turso.tech/sql-reference/statements/create-view) for regular views that are not materialized
- [DROP VIEW](https://docs.turso.tech/sql-reference/statements/drop-view) to remove materialized views
- [SELECT](https://docs.turso.tech/sql-reference/statements/select) for view-definition query syntax
