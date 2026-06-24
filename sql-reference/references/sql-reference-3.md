# Expressions, Extensions

> Sql Reference reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: Expressions; Extensions.

## Expressions

### Expressions

 An expression combines values, operators, and functions, and Turso evaluates that combination into a result. You can use expressions in SQL contexts such as SELECT column lists, WHERE filters, ORDER BY, GROUP BY, HAVING, CHECK constraints, and DEFAULT clauses.

### Literals

### Numeric Literals

```
SELECT 42;           -- integer literal
SELECT -17;          -- negative integer
SELECT 3.14;         -- real (floating-point) literal
SELECT 2.5e10;       -- scientific notation
SELECT 0xFF;         -- hexadecimal integer (255)

```

### String Literals

 Use single quotes around string literals. Inside a string, represent one literal single quote by writing two single quotes in a row:

```
SELECT 'hello world';        -- text literal
SELECT 'it''s a test';       -- embedded single quote
SELECT '';                   -- empty string

```

### Blob Literals

 Blob literals use hexadecimal text with a leading `x` or `X`:

```
SELECT x'CAFEBABE';          -- blob literal
SELECT X'48454C4C4F';        -- blob literal (case insensitive prefix)

```

### NULL Literal

```
SELECT NULL;                 -- null value

```

### Boolean Literals

 Turso has no dedicated boolean storage type. Represent false with integer `0` and true with integer `1`:

```
SELECT 1;    -- true
SELECT 0;    -- false

```

### Operators

### Arithmetic Operators

| Operator | Description | Example | Result |
| --- | --- | --- | --- |
| `+` | Addition | `3 + 4` | `7` |
| `-` | Subtraction | `10 - 3` | `7` |
| `*` | Multiplication | `3 * 4` | `12` |
| `/` | Division | `10 / 3` | `3` (integer division) |
| `-` (unary) | Negation | `-5` | `-5` |
| `+` (unary) | No-op | `+5` | `5` |

 Integer division drops the fractional part toward zero. For floating-point results, use `CAST` or multiply by `1.0`:

```
SELECT 10 / 3;           -- 3 (integer division)
SELECT 10 / 3.0;         -- 3.333... (real division)
SELECT CAST(10 AS REAL) / 3;  -- 3.333...

```

### Comparison Operators

| Operator | Description | Example |
| --- | --- | --- |
| `=` or `==` | Equal | `x = 5` |
| `!=` or `<>` | Not equal | `x != 5` |
| `<` | Less than | `x < 5` |
| `>` | Greater than | `x > 5` |
| `<=` | Less than or equal | `x <= 5` |
| `>=` | Greater than or equal | `x >= 5` |

 Each comparison operator produces `1` (true), `0` (false), or `NULL` when either side is NULL.

### Logical Operators

| Operator | Description | Example |
| --- | --- | --- |
| `AND` | Logical AND | `x > 0 AND x < 10` |
| `OR` | Logical OR | `x = 1 OR x = 2` |
| `NOT` | Logical NOT | `NOT x = 5` |

### Bitwise Operators

| Operator | Description | Example | Result |
| --- | --- | --- | --- |
| `&` | Bitwise AND | `5 & 3` | `1` |
| `|` | Bitwise OR | `5 | 3` | `7` |
| `~` | Bitwise NOT | `~5` | `-6` |
| `<<` | Left shift | `1 << 4` | `16` |
| `>>` | Right shift | `16 >> 2` | `4` |

### String Concatenation

| Operator | Description | Example | Result |
| --- | --- | --- | --- |
| `||` | Concatenation | `'hello' || ' ' || 'world'` | `'hello world'` |

 If an operand is an array, `||` instead concatenates arrays. See Array Operators.

### Array Operators

 **Turso Extension**: Array operators are specific to Turso and are not part of SQLite.
