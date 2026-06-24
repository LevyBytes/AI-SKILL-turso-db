# Create Index, Create Materialized View, Create Table

> Sql Reference reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: CREATE INDEX; CREATE MATERIALIZED VIEW; CREATE TABLE.

## CREATE INDEX

### CREATE INDEX

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

 After creating an FTS index, query it with the `search()` function:

```
SELECT * FROM articles WHERE search(articles, 'database performance');

```

### Examples

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

## CREATE MATERIALIZED VIEW

### CREATE MATERIALIZED VIEW

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

## CREATE TABLE

### CREATE TABLE

 Define a new database table with columns, data types, and constraints.

### Syntax

```
CREATE TABLE [IF NOT EXISTS] [schema-name.]table-name (
    column-def [, column-def ...] [, table-constraint [, table-constraint ...]]
) [STRICT];

```

### Column Definition

```
column-name [type-name] [column-constraint ...]

```

### Column Constraints

```
PRIMARY KEY [ASC | DESC] [AUTOINCREMENT]
NOT NULL
UNIQUE
DEFAULT expression
CHECK (expression)
REFERENCES foreign-table [(column-name)] [foreign-key-actions]
COLLATE collation-name

```

### Table Constraints

```
PRIMARY KEY (column-name [, column-name ...])
UNIQUE (column-name [, column-name ...])
CHECK (expression)
FOREIGN KEY (column-name [, column-name ...]) REFERENCES foreign-table [(column-name [, column-name ...])] [foreign-key-actions]

```

### Description

 `CREATE TABLE` creates a table in the database. Its name must not duplicate another name in the database schema. A table contains one or more column definitions, plus optional constraints at the table level.

### Parameters

| Parameter | Description |
| --- | --- |
| `IF NOT EXISTS` | Avoids an error when a table using that name already exists. If the table is already present, the statement does nothing. |
| `schema-name` | Names an attached database. If omitted, the main database is used by default. |
| `table-name` | The table name to create. |
| `STRICT` | Turns on strict type checking for the table. See STRICT Tables. |

### Column Definitions

 A column definition gives the column name, may include a type, and may include any number of column constraints. If no type is given, the column can store any storage class. When a type name is present, it sets the column's [type affinity](https://docs.turso.tech/sql-reference/data-types#type-affinity), affecting how inserted values are stored.

```
CREATE TABLE measurements (
    id INTEGER PRIMARY KEY,
    sensor_name TEXT NOT NULL,
    reading REAL,
    raw_data BLOB,
    notes                    -- no type: accepts any storage class
);

```

### Column Constraints

### PRIMARY KEY

 Marks the column as the table's primary key. Only one primary key is allowed per table.

```
CREATE TABLE users (
    id INTEGER PRIMARY KEY
);

```

 A column declared `INTEGER PRIMARY KEY` aliases the internal `rowid`; integer values inserted into it become the row identifier directly. This is the fastest primary key form.

#### AUTOINCREMENT

 The `AUTOINCREMENT` keyword is valid only with `INTEGER PRIMARY KEY`. It keeps a counter in the `sqlite_sequence` system table so rowid values from deleted rows are not reused.

```
CREATE TABLE events (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    description TEXT
);

```

 Without `AUTOINCREMENT`, Turso may reuse rowid values after deletions. With `AUTOINCREMENT`, each rowid is guaranteed to exceed every rowid that has ever existed in the table, even after rows have been deleted. Because `AUTOINCREMENT` updates the `sqlite_sequence` table on each insert, it adds a small amount of overhead. Use it only when identifiers must be strictly monotonically increasing.

### NOT NULL

 Disallows NULL in the column. An INSERT or UPDATE that would store NULL in that column raises a constraint error.

```
CREATE TABLE products (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    price REAL NOT NULL
);

```

### UNIQUE

 Requires every column value to be distinct. NULL values are treated as distinct from one another, so a UNIQUE column may contain multiple NULL values.

```
CREATE TABLE accounts (
    id INTEGER PRIMARY KEY,
    email TEXT UNIQUE
);

```

### DEFAULT

 Provides the column's fallback value when an INSERT statement omits that column.

```
CREATE TABLE orders (
    id INTEGER PRIMARY KEY,
    status TEXT DEFAULT 'pending',
    created_at TEXT DEFAULT CURRENT_TIMESTAMP,
    quantity INTEGER DEFAULT 1
);

```

 The default expression can be:
