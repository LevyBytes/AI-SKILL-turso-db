# Index lookup business rule and filter examples

[Context index](indexes-tables-and-materialized-views.md) · [Setup and preceding context](index-syntax-columns-and-uniqueness.md) · [Previous phase](index-partial-expression-and-fulltext-methods.md)

### Index for a Common Lookup Pattern

```
CREATE TABLE products (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    category TEXT,
    price REAL,
    in_stock INTEGER DEFAULT 1
);

-- Speed up lookups by category
CREATE INDEX idx_products_category ON products (category);

-- Speed up price range queries within a category
CREATE INDEX idx_products_cat_price ON products (category, price);

```

### Unique Index to Enforce a Business Rule

```
CREATE TABLE employees (
    id INTEGER PRIMARY KEY,
    email TEXT NOT NULL,
    department TEXT
);

CREATE UNIQUE INDEX idx_employees_email ON employees (email);

```

### Partial Index for a Status Filter

```
CREATE TABLE tasks (
    id INTEGER PRIMARY KEY,
    assignee TEXT,
    status TEXT DEFAULT 'pending',
    due_date TEXT
);

-- Only index pending tasks -- completed tasks are rarely queried
CREATE INDEX idx_pending_tasks ON tasks (assignee, due_date)
    WHERE status = 'pending';

```

### See Also

- [DROP INDEX](https://docs.turso.tech/sql-reference/statements/drop-index) to delete indexes
- [CREATE TABLE](https://docs.turso.tech/sql-reference/statements/create-table) for table-defined UNIQUE and PRIMARY KEY constraints
- [EXPLAIN](https://docs.turso.tech/sql-reference/statements/explain) to confirm index use in query plans
