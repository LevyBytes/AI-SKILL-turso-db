# Explain plan comparisons joins and subqueries

[Context index](explain-insert-and-replace-reference.md) · [Setup and preceding context](explain-bytecode-and-query-plan-columns.md)

### Common Detail Messages

| Detail Pattern | Meaning |
| --- | --- |
| `SCAN table` | Table-wide scan (no index used). |
| `SEARCH table USING INDEX idx (col=?)` | Lookup through an index on the named column. |
| `SEARCH table USING INTEGER PRIMARY KEY (rowid=?)` | Direct rowid lookup |
| `USE TEMP B-TREE FOR ORDER BY` | Sorting will use a temporary B-tree. |
| `USE TEMP B-TREE FOR DISTINCT` | Deduplication will use a temporary B-tree. |
| `COMPOUND SUBQUERY` | Signals a set operation: UNION, INTERSECT, or EXCEPT. |
| `CORRELATED SCALAR SUBQUERY` | Correlated subquery producing one scalar value. |

### Examples

### Comparing Query Plans

```
CREATE TABLE orders (
    id INTEGER PRIMARY KEY,
    customer_id INTEGER,
    total REAL
);

-- Without index: full table scan
EXPLAIN QUERY PLAN SELECT * FROM orders WHERE customer_id = 42;
-- SCAN orders

-- After creating an index
CREATE INDEX idx_customer ON orders(customer_id);
EXPLAIN QUERY PLAN SELECT * FROM orders WHERE customer_id = 42;
-- SEARCH orders USING INDEX idx_customer (customer_id=?)

```

### Join Order

```
EXPLAIN QUERY PLAN
SELECT o.id, c.name
FROM orders o
INNER JOIN customers c ON c.id = o.customer_id
WHERE c.name = 'Alice';
-- SCAN orders AS o
-- SEARCH customers AS c USING INTEGER PRIMARY KEY (rowid=?)

```

### Subqueries

```
EXPLAIN QUERY PLAN
SELECT * FROM orders
WHERE customer_id IN (SELECT id FROM customers WHERE active = 1);
-- SCAN orders
-- LIST SUBQUERY
--   SCAN customers

```

### See Also

- [ANALYZE](https://docs.turso.tech/sql-reference/statements/analyze) for gathering statistics used to improve query plans.
- [CREATE INDEX](https://docs.turso.tech/sql-reference/statements/create-index) for defining indexes that make queries faster.
