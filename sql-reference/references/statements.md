# Alter Table, Analyze, Attach Database, Create Domain

> Sql Reference reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: ALTER TABLE; ANALYZE; ATTACH DATABASE; CREATE DOMAIN.

## ALTER TABLE

### ALTER TABLE

 Change an existing table's structure.

### Syntax

```
ALTER TABLE [schema-name.]table-name RENAME TO new-table-name;
ALTER TABLE [schema-name.]table-name RENAME COLUMN old-column-name TO new-column-name;
ALTER TABLE [schema-name.]table-name ADD COLUMN column-def;
ALTER TABLE [schema-name.]table-name DROP COLUMN column-name;

```

### Description

 `ALTER TABLE` changes an existing table's structure in place, so you do not need to rebuild the table or duplicate its data. Turso supports four forms of ALTER TABLE: rename the table, rename a column, add a new column, or drop an existing column.

### Parameters

| Parameter | Description |
| --- | --- |
| `schema-name` | Name the attached database that holds the table. If omitted, this uses the main database. |
| `table-name` | The table name that will be altered. |

### RENAME TO

 Rename an existing table.

```
ALTER TABLE old_name RENAME TO new_name;

```

 When a table is renamed, every table reference is updated in:
- Triggers that reference the table
- Indexes on the table
- Foreign key constraints, whether the table is the parent or child
- CHECK constraints
- Views that reference the table
 The replacement table name cannot match any table, view, or index already present in the same database.

### Example

```
CREATE TABLE customers (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL
);

ALTER TABLE customers RENAME TO clients;

-- The table is now accessible as 'clients'
SELECT * FROM clients;

```

### RENAME COLUMN

 Change the name of a table column that already exists.

```
ALTER TABLE table-name RENAME COLUMN old-column-name TO new-column-name;

```

 When a column is renamed, all column references are rewritten in:
- Indexes referring to the column, including expression indexes
- Triggers that reference the column
- CHECK constraints that refer to the column
- Foreign key constraint definitions

### Restrictions

 A column rename fails if:
- The table has no column by that name.
- The requested column name duplicates a column already in the same table.
- A trigger on the table refers to the column with a qualified name, for example `table_name.column_name`, inside its body.
- The column is referenced from a trigger's WHEN clause.

### Example

```
CREATE TABLE products (
    id INTEGER PRIMARY KEY,
    product_name TEXT NOT NULL,
    price REAL
);

CREATE INDEX idx_name ON products(product_name);

ALTER TABLE products RENAME COLUMN product_name TO name;

-- The index is automatically updated
-- Queries now use the new column name
SELECT name, price FROM products;

```

### ADD COLUMN

 Add a column to a table that already exists. The added column is always placed last.

```
ALTER TABLE table-name ADD COLUMN column-name type [constraints];

```

### Restrictions

 Columns introduced with ADD COLUMN must meet these requirements:
| Requirement | Reason |
| --- | --- |
| No `PRIMARY KEY` constraint | A table's primary key cannot be modified after creation |
| No `UNIQUE` constraint | A UNIQUE column addition would need a scan of every existing row |
| `NOT NULL` requires a non-NULL default | Every existing row must receive a valid value for the added column |
| The default must be a constant expression | Allowed defaults are limited to literal values, signed literals, and parenthesized constant expressions. Functions such as `random()` are disallowed |
| `CURRENT_TIME`, `CURRENT_DATE`, `CURRENT_TIMESTAMP` defaults require an empty table | Those non-deterministic values cannot be applied backward to rows that already exist |
| CHECK constraints are allowed only when the table is empty | Existing rows cannot be used to validate the constraint |
| No duplicate column name | The table must not already contain that column name |
| No `GENERATED ALWAYS AS` | ALTER TABLE cannot add generated columns |
| STRICT tables must specify a type explicitly | The type assigned to the column must be a valid STRICT type |

### Supported Column Constraints

 Added columns support these constraints:
