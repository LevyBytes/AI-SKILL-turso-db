# Index syntax columns and uniqueness

[Context index](indexes-tables-and-materialized-views.md) · [Continue](index-partial-expression-and-fulltext-methods.md)

## CREATE INDEX

 Add an index to a table so lookups, joins, and ordered results can run faster.

### Syntax

```
CREATE [UNIQUE] INDEX [IF NOT EXISTS] [schema-name.]index-name
    ON table-name (column-or-expr [ASC|DESC], ...)
    [WHERE filter-expression];

```

### Description

 `CREATE INDEX` creates an index over one or more table columns or expressions. Turso stores B-tree indexes using SQLite's format. When an index can make a query faster, the query planner chooses it automatically; your SQL statements do not have to name the index.

### Parameters

| Parameter | Description |
| --- | --- |
| `UNIQUE` | Applies a uniqueness constraint. Turso refuses any INSERT or UPDATE that would introduce duplicate values in the indexed columns. |
| `IF NOT EXISTS` | Avoids an error when an index using that name is already defined. If the index is already present, the statement does nothing. |
| `schema-name` | Names the attached database that contains the table. If omitted, the main database is used by default. |
| `index-name` | A database-unique name assigned to the index. |
| `table-name` | The table to index. |
| `column-or-expr` | The column name or expression to add to the index. Separate multiple entries with commas. |
| `ASC` / `DESC` | The indexed column's sort direction. `ASC` is the default. |
| `WHERE filter-expression` | Optional filtering logic for a partial index. The index contains only rows that match the expression. |

### Column Indexes

 The usual pattern indexes one or more named columns.

```
-- Single-column index
CREATE INDEX idx_users_email ON users (email);

-- Multi-column (composite) index
CREATE INDEX idx_orders_customer_date ON orders (customer_id, order_date DESC);

```

 Use a composite index when queries filter or order results by more than one column. Column order is significant: an index on `(a, b)` can help queries constrained by `a` alone, but it will not help queries constrained only by `b`.

### UNIQUE Indexes

A `UNIQUE` index requires each indexed column-value combination to appear in no more than one row. Because NULL values are treated as distinct, several rows may have NULL in the indexed columns of a `UNIQUE` index.

```
CREATE UNIQUE INDEX idx_users_email_unique ON users (email);

-- This succeeds:
INSERT INTO users (email) VALUES ('alice@example.com');

-- This fails with a UNIQUE constraint violation:
INSERT INTO users (email) VALUES ('alice@example.com');

```
