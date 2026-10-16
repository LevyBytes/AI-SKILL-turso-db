# Strict tables and custom types

[Context index](data-types-and-experimental-features.md) · [Setup and preceding context](storage-classes-affinity-and-conversions.md) · [Continue](builtin-struct-and-union-types.md)

### STRICT Tables

 `STRICT` tables perform storage-level type enforcement. Values written to a `STRICT` table must either already fit the declared column type or be coercible to it.

```
CREATE TABLE users (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    age INTEGER,
    score REAL
) STRICT;

-- This works - values match declared types
INSERT INTO users VALUES (1, 'Alice', 30, 95.5);

-- This fails - 'thirty' cannot be converted to INTEGER
INSERT INTO users VALUES (2, 'Bob', 'thirty', 80.0);
-- Error: cannot store TEXT value in INTEGER column

```

### Allowed Types in STRICT Tables

 Only the following base type names are valid in `STRICT` tables:

| Type | Description |
| --- | --- |
| INTEGER | Signed integer |
| REAL | Floating-point number |
| TEXT | UTF-8 string |
| BLOB | Raw binary data |
| ANY | Accepts every storage class, so this column does not perform type checking |

 **Turso Extension**: `STRICT` tables can also use [custom type](https://docs.turso.tech/sql-reference/statements/create-type) names, composite types such as STRUCT and UNION, and array types. Add `[]` after any base type name to declare an array column, for example `INTEGER[]` or `TEXT[]`. Arrays, custom types, and composite types are Turso-specific.

### Custom Types

 **Turso Extension**: Custom types are specific to Turso and are not part of SQLite. They are supported only for `STRICT` tables. The capability is experimental and must be [enabled before use](https://docs.turso.tech/sql-reference/experimental-features). With custom types, you can define value encoding before storage, decoding during reads, storage-layer domain checks, operators, and defaults. Use the `CREATE TYPE` statement to declare them.

```
-- A type that stores monetary values as cents
CREATE TYPE cents BASE integer
    ENCODE value * 100
    DECODE value / 100;

CREATE TABLE prices (
    id INTEGER PRIMARY KEY,
    amount cents
) STRICT;

INSERT INTO prices VALUES (1, 42);
SELECT amount FROM prices;
-- 42  (stored on disk as 4200)

```