| Operator | Description | Example | Result |
| --- | --- | --- | --- |
| `@>` | Contains all | `ARRAY[1,2,3] @> ARRAY[1,2]` | `1` |
| `&&` | Overlaps | `ARRAY[1,2,3] && ARRAY[3,4,5]` | `1` |
| `||` | Array concat | `ARRAY[1,2] || ARRAY[3,4]` | `[1,2,3,4]` |
| `=`, `!=`, `<`, `>`, `<=`, `>=` | Comparison | `ARRAY[1,2] < ARRAY[1,3]` | `1` |

 The `@>` operator checks whether every element in the right array is present in the left array. The `&&` operator checks whether two arrays have at least one shared element. Each operator returns `1` (true) or `0` (false). Arrays are compared element by element, moving left to right. When all checked elements match, the shorter array sorts before the longer array.

```
SELECT ARRAY[1,2,3] @> ARRAY[1,3];     -- 1 (contains both 1 and 3)
SELECT ARRAY[1,2] && ARRAY[3,4];       -- 0 (no common elements)
SELECT ARRAY[1,2] < ARRAY[1,2,3];      -- 1 (shorter is less)

```

 For complete details, see [Array Functions](https://docs.turso.tech/sql-reference/functions/array).

### CAST Expression

 A CAST expression changes a value into the requested type.

```
CAST(expression AS type-name)

```

| Parameter | Type | Description |
| --- | --- | --- |
| expression | any | The value to convert |
| type-name | type | The target type name |

```
SELECT CAST(3.7 AS INTEGER);    -- 3 (truncates toward zero)
SELECT CAST(42 AS TEXT);         -- '42'
SELECT CAST('123' AS INTEGER);   -- 123
SELECT CAST('abc' AS INTEGER);   -- 0
SELECT CAST(NULL AS INTEGER);    -- NULL

```

 **Turso Extension**: In STRICT tables that use custom types, `CAST(value AS custom_type)` runs the ENCODE function for that custom type. See [CREATE TYPE](https://docs.turso.tech/sql-reference/statements/create-type) for more information.

### COLLATE Expression

 A COLLATE expression chooses the collation sequence used for comparing strings.

```
expression COLLATE collation-name

```

 Built-in collation sequences:
| Collation | Description |
| --- | --- |
| `BINARY` | Byte-by-byte comparison (default) |
| `NOCASE` | ASCII comparison that ignores case |
| `RTRIM` | Same as BINARY, except trailing spaces are disregarded |

```
SELECT 'ABC' = 'abc';                        -- 0 (BINARY comparison)
SELECT 'ABC' = 'abc' COLLATE NOCASE;          -- 1 (case-insensitive)
SELECT 'abc ' = 'abc' COLLATE RTRIM;           -- 1 (trailing space ignored)

SELECT name FROM users ORDER BY name COLLATE NOCASE;

```

### Pattern Matching

### LIKE Operator

 The LIKE operator matches patterns without case sensitivity for ASCII characters. `%` stands for any character sequence, while `_` stands for one character.

```
expression [NOT] LIKE pattern [ESCAPE escape-char]

```

```
SELECT 'Hello World' LIKE 'hello%';         -- 1 (case-insensitive)
SELECT 'Hello World' LIKE 'H_llo%';         -- 1 (_ matches 'e')
SELECT 'Hello World' LIKE '%World';          -- 1
SELECT '10%' LIKE '10\%' ESCAPE '\';        -- 1 (escaped % literal)

```

### GLOB Operator

 The GLOB operator uses Unix-style wildcards for case-sensitive pattern matching. `*` stands for any character sequence, while `?` stands for one character.

```
expression [NOT] GLOB pattern

```

```
SELECT 'Hello' GLOB 'H*';      -- 1
SELECT 'Hello' GLOB 'h*';      -- 0 (case-sensitive)
SELECT 'Hello' GLOB 'H?llo';   -- 1
SELECT 'Hello' GLOB 'H[a-z]*'; -- 1 (character class)

```

### REGEXP Operator

 The REGEXP operator matches text with regular expressions. It needs the regexp extension, which is loaded by default.

```
expression [NOT] REGEXP pattern

```

```
SELECT 'Hello123' REGEXP '[A-Za-z]+[0-9]+';  -- 1
SELECT 'test@email.com' REGEXP '^[^@]+@[^@]+\.[^@]+$';  -- 1

```

### BETWEEN Expression

 A BETWEEN expression checks whether a value is inside an inclusive range.

```
expression [NOT] BETWEEN low AND high

```

 BETWEEN has the same meaning as `expression >= low AND expression <= high`:

```
SELECT 5 BETWEEN 1 AND 10;     -- 1
SELECT 5 NOT BETWEEN 1 AND 3;  -- 1
SELECT 'b' BETWEEN 'a' AND 'c'; -- 1

```

### IN Expression

 An IN expression checks a value against every value supplied by a list or returned from a subquery.

```
expression [NOT] IN (value1, value2, ...)
expression [NOT] IN (select-statement)

```

```
SELECT 3 IN (1, 2, 3, 4, 5);           -- 1
SELECT 'red' NOT IN ('blue', 'green');   -- 1

SELECT name FROM users
WHERE id IN (SELECT user_id FROM orders WHERE total > 100);

```

### EXISTS Expression

 An EXISTS expression produces `1` when its subquery yields one or more rows; otherwise it produces `0`.

```
[NOT] EXISTS (select-statement)

```

```
SELECT EXISTS (SELECT 1 FROM users WHERE name = 'Alice');  -- 1 if Alice exists

SELECT name FROM departments d
WHERE EXISTS (
    SELECT 1 FROM employees e
    WHERE e.department_id = d.id
);

```

### IS NULL / IS NOT NULL

 IS NULL checks whether a value is NULL. Unlike `= NULL`, which evaluates to NULL in all cases, `IS NULL` produces `1` or `0`.

```
expression IS NULL
expression IS NOT NULL

```

```
SELECT NULL IS NULL;       -- 1
SELECT NULL = NULL;        -- NULL (not 1!)
SELECT 42 IS NOT NULL;     -- 1
SELECT NULL IS NOT NULL;   -- 0

```

### IS DISTINCT FROM

 IS DISTINCT FROM compares two values while allowing NULL to participate as a comparable value.

```
expression IS [NOT] DISTINCT FROM expression

```

```
SELECT 1 IS DISTINCT FROM 2;       -- 1 (different values)
SELECT 1 IS DISTINCT FROM 1;       -- 0 (same value)
SELECT NULL IS DISTINCT FROM NULL;  -- 0 (both NULL)
SELECT NULL IS DISTINCT FROM 1;     -- 1 (NULL vs non-NULL)
SELECT 1 IS NOT DISTINCT FROM 1;    -- 1

```

### CASE Expression

 A CASE expression adds conditional branching inside SQL expressions.

### Simple CASE

```
CASE expression
    WHEN value1 THEN result1
    WHEN value2 THEN result2
    ...
    [ELSE default_result]
END

```

### Searched CASE

```
CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    ...
    [ELSE default_result]
END

```

 When no WHEN branch is selected and no ELSE branch exists, CASE evaluates to NULL.

```
-- Simple CASE
SELECT name,
    CASE status
        WHEN 'A' THEN 'Active'
        WHEN 'I' THEN 'Inactive'
        ELSE 'Unknown'
    END AS status_text
FROM users;

-- Searched CASE
SELECT name, score,
    CASE
        WHEN score >= 90 THEN 'A'
        WHEN score >= 80 THEN 'B'
        WHEN score >= 70 THEN 'C'
        ELSE 'F'
    END AS grade
FROM students;

```

### Scalar Subqueries

 A parenthesized subquery that yields one value may appear where an expression is allowed.

```
(select-statement)

```

 The subquery is required to produce one column only, and no more than one row. If it produces zero rows, the expression result is NULL.

```
SELECT name,
    (SELECT COUNT(*) FROM orders WHERE orders.user_id = users.id) AS order_count
FROM users;

SELECT * FROM products
WHERE price > (SELECT AVG(price) FROM products);

```

### RAISE Function

 The RAISE function signals an error condition. Standard SQLite allows RAISE only in triggers. Turso also permits `RAISE(ABORT, msg)` outside triggers, including in CHECK constraints, custom type ENCODE expressions, and standalone queries. The remaining forms, `RAISE(IGNORE)`, `RAISE(ROLLBACK, msg)`, and `RAISE(FAIL, msg)`, are valid only within triggers.

```
RAISE(IGNORE)
RAISE(ROLLBACK, error-message)
RAISE(ABORT, error-message)
RAISE(FAIL, error-message)

```

| Form | Description |
| --- | --- |
| `RAISE(IGNORE)` | Stop the active trigger action while allowing the statement to continue |
| `RAISE(ROLLBACK, msg)` | Stop the statement and undo the active transaction |
| `RAISE(ABORT, msg)` | Stop the current statement while keeping earlier transaction changes |
| `RAISE(FAIL, msg)` | Stop execution at the current point in the statement while preserving earlier row changes |

```
-- In a trigger
CREATE TRIGGER validate_age BEFORE INSERT ON users
BEGIN
    SELECT CASE
        WHEN NEW.age < 0 THEN RAISE(ABORT, 'age must be non-negative')
    END;
END;

-- In a custom type ENCODE expression
CREATE TYPE positive_int BASE integer
    ENCODE CASE WHEN value > 0 THEN value
                ELSE RAISE(ABORT, 'value must be positive') END
    DECODE value;

```

### Operator Precedence

 Operator evaluation follows this precedence order, starting with the highest precedence:
| Precedence | Operators |
| --- | --- |
| 1 (highest) | `~` (unary NOT), `+` (unary), `-` (unary) |
| 2 | `||` (concatenation) |
| 3 | `*`, `/` |
| 4 | `+`, `-` |
| 5 | `<<`, `>>`, `&`, `|` |
| 6 | `<`, `<=`, `>`, `>=`, `@>`, `&&` |
| 7 | `=`, `==`, `!=`, `<>`, `IS`, `IS NOT`, `IS DISTINCT FROM`, `IN`, `LIKE`, `GLOB`, `REGEXP`, `BETWEEN` |
| 8 | `NOT` |
| 9 | `AND` |
| 10 (lowest) | `OR` |

 Add parentheses when you need a different precedence:

```
SELECT 2 + 3 * 4;       -- 14 (multiplication first)
SELECT (2 + 3) * 4;     -- 20 (parentheses override)

```

### See Also

- [Data Types](https://docs.turso.tech/sql-reference/data-types) for storage classes and type affinity details
- [Scalar Functions](https://docs.turso.tech/sql-reference/functions/scalar) for built-in functions that expressions can call
- [SELECT](https://docs.turso.tech/sql-reference/statements/select) for expression use in queries

## Extensions

### Extensions

 Turso includes multiple built-in extensions for specialized SQL functions and virtual tables. You can load extensions at runtime with the `load_extension()` function.

### Loading Extensions

```
SELECT load_extension('extension_name');

```

 Turso can load Turso-native extensions. Loadable SQLite `.so`/`.dll` extensions are unsupported.

### UUID Extension

 The UUID extension includes functions to create UUIDs and operate on them. By default, UUID values are stored as 16-byte BLOBs.

### Functions

| Function | Parameters | Return Type | Description |
| --- | --- | --- | --- |
| `uuid4()` | none | BLOB | Generate a random UUID v4 |
| `uuid4_str()` | none | TEXT | Create a random UUID v4 string. Alias: `gen_random_uuid()` |
| `uuid7()` | none | BLOB | Create a time-ordered UUID v7 |
| `uuid7(seconds)` | INTEGER | BLOB | Create a UUID v7 using supplied seconds since epoch |
| `uuid7_timestamp_ms(uuid)` | BLOB | INTEGER | Read milliseconds since epoch from a UUID v7 |
| `uuid_str(uuid)` | BLOB | TEXT | Change a UUID blob into its string representation |
| `uuid_blob(uuid)` | TEXT | BLOB | Change a UUID string into its blob representation |

### Examples

```
-- Generate UUIDs
SELECT uuid4_str();
-- '550e8400-e29b-41d4-a716-446655440000'

SELECT uuid_str(uuid4());
-- 'f47ac10b-58cc-4372-a567-0e02b2c3d479'

-- UUID v7 is time-ordered (good for primary keys)
SELECT uuid_str(uuid7());
-- '0190a5c0-1234-7abc-8def-0123456789ab'

-- Extract timestamp from UUID v7
SELECT uuid7_timestamp_ms(uuid7());
-- 1720000000000

-- Use in a table
CREATE TABLE documents (
    id BLOB PRIMARY KEY DEFAULT (uuid7()),
    title TEXT
);
INSERT INTO documents (title) VALUES ('My Document');
SELECT uuid_str(id), title FROM documents;

```

### Regexp Extension

 The regexp extension supplies regular expression functions compatible with [sqlean-regexp](https://github.com/nalgeon/sqlean/blob/main/docs/regexp.md).

### Functions

| Function | Parameters | Return Type | Description |
| --- | --- | --- | --- |
| `regexp(pattern, source)` | TEXT, TEXT | INTEGER | Returns 1 if source matches pattern |
| `regexp_like(source, pattern)` | TEXT, TEXT | INTEGER | Returns 1 when source matches pattern, with the argument order reversed |
| `regexp_substr(source, pattern)` | TEXT, TEXT | TEXT | Returns the first substring that matches pattern, or NULL |
| `regexp_capture(source, pattern)` | TEXT, TEXT | TEXT | Returns the match for the first capture group |
| `regexp_capture(source, pattern, n)` | TEXT, TEXT, INTEGER | TEXT | Returns the match for the n-th capture group |
| `regexp_replace(source, pattern, replacement)` | TEXT, TEXT, TEXT | TEXT | Replace matches with replacement string |

### Examples

```
-- Pattern matching
SELECT regexp('[0-9]+', 'abc123def');
-- 1

-- The REGEXP operator uses this extension
SELECT 'hello123' REGEXP '[a-z]+[0-9]+';
-- 1

-- Extract matching substring
SELECT regexp_substr('Price: $42.50', '[0-9]+\.[0-9]+');
-- '42.50'

-- Capture groups
SELECT regexp_capture('2025-03-15', '(\d{4})-(\d{2})-(\d{2})', 1);
-- '2025'

-- Replace
SELECT regexp_replace('Hello World', '[aeiou]', '*');
-- 'H*ll* W*rld'

```

### Vector Extension

 **Turso Extension**: Native vector search supports similarity search and semantic search use cases. The vector extension includes functions to create, store, and search vector embeddings. Vectors use BLOB storage. For the full function list, see [Vector Functions](https://docs.turso.tech/sql-reference/functions/vector).

### Quick Reference

| Function | Description |
| --- | --- |
| `vector32(json)` | Create a 32-bit float vector |
| `vector64(json)` | Create a 64-bit float vector |
| `vector_distance_cos(v1, v2)` | Cosine distance |
| `vector_distance_l2(v1, v2)` | Euclidean distance |
| `vector_extract(v)` | Convert vector to JSON |
| `vector_concat(v1, v2)` | Concatenate vectors |
| `vector_slice(v, start, end)` | Extract vector slice |

### Example

```
CREATE TABLE docs (
    id INTEGER PRIMARY KEY,
    content TEXT,
    embedding BLOB
);

INSERT INTO docs VALUES (1, 'database systems', vector32('[0.1, 0.2, 0.3]'));
INSERT INTO docs VALUES (2, 'machine learning', vector32('[0.4, 0.5, 0.6]'));

-- Find similar documents
SELECT content, vector_distance_cos(embedding, vector32('[0.1, 0.25, 0.35]')) AS dist
FROM docs
ORDER BY dist
LIMIT 5;

```

### Time Extension

 The time extension works with [sqlean-time](https://github.com/nalgeon/sqlean/blob/main/docs/time.md) compatibility and adds advanced functions for manipulating time values.

### Key Functions

| Function | Parameters | Return Type | Description |
| --- | --- | --- | --- |
| `time_now()` | none | INTEGER | Current time as Unix nanoseconds |
| `time_date(y,m,d,...)` | INTEGER… | INTEGER | Create time from components |
| `time_unix(sec)` | INTEGER | INTEGER | Create time from Unix seconds |
| `time_milli(ms)` | INTEGER | INTEGER | Create time from milliseconds |
| `time_micro(us)` | INTEGER | INTEGER | Create time from microseconds |
| `time_nano(ns)` | INTEGER | INTEGER | Create time from nanoseconds |
| `time_to_unix(t)` | INTEGER | INTEGER | Convert to Unix seconds |
| `time_to_milli(t)` | INTEGER | INTEGER | Convert to milliseconds |
| `time_to_micro(t)` | INTEGER | INTEGER | Convert to microseconds |
| `time_to_nano(t)` | INTEGER | INTEGER | Convert to nanoseconds |
| `time_get_year(t)` | INTEGER | INTEGER | Extract year |
| `time_get_month(t)` | INTEGER | INTEGER | Extract month (1-12) |
| `time_get_day(t)` | INTEGER | INTEGER | Extract day (1-31) |
| `time_get_hour(t)` | INTEGER | INTEGER | Extract hour (0-23) |
| `time_get_minute(t)` | INTEGER | INTEGER | Extract minute (0-59) |
| `time_get_second(t)` | INTEGER | INTEGER | Extract second (0-59) |
| `time_get_weekday(t)` | INTEGER | INTEGER | Day of week (0=Sunday) |
| `time_get(t, field)` | INTEGER, TEXT | INTEGER | Extract named field |
| `time_add(t, d)` | INTEGER, INTEGER | INTEGER | Add duration |
| `time_add_date(t, y, m, d)` | INTEGER… | INTEGER | Add years/months/days |
| `time_sub(t, u)` | INTEGER, INTEGER | INTEGER | Difference between times |
| `time_since(t)` | INTEGER | INTEGER | Duration since time |
| `time_until(t)` | INTEGER | INTEGER | Duration until time |
| `time_trunc(t, field)` | INTEGER, TEXT | INTEGER | Truncate to field |
| `time_after(t, u)` | INTEGER, INTEGER | INTEGER | 1 if t is after u |
| `time_before(t, u)` | INTEGER, INTEGER | INTEGER | 1 if t is before u |
| `time_compare(t, u)` | INTEGER, INTEGER | INTEGER | -1, 0, or 1 |
| `time_equal(t, u)` | INTEGER, INTEGER | INTEGER | 1 if equal |
| `time_fmt_iso(t)` | INTEGER | TEXT | Format as ISO 8601 |
| `time_fmt_datetime(t)` | INTEGER | TEXT | Format as datetime |
| `time_fmt_date(t)` | INTEGER | TEXT | Format as date |
| `time_fmt_time(t)` | INTEGER | TEXT | Format as time |
| `time_parse(s)` | TEXT | INTEGER | Parse time string |

#### Duration Constants

| Function | Value |
| --- | --- |
| `dur_ns()` | 1 nanosecond |
| `dur_us()` | 1 microsecond |
| `dur_ms()` | 1 millisecond |
| `dur_s()` | 1 second |
| `dur_m()` | 1 minute |
| `dur_h()` | 1 hour |

### Example

```
-- Current time formatted
SELECT time_fmt_iso(time_now());
-- '2025-03-15T10:30:00Z'

-- Date arithmetic
SELECT time_fmt_date(time_add_date(time_now(), 0, 1, 0));
-- add 1 month to today

-- Duration since a timestamp
SELECT time_since(time_date(2025, 1, 1, 0, 0, 0)) / dur_h();
-- hours since Jan 1, 2025

```

### Full-Text Search (FTS)

 **Turso Extension**: Full-text search is backed by Tantivy and replaces SQLite's FTS3/FTS4/FTS5. Turso exposes full-text search through the FTS index method. For query syntax, tokenizers, scoring, and the full reference, see [FTS Functions](https://docs.turso.tech/sql-reference/functions/fts).

### Quick Reference

```
-- Create an FTS index
CREATE INDEX idx ON articles USING fts (title, body);

-- Search
SELECT title, fts_score('idx') AS score
FROM articles
WHERE fts_match('idx', 'database search')
ORDER BY score;

-- Highlighted results
SELECT fts_highlight('idx', 0, '<b>', '</b>') AS title
FROM articles
WHERE fts_match('idx', 'database');

```

### CSV Extension

 The CSV extension supplies a virtual table for reading data from CSV files.

### Usage

```
CREATE VIRTUAL TABLE temp.csv_data USING csv(
    filename='/path/to/data.csv',
    header=yes
);

SELECT * FROM csv_data;

```

| Parameter | Description |
| --- | --- |
| `filename` | Path to the CSV file |
| `header` | `yes` when the first row supplies column names |

### Percentile Extension

 Statistical aggregate functions that calculate percentiles.

### Functions

| Function | Parameters | Return Type | Description |
| --- | --- | --- | --- |
| `median(X)` | column | REAL | Median value (50th percentile) |
| `percentile(Y, P)` | column, REAL | REAL | P-th percentile for Y, where P is between 0 and 100 |
| `percentile_cont(Y, P)` | column, REAL | REAL | Continuous percentile (interpolated) |
| `percentile_disc(Y, P)` | column, REAL | REAL | Discrete percentile (nearest value) |

### Examples

```
CREATE TABLE scores (value REAL);
INSERT INTO scores VALUES (10), (20), (30), (40), (50);

SELECT median(value) FROM scores;
-- 30.0

SELECT percentile(value, 75) FROM scores;
-- 40.0

SELECT percentile_cont(value, 0.25) FROM scores;
-- 20.0

```

### generate_series

 The `generate_series` table-valued function produces an integer sequence.

```
SELECT value FROM generate_series(start, stop);
SELECT value FROM generate_series(start, stop, step);

```

| Parameter | Type | Description |
| --- | --- | --- |
| start | INTEGER | Starting value (inclusive) |
| stop | INTEGER | Ending value (inclusive) |
| step | INTEGER | Step increment (default: 1) |

```
SELECT value FROM generate_series(1, 5);
-- 1, 2, 3, 4, 5

SELECT value FROM generate_series(0, 10, 2);
-- 0, 2, 4, 6, 8, 10

SELECT value FROM generate_series(10, 1, -3);
-- 10, 7, 4, 1

```

### See Also

- [Vector Functions](https://docs.turso.tech/sql-reference/functions/vector) for the detailed vector function reference
- [FTS Functions](https://docs.turso.tech/sql-reference/functions/fts) for the full-text search function reference
- [Aggregate Functions](https://docs.turso.tech/sql-reference/functions/aggregate) for percentile functions used with GROUP BY
- [Compatibility](https://docs.turso.tech/sql-reference/compatibility) for extension support status
