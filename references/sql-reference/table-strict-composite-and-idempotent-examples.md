# Table strict composite and idempotent examples

[Context index](indexes-tables-and-materialized-views.md) · [Setup and preceding context](table-definition-syntax-and-parameters.md) · [Previous phase](table-type-restrictions-and-constraint-examples.md)

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