| Expression | Example |
| --- | --- |
| A literal value | `DEFAULT 42`, `DEFAULT 'active'` |
| NULL | `DEFAULT NULL` |
| `CURRENT_TIME` | Current time as `HH:MM:SS` |
| `CURRENT_DATE` | Current date as `YYYY-MM-DD` |
| `CURRENT_TIMESTAMP` | Current datetime as `YYYY-MM-DD HH:MM:SS` |
| A parenthesized expression | `DEFAULT (1 + 1)` |

### CHECK

 Creates a boolean expression that every table row must evaluate as true or NULL. The expression may refer to any column in that same row.

```
CREATE TABLE employees (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    age INTEGER CHECK (age >= 18),
    salary REAL CHECK (salary > 0)
);

```

### REFERENCES (Foreign Key)

 Creates a foreign key link from this column to a column in another table. See Foreign Key Constraints for the complete syntax.

```
CREATE TABLE orders (
    id INTEGER PRIMARY KEY,
    customer_id INTEGER REFERENCES customers(id)
);

```

### COLLATE

 Sets the collation sequence for text comparison and column sorting.
| Collation | Description |
| --- | --- |
| `BINARY` | Compares text by raw byte value (default) |
| `NOCASE` | Compares ASCII characters without case sensitivity |
| `RTRIM` | Like `BINARY`, but ignores trailing spaces |

```
CREATE TABLE contacts (
    id INTEGER PRIMARY KEY,
    name TEXT COLLATE NOCASE,
    code TEXT COLLATE RTRIM
);

```

### Table Constraints

 Table constraints are written after the column definitions and can apply across one or more columns.

### Composite PRIMARY KEY

 Creates a primary key over several columns. The combined values across those key columns must be unique.

```
CREATE TABLE enrollment (
    student_id INTEGER,
    course_id INTEGER,
    semester TEXT,
    grade REAL,
    PRIMARY KEY (student_id, course_id, semester)
);

```

### Composite UNIQUE

 Requires every value combination in the selected columns to be unique.

```
CREATE TABLE assignments (
    employee_id INTEGER,
    project_id INTEGER,
    role TEXT,
    UNIQUE (employee_id, project_id)
);

```

### Table-Level CHECK

 A table-level CHECK constraint may refer to more than one column.

```
CREATE TABLE reservations (
    id INTEGER PRIMARY KEY,
    start_date TEXT NOT NULL,
    end_date TEXT NOT NULL,
    CHECK (end_date > start_date)
);

```

### Foreign Key Constraints

 Foreign key constraints maintain referential integrity across tables by requiring child-table values to match existing parent-table values. Enforcement of foreign keys is disabled by default. Turn it on with `PRAGMA foreign_keys = ON;` before running operations that need checking.

#### Syntax

```
-- Column constraint form (single column)
column-name type REFERENCES parent-table(parent-column)
    [ON DELETE action] [ON UPDATE action]
    [DEFERRABLE INITIALLY DEFERRED]

-- Table constraint form (single or multiple columns)
FOREIGN KEY (column-name [, column-name ...])
    REFERENCES parent-table(parent-column [, parent-column ...])
    [ON DELETE action] [ON UPDATE action]
    [DEFERRABLE INITIALLY DEFERRED]

```

#### Foreign Key Actions

 The `ON DELETE` and `ON UPDATE` clauses define the effect on child rows when the referenced parent row is deleted or updated.
| Action | Description |
| --- | --- |
| `NO ACTION` | Raises an error when the foreign key constraint is violated (default) |
| `RESTRICT` | Like `NO ACTION`, except the check occurs immediately instead of at statement end |
| `CASCADE` | Deletes or updates each child row that references the parent row |
| `SET NULL` | Sets child-row foreign key columns to NULL |
| `SET DEFAULT` | Sets child-row foreign key columns to their default values |

```
CREATE TABLE departments (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL
);

CREATE TABLE employees (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    dept_id INTEGER,
    manager_id INTEGER,
    FOREIGN KEY (dept_id) REFERENCES departments(id) ON DELETE CASCADE,
    FOREIGN KEY (manager_id) REFERENCES employees(id) ON DELETE SET NULL
);

```

### STRICT Tables

 STRICT tables check types at the storage layer. For each STRICT column, an inserted value has to match its declared type or convert to that type without loss.

```
CREATE TABLE sensor_data (
    id INTEGER PRIMARY KEY,
    reading REAL NOT NULL,
    label TEXT,
    payload BLOB
) STRICT;

```

### Allowed Column Types

 Only the following base column types are valid in STRICT tables:
| Type | Description |
| --- | --- |
| `INTEGER` | Signed integer |
| `REAL` | Floating-point number |
| `TEXT` | UTF-8 string |
| `BLOB` | Raw binary data |
| `ANY` | Any storage class; type checking is disabled for this column |

 In a STRICT table, each column must declare an explicit type.

