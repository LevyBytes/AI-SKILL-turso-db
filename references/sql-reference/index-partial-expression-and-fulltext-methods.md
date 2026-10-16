# Index partial expression and fulltext methods

[Context index](indexes-tables-and-materialized-views.md) · [Setup and preceding context](index-syntax-columns-and-uniqueness.md) · [Continue](index-lookup-business-rule-and-filter-examples.md)

### Partial Indexes

 A partial index stores entries only for rows satisfying the `WHERE` clause. Compared with a full index, it is smaller and works better for queries that always apply the same filter condition.

```
-- Index only active orders
CREATE INDEX idx_active_orders ON orders (customer_id)
    WHERE status = 'active';

-- The query planner uses this index when the WHERE clause matches
SELECT * FROM orders WHERE status = 'active' AND customer_id = 42;

```

 In a partial index, the `WHERE` clause may use any table column plus operators, literal values, and built-in functions. Subqueries cannot be used.

### Expression Indexes

 An expression index indexes a computed expression result instead of storing a direct column value. Choose expression indexes for queries that often filter or sort on a computed value.

```
-- Index on lowercase email for case-insensitive lookups
CREATE INDEX idx_users_email_lower ON users (lower(email));

-- The query planner can use this index for:
SELECT * FROM users WHERE lower(email) = 'alice@example.com';

```

```
-- Index on an arithmetic expression
CREATE INDEX idx_items_total ON items (quantity * unit_price);

```

 Every indexed expression has to be deterministic and may refer only to columns from the indexed table. Aggregate functions and subqueries cannot appear.

### Custom Index Methods

 **Turso Extension**: Custom index methods add indexing options other than B-trees. This experimental feature must be [enabled before use](https://docs.turso.tech/sql-reference/experimental-features). Turso lets a `USING` clause select an alternate index method.

```
CREATE INDEX index-name ON table-name USING method-name (columns...);

```

### Full-Text Search with `USING fts`

 The `fts` index method builds a Tantivy-backed full-text search index. Configure FTS tokenizers with the `WITH` clause.

```
-- Basic FTS index
CREATE INDEX idx_articles_search ON articles USING fts (title, body);

-- FTS index with custom tokenizers
CREATE INDEX idx_articles_search ON articles USING fts (
    title WITH tokenizer=simple,
    body WITH tokenizer=ngram
);

```

 After creating an FTS index, query it with the `fts_match()` function:

```
SELECT * FROM articles WHERE fts_match(title, body, 'database performance');

```

### Examples
