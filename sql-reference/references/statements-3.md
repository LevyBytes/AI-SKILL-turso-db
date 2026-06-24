# Create Type, Create View

> Sql Reference reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: CREATE TYPE; CREATE VIEW.

## CREATE TYPE

### CREATE TYPE

 **Turso Extension**: CREATE TYPE is specific to Turso and is not part of standard SQLite. It can be used only with STRICT tables, and the experimental feature has to be turned on first; see [experimental features](https://docs.turso.tech/sql-reference/experimental-features). A CREATE TYPE definition creates a user-defined type that determines storage encoding, read-time decoding, input validation, sort comparison behavior, and the default value. These custom types expand the STRICT table type system past SQLite's five built-in storage classes.

### Syntax

```
CREATE TYPE [IF NOT EXISTS] type-name [(parameters)]
    BASE base-type
    ENCODE encode-expr
    DECODE decode-expr
    [OPERATOR 'op' [function-name] ...]
    [DEFAULT default-expr];

```

### Description

 A custom type attaches user-defined behavior to one of the four base storage types. On writes to a column using that type, ENCODE converts the value into its stored representation. On reads, DECODE converts the stored value back. This makes it possible to keep compact data on disk while exposing another shape to queries. Custom types apply only in STRICT tables; in a non-STRICT table, a custom type name changes nothing.

### Clauses

### IF NOT EXISTS

 Avoids raising an error when a type with that name is already present. The existing type definition is not modified.

```
CREATE TYPE IF NOT EXISTS cents BASE integer ENCODE value * 100 DECODE value / 100;

```

### BASE

 Chooses the SQLite storage class used as the on-disk representation. A BASE clause is required for every custom type.
| Base Type | Description |
| --- | --- |
| `integer` | Signed integer using 1-8 bytes, with size chosen by magnitude |
| `real` | 8-byte IEEE 754 floating-point number |
| `text` | UTF-8 encoded string |
| `blob` | Raw binary data |

```
CREATE TYPE cents BASE integer
    ENCODE value * 100
    DECODE value / 100;

```

### ENCODE / DECODE

 ENCODE runs each time a value is written into a column of this type, while DECODE runs each time the value is read back. In both expressions, the identifier `value` denotes the input.
- **ENCODE**: Converts the incoming value to the base storage representation. Executes for INSERT, UPDATE, and CAST.
- **DECODE**: Converts the stored representation back to the presented value. Executes for SELECT.
- **NULL handling**: NULL skips ENCODE and DECODE entirely. Given NULL input, the result is NULL and the expression is not evaluated.

```
-- Store text in reversed form
CREATE TYPE reversed_text BASE text
    ENCODE turso_reverse(value)
    DECODE turso_reverse(value);

```

 Put validation rules in ENCODE. When ENCODE raises an error, the associated INSERT or UPDATE does not continue.

### Parameters

 Custom types may declare parameters that ENCODE and DECODE can reference. Declare them in parentheses after the type name; every parameter has a name and may include a type annotation.

```
CREATE TYPE varchar(value text, maxlen integer) BASE text
    ENCODE CASE
        WHEN length(value) <= maxlen THEN value
        ELSE RAISE(ABORT, 'value too long for varchar')
    END
    DECODE value;

```

 For a column that uses a parametric type, place the arguments in parentheses after the type name:

```
CREATE TABLE users (
    id INTEGER PRIMARY KEY,
    name varchar(100),
    bio varchar(500)
) STRICT;

```

 The initial parameter is always the input `value`. Later parameters correspond, in order, to the arguments from the column type declaration.

### OPERATOR

 OPERATOR binds SQL operators to functions, allowing custom-typed columns to work in comparisons, arithmetic expressions, and sort ordering.

```
OPERATOR 'op' function-name

```

| Component | Description |
| --- | --- |
| `'op'` | The operator symbol as a string literal: `'+'`, `'-'`, `'*'`, `'/'`, `'<'`, `'='`, etc. |
| `function-name` | The name of a SQL function to call when this operator is used. Omit to use the base type’s native comparison. |

 With an operator definition present, an expression such as `column + 1` becomes `function_name(column, 1)`.

#### Ordering with OPERATOR ’<’

 The `<` operator has special behavior because it defines sorting for this type in ORDER BY, MIN, MAX, and CREATE INDEX.
- **`OPERATOR '<'`** (no function name): Orders by the base type's native comparison. This is enough when the encoded representation already sorts correctly (e.g., text dates in ISO 8601 format).
- **`OPERATOR '<' my_compare`**: Orders using `my_compare(a, b)` as the comparator. The function must yield a negative integer for `a < b`, zero for `a = b`, and a positive integer for `a > b`.
- **No `<` operator**: Values of the type are not allowed in ORDER BY or CREATE INDEX.

```
-- Use base type comparison for ordering
CREATE TYPE cents BASE integer
    ENCODE value * 100
    DECODE value / 100
    OPERATOR '<';

-- Full operator set for a numeric type
CREATE TYPE uint BASE integer
    ENCODE CASE WHEN value >= 0 THEN value ELSE RAISE(ABORT, 'uint must be non-negative') END
    DECODE value
    OPERATOR '+' uint_add
    OPERATOR '-' uint_sub
    OPERATOR '*' uint_mul
    OPERATOR '<'
    OPERATOR '=';

```

 When `=` exists, it is also used to produce `!=`. The `<` definition automatically supplies `>`, `>=`, and `<=` by reversing arguments or negating the result.

### DEFAULT

 DEFAULT defines the default at the type level. If a column of this type has no DEFAULT of its own, this value is applied.

```
CREATE TYPE boolean BASE integer
    ENCODE boolean_to_int(value)
    DECODE CASE WHEN value THEN 1 ELSE 0 END
    OPERATOR '<'
    DEFAULT 0;

```

 A DEFAULT specified on the column takes precedence over the type-level DEFAULT:

```
CREATE TABLE flags (
    id INTEGER PRIMARY KEY,
    is_active boolean,              -- uses type default (0)
    is_admin boolean DEFAULT 1      -- overrides type default
) STRICT;

```

### Validation with RAISE

 Validate writes by using CASE/WHEN together with RAISE inside ENCODE. If the check is not satisfied, the statement stops with the configured error message.

```
CREATE TYPE positive_int BASE integer
    ENCODE CASE
        WHEN value > 0 THEN value
        ELSE RAISE(ABORT, 'value must be positive')
    END
    DECODE value;

```

```
CREATE TYPE email BASE text
    ENCODE CASE
        WHEN value LIKE '%_@_%.__%' THEN lower(value)
        ELSE RAISE(ABORT, 'invalid email address')
    END
    DECODE value;

```

### CAST Support

 The CAST expression runs the custom type's ENCODE behavior:

```
CREATE TYPE cents BASE integer
    ENCODE value * 100
    DECODE value / 100;

SELECT CAST(42.5 AS cents);
-- 4250

```

 Use this when a value needs to be converted to its encoded representation outside INSERT/UPDATE, including in WHERE clauses and CHECK constraints.

### CHECK Constraints with Custom Types

 For STRICT tables that use custom types, CHECK constraints evaluate the **decoded** presentation values. To compare a custom-typed column to a literal, encode the literal with CAST:

```
CREATE TYPE cents BASE integer
    ENCODE value * 100
    DECODE value / 100;

CREATE TABLE products (
    id INTEGER PRIMARY KEY,
    price cents CHECK(price >= CAST(0 AS cents))
) STRICT;

```

### Inspecting Types

### PRAGMA list_types

 Shows every available type, covering both built-in and user-defined types:

```
PRAGMA list_types;
-- name
-- boolean
-- varchar
-- date
-- time
-- timestamp
-- smallint
-- numeric
-- ...

```

### sqlite_turso_types Virtual Table

 The `sqlite_turso_types` virtual table exposes each type's name and SQL definition:

```
SELECT name, sql FROM sqlite_turso_types;
-- name    | sql
-- boolean | CREATE TYPE boolean(value any) BASE integer ENCODE ...
-- ...

```

### Using with ALTER TABLE

 Custom types are valid in ALTER TABLE ADD COLUMN when adding new columns:

```
ALTER TABLE users ADD COLUMN email varchar(255);

```

 The new column uses the same STRICT type-checking behavior as columns declared in the original CREATE TABLE.

### Built-in Types

 Turso includes these built-in custom types for use in STRICT tables:
| Type | Base | Parameters | Description |
| --- | --- | --- | --- |
| `boolean` | integer | — | Constrained to 0 or 1. Aliases: `bool`. |
| `smallint` | integer | — | Integer constrained to -32768 through 32767. Aliases: `int2`. |
| `bigint` | integer | — | Integer (no range constraint). Aliases: `int8`. |
| `varchar(N)` | text | `maxlen` | Text capped at N characters. |
| `date` | text | — | ISO 8601 date (YYYY-MM-DD), checked during insert. |
| `time` | text | — | ISO 8601 time (HH:MM:SS), checked during insert. |
| `timestamp` | text | — | ISO 8601 datetime, validated on insert. |
| `numeric(P,S)` | blob | `precision`, `scale` | Fixed-point decimal with P digits total and S digits after the decimal point. Arithmetic operators are supported. |
| `uuid` | blob | — | UUID stored as a 16-byte blob and shown as a string. Default: `uuid4_str()`. |
| `inet` | text | — | Validated IP address, either IPv4 or IPv6. |
| `bytea` | blob | — | Binary data (PostgreSQL-compatible alias). |
| `json` | text | — | Validated JSON text. |
| `jsonb` | blob | — | JSON kept in binary form and returned as JSON text when read. |

 The `uuid`, `json`, and `jsonb` types depend on their matching extensions being compiled in. Standard Turso builds include them by default.

### Composite Types: STRUCT and UNION

 CREATE TYPE supports two composite forms in addition to encode/decode custom types: **STRUCT** (a named product type) and **UNION** (a discriminated union / tagged variant). Both require STRICT tables and store their data on disk as blobs.

### STRUCT

 A STRUCT combines several named fields into one column. Dot notation lets you read or filter by a specific field:

```
CREATE TYPE point AS STRUCT(x INT, y INT);

CREATE TABLE locations (
    id INTEGER PRIMARY KEY,
    pos point
) STRICT;

INSERT INTO locations VALUES (1, struct_pack(10, 20));
INSERT INTO locations VALUES (2, struct_pack(30, 40));

-- Read fields with dot notation
SELECT pos.x, pos.y FROM locations WHERE id = 1;
-- 10|20

-- Filter and order by fields
SELECT id, pos.x FROM locations WHERE pos.y > 25 ORDER BY pos.x;
-- 2|30

```

 Dot notation is accepted anywhere a column expression is valid, including SELECT, WHERE, ORDER BY, GROUP BY, HAVING, and aggregate functions:

```
CREATE TYPE address AS STRUCT(street TEXT, city TEXT, zip TEXT);
CREATE TABLE people(name TEXT, home address) STRICT;

INSERT INTO people VALUES ('Alice', struct_pack('123 Main St', 'Springfield', '62704'));
INSERT INTO people VALUES ('Bob', struct_pack('456 Oak Ave', 'Springfield', '62701'));

SELECT home.city, COUNT(*) FROM people GROUP BY home.city;
-- Springfield|2

```

 If a table name conflicts with a column name, the table reference takes precedence. Use an alias to access the struct field:

```
-- t.x here is table.column, not column.field
SELECT t.x FROM t;

-- Use an alias to access the struct field
SELECT s.pos.x FROM locations AS s;

```

#### struct_pack()

 Builds a struct value for INSERT and UPDATE. Arguments are positional and follow the field order from the type definition.

```
INSERT INTO locations VALUES (1, struct_pack(10, 20));
UPDATE locations SET pos = struct_pack(99, 88) WHERE id = 1;

```

#### struct_extract()

 Functional equivalent of dot notation: `struct_extract(col, 'field')` matches `col.field`. It is mainly useful for expression indexes, where dot notation is unavailable:

```
CREATE INDEX idx_x ON locations(struct_extract(pos, 'x'));

```

### UNION

 A UNION is a discriminated union (tagged variant). Each stored value has exactly one declared variant, selected by a tag. Dot notation extracts the value for a variant and returns NULL when that variant is not active:

```
CREATE TYPE platform_id AS UNION(telegram INT, slack TEXT, signal TEXT);

CREATE TABLE contacts (
    id INTEGER PRIMARY KEY,
    platform platform_id
) STRICT;

INSERT INTO contacts VALUES (1, union_value('telegram', 12345));
INSERT INTO contacts VALUES (2, union_value('slack', 'U0ABC'));
INSERT INTO contacts VALUES (3, union_value('signal', '+1555'));

-- Dot notation extracts the variant value (NULL if tag doesn't match)
SELECT id, platform.telegram, platform.slack FROM contacts ORDER BY id;
-- 1|12345|
-- 2||U0ABC
-- 3||

-- Combine with union_tag() to filter by variant
SELECT id, platform.slack FROM contacts WHERE union_tag(platform) = 'slack';
-- 2|U0ABC

```

 For unions with struct-type variants, chain dot notation to access fields inside the nested struct:

```
CREATE TYPE telegram_msg AS STRUCT(chat_id INT, text TEXT);
CREATE TYPE msg AS UNION(telegram telegram_msg, slack TEXT);
CREATE TABLE messages(id INT, data msg) STRICT;

INSERT INTO messages VALUES (1, union_value('telegram', struct_pack(100, 'hello')));

-- col.variant.field
SELECT data.telegram.chat_id, data.telegram.text FROM messages;
-- 100|hello

```

#### union_value()

 Constructs a union value for INSERT and UPDATE. Pass the variant tag (a string literal) first and the value second. The tag is matched against the target column's union type.

```
INSERT INTO contacts VALUES (1, union_value('telegram', 12345));
UPDATE contacts SET platform = union_value('slack', 'U0XYZ') WHERE id = 1;

```

#### union_tag()

 Returns the active variant's tag name as text.

```
SELECT id, union_tag(platform) FROM contacts ORDER BY id;
-- 1|telegram
-- 2|slack
-- 3|signal

```

#### union_extract()

 Functional equivalent of dot notation: `union_extract(col, 'variant')` matches `col.variant`. It is mainly useful in expression indexes:

```
CREATE INDEX idx_tg ON contacts(union_extract(platform, 'telegram'));

```

### NULL handling

 NULL propagates through every struct and union operation:

```
CREATE TYPE number AS UNION(i INT, f REAL);
CREATE TABLE values_t(id INT, val number) STRICT;

INSERT INTO values_t VALUES (1, union_value('i', 42));
INSERT INTO values_t VALUES (2, union_value('f', 3.14));
INSERT INTO values_t VALUES (3, NULL);

SELECT id, val.i, val.f FROM values_t ORDER BY id;
-- 1|42|
-- 2||3.14
-- 3||

```

### Dot notation precedence

 For an ambiguous dot expression like `a.b`, where `a` might be either a table name or a column name, table references take precedence over struct field access. This matches DuckDB's resolution rules.

```
CREATE TYPE point AS STRUCT(x INT, y INT);
CREATE TABLE t(x INT, t point) STRICT;
INSERT INTO t VALUES (99, struct_pack(10, 20));

-- t.x resolves as table.column (99), not column.field (10)
SELECT t.x FROM t;
-- 99

-- Use an alias to reach the struct field
SELECT s.t.x FROM t AS s;
-- 10

```

 For `a.b.c`, resolution tries database.table.column first, then table.column.field.

### Inline types not supported

 Column definitions cannot declare inline STRUCT/UNION types. Define the type with CREATE TYPE before using it:

```
-- This will error:
CREATE TABLE t(data STRUCT(x INT, y INT)) STRICT;

-- Do this instead:
CREATE TYPE point AS STRUCT(x INT, y INT);
CREATE TABLE t(data point) STRICT;

```

### Array Types

 Array columns hold ordered value collections. Unlike STRUCT and UNION, arrays do not need a prior CREATE TYPE; in a STRICT table column definition, declare one by adding `[]` to any base type name:

```
CREATE TABLE sensors (
    id INTEGER PRIMARY KEY,
    readings REAL[],
    labels TEXT[],
    flags INTEGER[]
) STRICT;

INSERT INTO sensors VALUES (1, ARRAY[1.5, 2.5, 3.5], ARRAY['temp','humidity'], '[0, 1, 1]');

SELECT readings[0], labels[1], array_length(flags) FROM sensors;
-- 1.5|humidity|3

```

 Use repeated bracket pairs for multi-dimensional arrays:

```
CREATE TABLE matrices (
    id INTEGER PRIMARY KEY,
    data INTEGER[][]
) STRICT;

INSERT INTO matrices VALUES (1, ARRAY[ARRAY[1,2], ARRAY[3,4]]);
SELECT data[1][0] FROM matrices;
-- 3

```

 Array element types are checked on insert; for example, an `INTEGER[]` column refuses values that cannot be stored as integers. Internally, arrays use compact record-format BLOBs, and output displays them as JSON arrays. See [Array Functions](https://docs.turso.tech/sql-reference/functions/array) for subscript syntax, operators (`@>`, `&&`, `||`), and the complete function set, including `array_agg`, `array_append`, `array_contains`, `array_slice`, etc.

### Restrictions

- **STRICT tables only**: In non-STRICT tables, custom type names are ignored and the column instead follows normal type affinity rules.
- **Cannot drop while in use**: DROP TYPE cannot remove a type while any table still contains a column using it.
 If a non-STRICT table is created with a type name that is not defined yet (e.g., `CREATE TABLE t(x mytype)`), then later creation of a matching custom type (`CREATE TYPE mytype ...`) does **not** apply that type's ENCODE/DECODE behavior to the existing table retroactively. This behavior is expected: non-STRICT tables interpret all type names only as affinity hints, whether or not a corresponding custom type exists. STRICT tables avoid this case by rejecting unknown type names during CREATE TABLE.
- **No subqueries in expressions**: ENCODE, DECODE, and DEFAULT expressions may not include subqueries, aggregate functions, or window functions.
- **No indexes on STRUCT/UNION columns**: A STRUCT or UNION column cannot be indexed with CREATE INDEX.

### Examples

### Identity Type (Passthrough)

 A minimal type that round-trips values without changing them:

```
CREATE TYPE my_text BASE text ENCODE value DECODE value;

CREATE TABLE notes (
    id INTEGER PRIMARY KEY,
    body my_text
) STRICT;

```

### Monetary Values as Cents

 Represent dollar amounts as integer cents for exact arithmetic, while showing the original value:

```
CREATE TYPE cents BASE integer
    ENCODE value * 100
    DECODE value / 100
    OPERATOR '<';

CREATE TABLE invoices (
    id INTEGER PRIMARY KEY,
    amount cents
) STRICT;

INSERT INTO invoices VALUES (1, 42);
-- Stored as 4200 on disk, queried as 42

SELECT amount FROM invoices;
-- 42

```

### JSON Validation

 Check that inserted values contain valid JSON:

```
CREATE TYPE json BASE text
    ENCODE json(value)
    DECODE value;

CREATE TABLE configs (
    id INTEGER PRIMARY KEY,
    data json
) STRICT;

INSERT INTO configs VALUES (1, '{"key": "value"}');
-- succeeds

INSERT INTO configs VALUES (2, 'not json');
-- Error: malformed JSON

```

### Unsigned Integer with Operators

 A non-negative integer type that defines arithmetic and comparison operators:

```
CREATE TYPE uint BASE integer
    ENCODE CASE
        WHEN value >= 0 THEN value
        ELSE RAISE(ABORT, 'uint must be non-negative')
    END
    DECODE value
    OPERATOR '<'
    OPERATOR '=';

CREATE TABLE counters (
    id INTEGER PRIMARY KEY,
    count uint DEFAULT 0
) STRICT;

INSERT INTO counters VALUES (1, 5);
SELECT * FROM counters WHERE count > 0 ORDER BY count;

```

### Fixed-Point Decimal

 For precise decimal arithmetic, use the built-in `numeric` type:

```
CREATE TABLE transactions (
    id INTEGER PRIMARY KEY,
    amount numeric(10, 2),
    tax numeric(10, 2)
) STRICT;

INSERT INTO transactions VALUES (1, 99.99, 8.25);
SELECT amount + tax FROM transactions;
-- 108.24

```

### See Also

- [DROP TYPE](https://docs.turso.tech/sql-reference/statements/drop-type) explains how to remove custom types
- [CREATE DOMAIN](https://docs.turso.tech/sql-reference/statements/create-domain) covers constrained type aliases without ENCODE/DECODE logic
- [Data Types](https://docs.turso.tech/sql-reference/data-types) gives an overview of type affinity and the type system
- [Array Types](https://docs.turso.tech/sql-reference/data-types#array-types) describes native array columns (`INTEGER[]`, `TEXT[]`, etc.)
- [Array Functions](https://docs.turso.tech/sql-reference/functions/array) covers creating, changing, and operating on arrays
- [CREATE TABLE](https://docs.turso.tech/sql-reference/statements/create-table) documents STRICT table definitions

## CREATE VIEW

### CREATE VIEW

 Define a named view around a SELECT statement.

### Syntax

```
CREATE [TEMPORARY] VIEW [IF NOT EXISTS] [schema-name.]view-name
    AS select-statement;

```

### Description

 A view is a virtual table specified by a SELECT statement. It stores no data on disk; when queried, Turso runs the underlying SELECT. Views can simplify complex queries, keep a stable interface as table schemas change, and limit the visible rows or columns.

### Parameters

| Parameter | Description |
| --- | --- |
| `TEMPORARY` | Creates the view in a temporary database. Only the current connection can see it, and it is dropped when that connection closes. `TEMP` is allowed as a synonym. |
| `IF NOT EXISTS` | Avoids an error when a view with the same name already exists. If the view is already present, the statement performs no action. |
| `schema-name` | Names the attached database where the view should be created. If omitted, the main database is used by default. This cannot be combined with `TEMPORARY`. |
| `view-name` | A database-unique name assigned to the view. |
| `select-statement` | The SELECT query used as the view definition. |

### Behavior

- Views are read-only. INSERT, UPDATE, and DELETE against a view are unsupported unless the view has an INSTEAD OF trigger.
- Turso stores the SELECT statement in `sqlite_schema` and parses it whenever the view is referenced.
- Dropping a referenced table or altering it incompatibly causes later queries against the view to error.

### Examples

### Simplify a Common Query

```
CREATE VIEW active_users AS
SELECT id, name, email
FROM users
WHERE status = 'active';

-- Use the view like a table
SELECT * FROM active_users WHERE name LIKE 'A%';

```

### Aggregate View

```
CREATE VIEW department_stats AS
SELECT
    department,
    COUNT(*) AS employee_count,
    AVG(salary) AS avg_salary
FROM employees
GROUP BY department;

SELECT * FROM department_stats ORDER BY avg_salary DESC;

```

### Temporary View for a Session

```
CREATE TEMP VIEW my_tasks AS
SELECT id, title, due_date
FROM tasks
WHERE assignee = 'alice';

```

### See Also

- CREATE MATERIALIZED VIEW: [materialized view reference](https://docs.turso.tech/sql-reference/statements/create-materialized-view) covers views that keep precomputed results on disk
- [DROP VIEW](https://docs.turso.tech/sql-reference/statements/drop-view) explains view removal
- [SELECT](https://docs.turso.tech/sql-reference/statements/select) documents the query syntax used by view definitions
- [CREATE TRIGGER](https://docs.turso.tech/sql-reference/statements/create-trigger) covers INSTEAD OF triggers that make views writable