```
-- This fails: STRICT tables require explicit types on every column
CREATE TABLE bad (
    id INTEGER PRIMARY KEY,
    data  -- error: missing type
) STRICT;

```

 **Turso Extension**: STRICT tables also accept custom types created with [CREATE TYPE](https://docs.turso.tech/sql-reference/statements/create-type) and [array types](https://docs.turso.tech/sql-reference/data-types#array-types). Custom types add user-defined encoding, decoding, validation, and operator overloading to the type system. Declare array columns by appending `[]` to a base type.

```
CREATE TABLE events (
    id uuid PRIMARY KEY,
    name varchar(100) NOT NULL,
    event_date date,
    is_active boolean DEFAULT 1,
    tags TEXT[]
) STRICT;

```

### Unsupported Features

 These CREATE TABLE features are not currently supported:
| Feature | Notes |
| --- | --- |
| `CREATE TEMPORARY TABLE` | Temporary tables are not supported |
| `CREATE TABLE ... AS SELECT` | Creating a table from query results is unsupported |
| `WITHOUT ROWID` | WITHOUT ROWID tables are unsupported |
| `GENERATED ALWAYS AS` | Generated (computed) columns are unsupported |
| `ON CONFLICT` clause on column constraints | Column-level ON CONFLICT clauses, such as `NOT NULL ON CONFLICT REPLACE`, are unsupported. Use INSERT ... ON CONFLICT syntax instead |

### Examples

### Basic Table with Constraints

```
CREATE TABLE users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    username TEXT NOT NULL UNIQUE,
    email TEXT NOT NULL UNIQUE,
    display_name TEXT,
    bio TEXT DEFAULT '',
    created_at TEXT DEFAULT CURRENT_TIMESTAMP
);

```

### Table with Foreign Keys and CHECK Constraints

```
CREATE TABLE orders (
    id INTEGER PRIMARY KEY,
    customer_id INTEGER NOT NULL,
    total_amount REAL NOT NULL CHECK (total_amount >= 0),
    status TEXT NOT NULL DEFAULT 'pending' CHECK (status IN ('pending', 'shipped', 'delivered', 'cancelled')),
    order_date TEXT NOT NULL DEFAULT CURRENT_DATE,
    FOREIGN KEY (customer_id) REFERENCES customers(id) ON DELETE RESTRICT
);

```

### STRICT Table

```
CREATE TABLE inventory (
    product_id INTEGER PRIMARY KEY,
    sku TEXT NOT NULL UNIQUE,
    quantity INTEGER NOT NULL DEFAULT 0 CHECK (quantity >= 0),
    price REAL NOT NULL CHECK (price > 0),
    category TEXT NOT NULL
) STRICT;

-- This succeeds: '42' is losslessly convertible to INTEGER
INSERT INTO inventory VALUES (1, 'WIDGET-001', '42', 9.99, 'widgets');

-- This fails: 'abc' cannot be converted to INTEGER
INSERT INTO inventory VALUES (2, 'GADGET-001', 'abc', 19.99, 'gadgets');
-- Error: cannot store TEXT value in INTEGER column

```

### Composite Primary Key with Foreign Keys

```
CREATE TABLE course_enrollment (
    student_id INTEGER NOT NULL,
    course_id INTEGER NOT NULL,
    enrolled_date TEXT DEFAULT CURRENT_DATE,
    grade REAL CHECK (grade >= 0.0 AND grade <= 4.0),
    PRIMARY KEY (student_id, course_id),
    FOREIGN KEY (student_id) REFERENCES students(id) ON DELETE CASCADE,
    FOREIGN KEY (course_id) REFERENCES courses(id) ON DELETE CASCADE
);

```

### IF NOT EXISTS

```
-- Safe to run multiple times
CREATE TABLE IF NOT EXISTS sessions (
    id INTEGER PRIMARY KEY,
    user_id INTEGER NOT NULL,
    token TEXT NOT NULL UNIQUE,
    expires_at TEXT NOT NULL,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);

```

### See Also

- [ALTER TABLE](https://docs.turso.tech/sql-reference/statements/alter-table) to change existing tables
- [DROP TABLE](https://docs.turso.tech/sql-reference/statements/drop-table) to delete tables
- [Data Types](https://docs.turso.tech/sql-reference/data-types) for type affinity and STRICT table types
- [CREATE INDEX](https://docs.turso.tech/sql-reference/statements/create-index) for indexes on table columns
- [CREATE TYPE](https://docs.turso.tech/sql-reference/statements/create-type) for custom STRICT table types
- [INSERT](https://docs.turso.tech/sql-reference/statements/insert) to add rows to a table