- `NOT NULL`, when paired with a non-NULL default
- `DEFAULT expression`
- `CHECK (expression)`
- `REFERENCES` (foreign key)
- `COLLATE`

### Examples

#### Add a simple column

```
ALTER TABLE users ADD COLUMN phone TEXT;

```

#### Add a column with a default value

```
ALTER TABLE orders ADD COLUMN priority INTEGER DEFAULT 0;

```

#### Add a NOT NULL column with a default

```
ALTER TABLE products ADD COLUMN in_stock INTEGER NOT NULL DEFAULT 1;

```

#### Add a column with a foreign key

```
ALTER TABLE orders ADD COLUMN warehouse_id INTEGER REFERENCES warehouses(id);

```

#### Add a column on a STRICT table

```
CREATE TABLE metrics (
    id INTEGER PRIMARY KEY,
    value REAL
) STRICT;

-- Must specify a valid STRICT type
ALTER TABLE metrics ADD COLUMN label TEXT DEFAULT 'unknown';

```

### DROP COLUMN

 Delete a column that already exists in a table.

```
ALTER TABLE table-name DROP COLUMN column-name;

```

### Restrictions

 A column drop is prohibited when any condition below applies:
| Condition | Error |
| --- | --- |
| The column is a PRIMARY KEY, or it belongs to one | Cannot drop PRIMARY KEY column |
| The column carries a UNIQUE constraint | Cannot drop UNIQUE column |
| An index references the column | Cannot drop indexed column |
| An expression index references the column | Column used in an expression index cannot be dropped |
| A partial index WHERE clause names the column | Column used in a partial index cannot be dropped |
| A CHECK constraint on another column or on the table refers to the column | Column used in a CHECK constraint cannot be dropped |
| A foreign key in another table references the column | Column used in a foreign key cannot be dropped |
| The table contains exactly one column | The last remaining column cannot be dropped |
| A trigger or view includes the column | Column referenced by a trigger or view cannot be dropped |

### Examples

#### Drop a column

```
ALTER TABLE users DROP COLUMN phone;

```

#### Verify columns after dropping

```
CREATE TABLE example (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    old_field TEXT,
    active INTEGER DEFAULT 1
);

ALTER TABLE example DROP COLUMN old_field;

PRAGMA table_info(example);
-- cid | name   | type    | notnull | dflt_value | pk
-- 0   | id     | INTEGER | 0       | NULL       | 1
-- 1   | name   | TEXT    | 1       | NULL       | 0
-- 2   | active | INTEGER | 0       | 1          | 0

```

### Examples

### Complete Schema Evolution

```
-- Create initial table
CREATE TABLE articles (
    id INTEGER PRIMARY KEY,
    title TEXT NOT NULL,
    body TEXT
);

-- Add an author column with a foreign key
ALTER TABLE articles ADD COLUMN author_id INTEGER REFERENCES users(id);

-- Add a publication status
ALTER TABLE articles ADD COLUMN status TEXT NOT NULL DEFAULT 'draft';

-- Rename a column for clarity
ALTER TABLE articles RENAME COLUMN body TO content;

-- Add a nullable timestamp column
ALTER TABLE articles ADD COLUMN updated_at TEXT;

-- Rename the table
ALTER TABLE articles RENAME TO posts;

-- Final schema
PRAGMA table_info(posts);
-- cid | name       | type    | notnull | dflt_value        | pk
-- 0   | id         | INTEGER | 0       | NULL              | 1
-- 1   | title      | TEXT    | 1       | NULL              | 0
-- 2   | content    | TEXT    | 0       | NULL              | 0
-- 3   | author_id  | INTEGER | 0       | NULL              | 0
-- 4   | status     | TEXT    | 1       | 'draft'           | 0
-- 5   | updated_at | TEXT    | 0       | NULL              | 0

```

### See Also

