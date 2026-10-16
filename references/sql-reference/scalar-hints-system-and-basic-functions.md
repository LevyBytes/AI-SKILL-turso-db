# Scalar hints system and basic functions

[Context index](scalar-function-reference.md) · [Setup and preceding context](scalar-function-category-reference.md) · [Continue](scalar-format-pattern-hex-and-conditionals.md)

### Optimizer Hints

| Function | Description |
| --- | --- |
| `likelihood(X, Y)` | Returns X unchanged and tells the planner to estimate X as true at probability Y, where Y ranges from 0.0 to 1.0 |
| `likely(X)` | Returns X unchanged. Equivalent to `likelihood(X, 0.9375)` |
| `unlikely(X)` | Returns X unchanged. Equivalent to `likelihood(X, 0.0625)` |

### Blob Functions

| Function | Description |
| --- | --- |
| `hex(X)` | Gives blob or integer X as uppercase hexadecimal text |
| `randomblob(N)` | Creates an N-byte blob populated with pseudo-random data |
| `unhex(X)` | Parses hexadecimal string X into a blob |
| `zeroblob(N)` | Creates an N-byte blob filled with zeros |

### System Functions

| Function | Description |
| --- | --- |
| `last_insert_rowid()` | Reports the rowid from the latest successful INSERT on this database connection |
| `changes()` | Reports how many rows the latest INSERT, UPDATE, or DELETE changed |
| `total_changes()` | Reports how many rows have changed in total since the database connection opened |
| `sqlite_version()` | Returns the version string `"3.42.0"` |
| `sqlite_source_id()` | Provides the source identifier string for the SQLite-compatible engine |
| `load_extension(X)` | Opens a Turso-native extension from the shared library located at path X |

### Detailed Descriptions and Examples

### abs(X)

 Produces X without its sign. The result keeps the input category: INTEGER for integer inputs and REAL for floating-point inputs. If X is NULL, the result is NULL. Text that resembles an integer yields INTEGER.

```
SELECT abs(-42);       -- 42
SELECT abs(3.14);      -- 3.14
SELECT abs(0);         -- 0
SELECT abs(NULL);      -- NULL

```

### char(X1, X2, …, XN)

 Builds a string from characters whose Unicode code points are X1 through XN. Any argument outside the valid code point range becomes the Unicode replacement character (U+FFFD).

```
SELECT char(72, 101, 108, 108, 111);   -- 'Hello'
SELECT char(9731);                       -- snowman character
SELECT hex(char(0));                     -- '00'

```

### coalesce(X, Y, …)

 Chooses the first argument whose value is not NULL. When every argument is NULL, the result is NULL. At least two arguments are required.

```
SELECT coalesce(NULL, NULL, 'fallback');   -- 'fallback'
SELECT coalesce(1, 2, 3);                  -- 1
SELECT coalesce(NULL, 42);                 -- 42

```

### concat(X, …) and concat_ws(SEP, X, …)

 `concat` joins arguments as strings and omits NULL values. `concat_ws` places the separator between values that are not NULL.

```
SELECT concat('Hello', ' ', 'World');          -- 'Hello World'
SELECT concat('a', NULL, 'b');                 -- 'ab'
SELECT concat_ws(', ', 'Alice', 'Bob', NULL, 'Carol');  -- 'Alice, Bob, Carol'
SELECT concat_ws('-', 2024, 1, 15);            -- '2024-1-15'

```
