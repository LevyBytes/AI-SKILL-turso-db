# Array types inspection and sorting

[Context index](data-types-and-experimental-features.md) · [Setup and preceding context](storage-classes-affinity-and-conversions.md) · [Previous phase](builtin-struct-and-union-types.md)

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
