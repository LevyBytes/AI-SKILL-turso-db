# Custom array types and monetary values

[Context index](custom-types-and-view-reference.md) · [Setup and preceding context](custom-type-base-encoding-and-decoding.md) · [Previous phase](composite-type-null-and-dot-semantics.md) · [Continue](custom-type-json-unsigned-and-decimal-examples.md)

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