- [CREATE TABLE](https://docs.turso.tech/sql-reference/statements/create-table) when you need to create tables
- [DROP TABLE](https://docs.turso.tech/sql-reference/statements/drop-table) when you need to remove tables
- [CREATE INDEX](https://docs.turso.tech/sql-reference/statements/create-index) when you need to index columns
- [Data Types](https://docs.turso.tech/sql-reference/data-types) for details about column types and STRICT table types

## ANALYZE

### ANALYZE

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

## ATTACH DATABASE

### ATTACH DATABASE

 The ATTACH DATABASE statement registers an additional database file on the current connection with a schema name. After attachment, tables from that external database can be queried together with tables from the main database.

### Syntax

```
ATTACH [DATABASE] filename AS schema-name;

```

### Description

 ATTACH opens the file identified by `filename` and exposes its tables under the supplied `schema-name`. Refer to tables in that database with `schema-name.table-name`.

### Clauses

| Clause | Description |
| --- | --- |
| `DATABASE` | Optional keyword; it does not change behavior. |
| `filename` | Provide a string expression resolving to the database file path to attach. Use `':memory:'` for an in-memory database. |
| `schema-name` | The qualifier name for table references from the attached database. It must be unique across every database attached to the connection. |

 Each connection includes a built-in schema named `main` for its primary database. `main` and `temp` are reserved, so attached databases cannot use them as schema names. Because this feature is experimental, it must be [enabled before use](https://docs.turso.tech/sql-reference/experimental-features). Attached databases allow reads and writes, including INSERT, UPDATE, DELETE, CREATE TABLE, etc. The attached database's journal mode has to match the main database's journal mode, for example both WAL or both MVCC.

### Examples

### Attach and Query an External Database

```
-- Attach another database file
ATTACH DATABASE 'archive.db' AS archive;

-- Query a table in the attached database
SELECT * FROM archive.events WHERE event_date > '2025-01-01';

```

### Cross-Database Queries

```
ATTACH 'customers.db' AS customers_db;

-- Join tables across the main and attached databases
SELECT o.id, o.total, c.name
FROM main.orders o
JOIN customers_db.customers c ON o.customer_id = c.id;

```

### Attach an In-Memory Database

```
ATTACH ':memory:' AS scratch;

```

### See Also

- [DETACH DATABASE](https://docs.turso.tech/sql-reference/statements/detach-database) when removing an attached database
- [SELECT](https://docs.turso.tech/sql-reference/statements/select) when querying tables across databases

## CREATE DOMAIN

### CREATE DOMAIN

 **Turso Extension**: CREATE DOMAIN is Turso-specific and is not part of standard SQLite. Domains require STRICT tables plus the custom types experimental feature, which must be [enabled before use](https://docs.turso.tech/sql-reference/experimental-features). The CREATE DOMAIN statement creates a named type alias and can add constraints. Unlike [CREATE TYPE](https://docs.turso.tech/sql-reference/statements/create-type), a domain supplies no custom ENCODE/DECODE logic and no OPERATORs. It layers NOT NULL and CHECK constraints over a base type, while values continue to use the base type's native storage format.

### Syntax

```
CREATE DOMAIN [IF NOT EXISTS] domain-name AS base-type
    [DEFAULT default-expr]
    [NOT NULL]
    [[CONSTRAINT constraint-name] CHECK (expr)]
    ...;

```

### Description

 A domain places validation rules around a base type. On writes to a domain-typed column, Turso verifies CHECK constraints and any NOT NULL restriction. Passing values are stored in the base type's native format. Reads return the stored value without changes. For ORDER BY, indexing, arithmetic, aggregation, and similar work, domains are transparent to the query engine. A domain column acts like its base-type column except that inputs are validated. Domains apply only in STRICT tables. In a non-STRICT table, a domain name has no effect.

### Clauses

### IF NOT EXISTS

 Prevents the error that would otherwise be raised when a domain with the same name already exists. The existing domain remains unchanged.

```
CREATE DOMAIN IF NOT EXISTS positive_int AS integer CHECK (value > 0);

```

### AS base-type

 Sets the underlying type. Use a primitive base (`integer`, `real`, `text`, or `blob`) or another domain; using another domain layers the domains.

```
CREATE DOMAIN percentage AS integer
    CHECK (value >= 0)
    CHECK (value <= 100);

```

### DEFAULT

 Defines the default for columns using this domain type. A column-level DEFAULT takes precedence over the domain-level DEFAULT.

```
CREATE DOMAIN status AS text DEFAULT 'active';

CREATE TABLE accounts (
    id INTEGER PRIMARY KEY,
    state status
) STRICT;

INSERT INTO accounts(id) VALUES (1);
SELECT state FROM accounts;
-- active

```

### NOT NULL

 Applies a NOT NULL constraint to the domain. INSERT or UPDATE of NULL into a column of this domain type fails, even when the column definition omits NOT NULL.

```
CREATE DOMAIN required_text AS text NOT NULL;

CREATE TABLE contacts (
    id INTEGER PRIMARY KEY,
    name required_text
) STRICT;

INSERT INTO contacts VALUES (1, 'Alice');
-- OK

INSERT INTO contacts VALUES (2, NULL);
-- Error: domain required_text does not allow null values

```

 Declaring NULL at the column level does not supersede the domain's NOT NULL constraint.

### CHECK

 Adds validation. The expression may use `value` for the input value under test. You can specify multiple CHECK constraints, and every one must pass.

```
CREATE DOMAIN positive_int AS integer CHECK (value > 0);

CREATE TABLE measurements (
    id INTEGER PRIMARY KEY,
    reading positive_int
) STRICT;

INSERT INTO measurements VALUES (1, 42);
-- OK

INSERT INTO measurements VALUES (2, -5);
-- Error: domain positive_int constraint violation

```

### CONSTRAINT name CHECK

 A CHECK constraint may be named when you want documentation-only labeling.

```
CREATE DOMAIN valid_score AS integer
    CONSTRAINT non_negative CHECK (value >= 0)
    CONSTRAINT max_hundred CHECK (value <= 100);

```

### Domain Chaining

 One domain may take another domain as its base type. In a chain, constraints are enforced across every level, from child through ancestor.

```
CREATE DOMAIN base_amount AS integer CHECK (value > 0);
CREATE DOMAIN small_amount AS base_amount CHECK (value < 1000);

CREATE TABLE orders (
    id INTEGER PRIMARY KEY,
    quantity small_amount
) STRICT;

INSERT INTO orders VALUES (1, 50);
-- OK: passes both value > 0 and value < 1000

INSERT INTO orders VALUES (2, -1);
-- Error: fails base_amount's CHECK (value > 0)

INSERT INTO orders VALUES (3, 5000);
-- Error: fails small_amount's CHECK (value < 1000)

```

 Chaining can include three or more levels:

```
CREATE DOMAIN text_val AS text;
CREATE DOMAIN nonempty AS text_val CHECK (length(value) > 0);
CREATE DOMAIN short_text AS nonempty CHECK (length(value) < 50);

CREATE TABLE labels (
    id INTEGER PRIMARY KEY,
    name short_text
) STRICT;

INSERT INTO labels VALUES (1, 'OK');
-- OK

INSERT INTO labels VALUES (2, '');
-- Error: fails nonempty's CHECK (length(value) > 0)

```

### UPDATE Enforcement

 Domain constraints apply during UPDATE and INSERT.

```
CREATE DOMAIN positive_int AS integer CHECK (value > 0);

CREATE TABLE items (
    id INTEGER PRIMARY KEY,
    stock positive_int
) STRICT;

INSERT INTO items VALUES (1, 10);
UPDATE items SET stock = 20 WHERE id = 1;
SELECT stock FROM items;
-- 20

UPDATE items SET stock = -1 WHERE id = 1;
-- Error: domain positive_int constraint violation

```

### CAST Support

 CAST runs the domain's validation constraints:

```
CREATE DOMAIN positive_int AS integer CHECK (value > 0);

SELECT CAST(42 AS positive_int);
-- 42

SELECT CAST(-1 AS positive_int);
-- Error: domain positive_int constraint violation

```

 A CAST from NULL into a NOT NULL domain is rejected:

```
CREATE DOMAIN notnull_int AS integer NOT NULL;

SELECT CAST(NULL AS notnull_int);
-- Error: domain notnull_int does not allow null values

```

### CHECK Constraints with Table-Level Checks

 Both domain CHECK constraints and table-level CHECK constraints are applied. The domain constraint is evaluated during encoding, and the table CHECK is evaluated separately.

```
CREATE DOMAIN positive_int AS integer CHECK (value > 0);

CREATE TABLE bounded (
    id INTEGER PRIMARY KEY,
    val positive_int CHECK (val < 100)
) STRICT;

INSERT INTO bounded VALUES (1, 50);
-- OK: passes domain CHECK (> 0) and table CHECK (< 100)

INSERT INTO bounded VALUES (2, -1);
-- Error: fails domain CHECK

INSERT INTO bounded VALUES (3, 200);
-- Error: fails table CHECK

```

### Operations on Domain Columns

 A domain column supports its base type's operations, including arithmetic, comparisons, ordering, and aggregation.

```
CREATE DOMAIN myint AS integer;

CREATE TABLE data (
    id INTEGER PRIMARY KEY,
    a myint,
    b myint
) STRICT;

INSERT INTO data VALUES (1, 10, 3);
SELECT a + b, a - b, a * b FROM data;
-- 13|7|30

```

```
CREATE DOMAIN myint AS integer;

CREATE TABLE scores (
    id INTEGER PRIMARY KEY,
    val myint
) STRICT;

INSERT INTO scores VALUES (1, 30);
INSERT INTO scores VALUES (2, 10);
INSERT INTO scores VALUES (3, 20);
SELECT val FROM scores ORDER BY val;
-- 10
-- 20
-- 30

```

### Dropping Domains

 Use [DROP DOMAIN](https://docs.turso.tech/sql-reference/statements/drop-domain) to drop domains; do not use DROP TYPE. Using DROP TYPE on a domain, or DROP DOMAIN on a type, raises an error. A domain is not droppable while a table column or another domain still references it.

```
CREATE DOMAIN my_domain AS integer;
CREATE TABLE t(x my_domain) STRICT;

DROP DOMAIN my_domain;
-- Error: type 'my_domain' is in use

DROP TABLE t;
DROP DOMAIN my_domain;
-- OK

```

### Restrictions

- **STRICT tables only**: Domain types are rejected on non-STRICT tables at CREATE TABLE and ALTER TABLE ADD COLUMN time. Non-STRICT tables treat arbitrary type names merely as affinity hints. Domain constraints (CHECK, NOT NULL, DEFAULT) are enforced only on STRICT tables, so allowing domain types in non-STRICT tables would be misleading.
- **Cannot drop while in use**: A domain remains undroppable as long as any table column or another domain references it.
 If a non-STRICT table used a type name that did not yet exist when it was created, such as `CREATE TABLE t(x mydom)`, and a matching domain is later created with `CREATE DOMAIN mydom AS ...`, its domain constraints are **not** applied retroactively to that existing table. This behavior is intentional: non-STRICT tables handle every type name as a plain affinity hint, whether or not a matching domain exists. STRICT tables avoid the scenario by rejecting unknown type names at CREATE TABLE time.
- **No UNIQUE, PRIMARY KEY, or REFERENCES**: Domain definitions do not support these constraints. Put them in table-level constraints instead.
- **No duplicate clauses**: A definition cannot include multiple DEFAULT or NOT NULL clauses. Conflicting NULL and NOT NULL clauses are rejected too.

### See Also

- [DROP DOMAIN](https://docs.turso.tech/sql-reference/statements/drop-domain) when removing domains
- [CREATE TYPE](https://docs.turso.tech/sql-reference/statements/create-type) for custom types that define ENCODE/DECODE logic
- [Data Types](https://docs.turso.tech/sql-reference/data-types) for a type-system overview
- [CREATE TABLE](https://docs.turso.tech/sql-reference/statements/create-table) for defining STRICT tables
