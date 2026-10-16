# Table type restrictions and constraint examples

[Context index](indexes-tables-and-materialized-views.md) · [Setup and preceding context](table-definition-syntax-and-parameters.md) · [Previous phase](table-foreign-key-actions-and-strict-mode.md) · [Continue](table-strict-composite-and-idempotent-examples.md)

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
