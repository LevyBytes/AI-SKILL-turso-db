# Builtin struct and union types

[Context index](data-types-and-experimental-features.md) · [Setup and preceding context](storage-classes-affinity-and-conversions.md) · [Previous phase](strict-tables-and-custom-types.md) · [Continue](array-types-inspection-and-sorting.md)

### Built-in Custom Types

 Turso includes several built-in custom types that can be used in `STRICT` tables:

| Type | Base | Description |
| --- | --- | --- |
| `date` | TEXT | ISO 8601 date (YYYY-MM-DD) |
| `time` | TEXT | ISO 8601 time (HH:MM:SS) |
| `timestamp` | TEXT | ISO 8601 datetime |
| `varchar(N)` | TEXT | Text with maximum length constraint |
| `numeric(P,S)` | BLOB | Fixed-point decimal value with precision and scale |
| `smallint` | INTEGER | Integer constrained to -32768..32767 |
| `boolean` | INTEGER | Integer constrained to 0 or 1 |
| `uuid` | BLOB | UUID represented as a 16-byte blob in storage and shown as a string |
| `bytea` | BLOB | Binary data (PostgreSQL-compatible alias) |
| `inet` | TEXT | IP address |
| `json` | TEXT | Validated JSON text |
| `jsonb` | BLOB | JSON in binary format |

```
CREATE TABLE events (
    id uuid PRIMARY KEY,
    name varchar(100),
    event_date date,
    is_active boolean DEFAULT 1,
    metadata json
) STRICT;

INSERT INTO events VALUES (
    uuid4(),
    'Product Launch',
    '2025-03-15',
    1,
    '{"venue": "online"}'
);

```

 See [CREATE TYPE](https://docs.turso.tech/sql-reference/statements/create-type) for the complete custom type documentation, including ENCODE/DECODE, operators, parametric types, and validation.

### Composite Types: STRUCT and UNION

 Turso supports the composite types **STRUCT** and **UNION**. **STRUCT** is a named product type, while **UNION** is a discriminated union; both allow structured values in one column and dot-notation access.

```
-- STRUCT: group related fields, access with col.field
CREATE TYPE point AS STRUCT(x INT, y INT);
CREATE TABLE locations(id INT PRIMARY KEY, pos point) STRICT;
INSERT INTO locations VALUES (1, struct_pack(10, 20));
SELECT pos.x, pos.y FROM locations WHERE pos.x > 5;
-- 10|20

-- UNION: tagged variants, access with col.variant
CREATE TYPE platform_id AS UNION(telegram INT, slack TEXT);
CREATE TABLE contacts(id INT PRIMARY KEY, platform platform_id) STRICT;
INSERT INTO contacts VALUES (1, union_value('telegram', 12345));
INSERT INTO contacts VALUES (2, union_value('slack', 'U0ABC'));
SELECT id, platform.telegram, platform.slack FROM contacts ORDER BY id;
-- 1|12345|
-- 2||U0ABC

```

 For complete details on composite types, refer to [CREATE TYPE - Composite Types](https://docs.turso.tech/sql-reference/statements/create-type#composite-types-struct-and-union).
