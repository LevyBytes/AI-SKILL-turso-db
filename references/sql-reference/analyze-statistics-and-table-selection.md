# Analyze statistics and table selection

[Context index](alter-analyze-attach-and-domain-reference.md)

## ANALYZE

 The ANALYZE statement builds statistics for table and index contents. The query optimizer relies on those statistics to select stronger query plans, especially for index choice and join ordering.

### Syntax

```
ANALYZE;
ANALYZE schema-name;
ANALYZE [schema-name.]table-or-index-name;

```

### Description

 The ANALYZE statement measures how values are distributed in indexes and records the results in the `sqlite_stat1` table and, optionally, `sqlite_stat4`. The query optimizer consults this data when deciding:
- Which index a query should use
- The table processing order for a join
- Whether an index or a full table scan is preferable

### Forms

| Form | Description |
| --- | --- |
| `ANALYZE` | Run analysis on every table and index across all attached databases |
| `ANALYZE schema-name` | Run analysis on every table and index in the specified database |
| `ANALYZE table-name` | Run analysis on all indexes for the specified table |
| `ANALYZE index-name` | Analyze the named index |

### Examples

### Analyze All Tables

```
-- Collect statistics for the entire database
ANALYZE;

```

### Analyze a Specific Table

```
CREATE TABLE orders (
    id INTEGER PRIMARY KEY,
    customer_id INTEGER,
    product_id INTEGER,
    amount REAL
);

CREATE INDEX idx_customer ON orders(customer_id);
CREATE INDEX idx_product ON orders(product_id);

-- Collect statistics for the orders table and its indexes
ANALYZE orders;

```

### Viewing Statistics

 Statistics are written to the `sqlite_stat1` table:

```
SELECT * FROM sqlite_stat1;
-- tbl     | idx           | stat
-- orders  | idx_customer  | 1000 50
-- orders  | idx_product   | 1000 10

```

 The `stat` column stores integers separated by spaces. Its first integer gives the table's total row count. Each following integer estimates the average rows sharing the same value across the index's leftmost N columns.

### See Also

- [CREATE INDEX](https://docs.turso.tech/sql-reference/statements/create-index) when creating indexes
- [EXPLAIN](https://docs.turso.tech/sql-reference/statements/explain) when inspecting query plans
