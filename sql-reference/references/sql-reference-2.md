# Data Types, Experimental Features

> Sql Reference reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: Data Types; Experimental Features.

## Data Types

### Data Types

 Turso follows SQLite's dynamic typing model: the type is attached to each stored value, while ordinary columns do not require one fixed type unless the table is `STRICT`. Stored values fall into five storage classes. For `STRICT` tables, Turso adds custom types, composite types, and native array types on top of that model.

### Storage Classes

| Storage Class | Description |
| --- | --- |
| NULL | The NULL value |
| INTEGER | Signed integer storage uses 1, 2, 3, 4, 6, or 8 bytes, selected according to the value's magnitude |
| REAL | IEEE 754 floating-point value using 8 bytes |
| TEXT | A UTF-8 encoded string |
| BLOB | Binary bytes kept unchanged from the provided input |

```
SELECT typeof(NULL);       -- 'null'
SELECT typeof(42);         -- 'integer'
SELECT typeof(3.14);       -- 'real'
SELECT typeof('hello');    -- 'text'
SELECT typeof(x'CAFE');    -- 'blob'

```

### Type Affinity

 Declaring a type name on a column gives that column a **type affinity** in Turso. This affinity guides storage conversion choices rather than acting as a hard rule, except in `STRICT` tables. The affinity rules match SQLite.

### Affinity Determination Rules

 Turso derives a column's type affinity from its declared type name by checking the following rules sequentially:
| Rule | Condition | Affinity | Examples |
| --- | --- | --- | --- |
| 1 | Type name contains “INT” | INTEGER | `INT`, `INTEGER`, `BIGINT`, `SMALLINT`, `TINYINT` |
| 2 | Type name includes "CHAR", "CLOB", or "TEXT" | TEXT | `TEXT`, `VARCHAR(255)`, `CLOB`, `CHARACTER(20)` |
| 3 | Type name includes "BLOB", or the column has no declared type | BLOB | `BLOB`, (no type) |
| 4 | Type name includes "REAL", "FLOA", or "DOUB" | REAL | `REAL`, `FLOAT`, `DOUBLE`, `DOUBLE PRECISION` |
| 5 | Otherwise | NUMERIC | `NUMERIC`, `DECIMAL`, `BOOLEAN`, `DATE` |

```
-- Type affinity is a suggestion, not a constraint
CREATE TABLE flexible (
    id INTEGER,
    name TEXT,
    data BLOB
);

-- This works - TEXT value in an INTEGER column
INSERT INTO flexible VALUES ('not a number', 42, 'text in blob');
SELECT typeof(id), typeof(name), typeof(data) FROM flexible;
-- 'text', 'integer', 'text'

```

### Type Conversions

 On insert, Turso may coerce the incoming value according to the target column's affinity:
- **INTEGER affinity**: TEXT or REAL input that has integer form is stored by Turso as INTEGER
- **REAL affinity**: TEXT input with numeric form is changed to REAL; integer input is also changed to REAL by Turso
- **NUMERIC affinity**: Turso attempts INTEGER conversion, then REAL conversion, and otherwise leaves the value as TEXT
- **TEXT affinity**: INTEGER and REAL inputs are rendered into text form
- **BLOB affinity**: Turso leaves the value unconverted

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

### Array Types

 **Turso Extension**: Array types are provided by Turso and do not exist in SQLite. Array columns require `STRICT` tables and hold ordered value collections. To declare one, append `[]` to a base type name:

```
CREATE TABLE sensors (
    id INTEGER PRIMARY KEY,
    readings REAL[],
    labels TEXT[],
    flags INTEGER[]
) STRICT;

```

 Use repeated bracket pairs to declare multi-dimensional arrays:

```
CREATE TABLE matrices (
    id INTEGER PRIMARY KEY,
    data INTEGER[][]
) STRICT;

```

 Insert arrays either with the `ARRAY[...]` constructor or by providing JSON text:

```
INSERT INTO sensors VALUES (1, ARRAY[1.5, 2.5, 3.5], '["a","b"]', '[0, 1, 1]');

```

 Array output is formatted as JSON arrays. Turso checks element values against the declared base type, so an `INTEGER[]` column, for example, refuses text elements that are not numeric. For array functions, operators, and subscript syntax, see [Array Functions](https://docs.turso.tech/sql-reference/functions/array).

### Inspecting Types

 Show every available type, including built-in and custom types:

```
PRAGMA list_types;

```

 The `sqlite_turso_types` virtual table exposes the same type information:

```
SELECT name, sql FROM sqlite_turso_types;

```

### Comparison and Sorting

 Turso follows SQLite's comparison behavior. When storage classes differ, ordering is:

```
NULL < INTEGER/REAL < TEXT < BLOB

```

- NULL sorts before all non-NULL values
- INTEGER and REAL are ordered by numeric comparison
- TEXT comparison uses the column's collation sequence, with BINARY as the default
- BLOB comparison is performed with `memcmp()`

```
SELECT 1 < 2;          -- 1 (true)
SELECT 'abc' < 'abd';  -- 1 (true)
SELECT 1 < '2';        -- 1 (true, numeric < text)
SELECT NULL < 1;        -- NULL (any comparison with NULL yields NULL)
SELECT NULL IS NULL;    -- 1 (use IS to test for NULL)

```

### See Also

- [CREATE TABLE](https://docs.turso.tech/sql-reference/statements/create-table) covers column definitions and constraints
- [CREATE TYPE](https://docs.turso.tech/sql-reference/statements/create-type) covers defining custom types
- [Array Functions](https://docs.turso.tech/sql-reference/functions/array) covers array creation, updates, and operators
- [Expressions](https://docs.turso.tech/sql-reference/expressions) covers CAST expressions and type conversion behavior
- [Compatibility](https://docs.turso.tech/sql-reference/compatibility) covers Turso differences from SQLite

## Experimental Features

### Experimental Features

 Certain Turso capabilities remain experimental and have to be enabled explicitly before they can be used. They are suitable for testing, but their API can change in later releases.

### Experimental Features List

| Feature | Flag | Description |
| --- | --- | --- |
| Views | `views` | CREATE VIEW plus CREATE MATERIALIZED VIEW |
| Custom Types | `custom_types` | CREATE TYPE, DROP TYPE, CREATE DOMAIN, and DROP DOMAIN support for `STRICT` tables |
| Triggers | `triggers` | CREATE TRIGGER and DROP TRIGGER |
| Encryption | `encryption` | At-rest encryption configured with PRAGMA cipher / hexkey |
| Index Methods | `index_method` | CREATE INDEX ... USING support for FTS and custom index types |
| Autovacuum | `autovacuum` | Automatic database file compaction |
| Vacuum | `vacuum` | In-place VACUUM compaction |
| Attach | `attach` | ATTACH DATABASE and DETACH DATABASE |
| Multi-Process WAL | `multiprocess_wal` | Multi-Process Access - lets OS processes share one database file through a shared WAL coordinator |

### CLI

 Start `tursodb` with `--experimental-<feature>` flags:

```
tursodb --experimental-views --experimental-triggers database.db

```

 Multiple flags can be combined:

```
tursodb \
  --experimental-views \
  --experimental-custom-types \
  --experimental-triggers \
  --experimental-encryption \
  --experimental-index-method \
  --experimental-vacuum \
  database.db

```

### Rust SDK

 The Rust binding exposes configuration through a fluent `Builder` API:

```
use turso::Builder;

let db = Builder::new_local("database.db")
    .experimental_encryption(true)
    .experimental_triggers(true)
    .experimental_materialized_views(true)
    .experimental_custom_types(true)
    .experimental_index_method(true)
    .experimental_vacuum(true)
    .experimental_attach(true)
    .experimental_multiprocess_wal(true)
    .build()?;

```

### Python SDK

 Provide a comma-separated string through the `experimental_features` parameter:

```
import turso

conn = turso.connect(
    "database.db",
    experimental_features="views,triggers,custom_types,vacuum",
)

```

### JavaScript / Node.js SDK

 Supply an array of feature strings in the `experimental` option:

```
import { Database } from "@tursodatabase/libsql";

const db = new Database("file:database.db", {
    experimental: ["views", "triggers", "custom_types", "encryption", "vacuum"],
});

```

### Go SDK

 Assign a comma-separated string to the `ExperimentalFeatures` field:

```
db, err := turso.NewDatabase(turso.TursoDatabaseConfig{
    Path: "database.db",
    ExperimentalFeatures: "views,triggers,custom_types,vacuum",
})

```

 DSN query parameters are also supported by the Go driver:

```
db, err := sql.Open("turso", "database.db?experimental=views,triggers,vacuum")

```

### Java SDK

 The Java binding currently exposes encryption with a dedicated method:

```
TursoDB db = TursoDB.openWithEncryption(
    "database.db",
    OpenFlags.CREATE,
    "aegis256",
    "your-hex-key-here"
);

```

### .NET SDK

 The .NET binding currently exposes encryption through the C FFI:

```
var db = Database.OpenWithEncryption(
    "database.db",
    "aegis256",
    "your-hex-key-here"
);

```

### See Also

- [Compatibility](https://docs.turso.tech/sql-reference/compatibility) provides the complete feature support matrix
