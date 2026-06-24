# Scalar

> Sql Reference reference. Original prose; identifiers preserved verbatim.

### Scalar Functions

 Scalar functions take one or more inputs and produce one value. Use them anywhere expressions are allowed, including SELECT column lists, WHERE filters, ORDER BY and GROUP BY clauses, HAVING predicates, CHECK constraints, and DEFAULT expressions.

### Function Reference

### Math Functions

| Function | Description |
| --- | --- |
| `abs(X)` | Computes X without its sign. Result type is INTEGER when X is INTEGER, REAL when X is REAL, and NULL when X is NULL. |
| `max(X, Y, ...)` | Chooses the argument whose value is highest |
| `min(X, Y, ...)` | Chooses the argument whose value is lowest |
| `random()` | Produces a signed 64-bit integer selected pseudo-randomly |
| `round(X)` | Rounds X to the closest whole number |
| `round(X, Y)` | Rounds X using Y digits after the decimal point |
| `sign(X)` | Maps X to -1, 0, or 1 depending on whether it is negative, zero, or positive |

### String Functions

| Function | Description |
| --- | --- |
| `char(X1, X2, ..., XN)` | Builds a string from characters whose Unicode code points are X1 through XN |
| `concat(X, ...)` | Joins every argument after converting to strings, while ignoring NULL inputs |
| `concat_ws(SEP, X, ...)` | Joins arguments separated by SEP, leaving out any NULL input |
| `format(FORMAT, ...)` | Builds text from printf-style format specifiers |
| `hex(X)` | Gives X as uppercase hexadecimal text |
| `instr(X, Y)` | Reports the 1-based location where Y first appears in X, or 0 when absent |
| `length(X)` | Reports character count for strings, and byte count for blobs |
| `lower(X)` | Creates a lowercase version of string X |
| `ltrim(X)` | Removes leading whitespace from X |
| `ltrim(X, Y)` | Strips from the front of X any characters present in Y |
| `octet_length(X)` | Reports X's byte length |
| `printf(FORMAT, ...)` | Alias for `format()`. Produces a formatted string |
| `quote(X)` | Produces the SQL literal form for X |
| `replace(X, Y, Z)` | Copies X while substituting Z for each occurrence of Y |
| `rtrim(X)` | Removes trailing whitespace from X |
| `rtrim(X, Y)` | Strips from the end of X any characters present in Y |
| `soundex(X)` | Produces the Soundex code for string X |
| `substr(X, Y)` | Extracts part of X beginning at 1-based position Y |
| `substr(X, Y, Z)` | Extracts Z characters from X beginning at position Y |
| `substring(X, Y)` | Alias for `substr(X, Y)` |
| `substring(X, Y, Z)` | Alias for `substr(X, Y, Z)` |
| `trim(X)` | Strips whitespace from both ends of X |
| `trim(X, Y)` | Strips from both ends of X any characters listed in Y |
| `unhex(X)` | Parses hexadecimal string X into a blob. If X includes non-hex characters, result is NULL |
| `unhex(X, Y)` | Same as `unhex(X)`, except characters listed in Y are ignored while reading X |
| `unicode(X)` | Reports the Unicode code point for string X's first character |
| `upper(X)` | Creates an uppercase version of string X |
| `zeroblob(N)` | Creates a blob containing N zero bytes |

### Conditional Functions

| Function | Description |
| --- | --- |
| `coalesce(X, Y, ...)` | Chooses the first argument that is not NULL. If none exists, result is NULL |
| `ifnull(X, Y)` | Yields X when X is not NULL; otherwise yields Y. Same result as `coalesce(X, Y)` |
| `iif(X, Y, Z)` | Yields Y when X evaluates true (non-zero); otherwise yields Z |
| `if(X, Y, Z)` | Alias for `iif(X, Y, Z)` |
| `nullif(X, Y)` | Yields NULL when X and Y are equal; otherwise yields X |

### Type Functions

| Function | Description |
| --- | --- |
| `typeof(X)` | Reports X's type as one of these strings: `"null"`, `"integer"`, `"real"`, `"text"`, or `"blob"` |

### Pattern Matching Functions

| Function | Description |
| --- | --- |
| `glob(X, Y)` | Yields 1 when string Y fits glob pattern X; otherwise yields 0. Matching is case-sensitive |
| `like(X, Y)` | Yields 1 when string Y fits LIKE pattern X; otherwise yields 0. ASCII matching ignores case |
| `like(X, Y, Z)` | Same behavior as `like(X, Y)`, with Z supplying the escape character |

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

### format(FORMAT, …) and printf(FORMAT, …)

 Builds a formatted string from printf-style format specifiers. `printf` is another name for `format`.
| Specifier | Description |
| --- | --- |
| `%d` | Signed integer |
| `%f` | Floating-point |
| `%s` | String |
| `%x` | Lowercase hexadecimal integer |
| `%X` | Uppercase hexadecimal integer |
| `%o` | Octal integer |
| `%e` | Scientific notation |
| `%g` | General floating-point (shortest representation) |
| `%%` | Literal percent sign |

```
SELECT format('Hello, %s! You are #%d.', 'Alice', 1);
-- 'Hello, Alice! You are #1.'

SELECT printf('%.2f%%', 99.5);
-- '99.50%'

SELECT format('0x%08X', 255);
-- '0x000000FF'

```

### glob(X, Y)

 Yields 1 when string Y matches glob pattern X, or 0 when it does not. Glob matching is case-sensitive and treats `*` as any character sequence, `?` as one character, and `[...]` as a character class.

```
SELECT glob('*.txt', 'readme.txt');   -- 1
SELECT glob('*.TXT', 'readme.txt');   -- 0 (case-sensitive)
SELECT glob('H?llo', 'Hello');        -- 1

```

 The scalar function `glob(X, Y)` corresponds to the operator form `Y GLOB X`. Its parameters are in the opposite order from the operator syntax.

### hex(X) and unhex(X)

 `hex` emits uppercase hexadecimal text for its input. For text, the UTF-8 bytes are encoded; for blobs, each byte is encoded; for integers, the numeric value is represented in hex. `unhex` turns a hexadecimal string into a blob. It returns NULL for non-hex input unless a second argument names characters that should be ignored.

```
SELECT hex('ABC');             -- '414243'
SELECT hex(255);               -- 'FF'
SELECT hex(x'CAFE');           -- 'CAFE'

SELECT unhex('48454C4C4F');    -- x'48454C4C4F' (blob for 'HELLO')
SELECT unhex('48-45-4C', '-'); -- x'48454C' (ignoring dashes)
SELECT unhex('ZZZZ');          -- NULL (invalid hex)

```

### iif(X, Y, Z) and if(X, Y, Z)

 Yields Y when X is true (non-zero and non-NULL); otherwise yields Z. `if` is another name for `iif`.

```
SELECT iif(1 > 0, 'yes', 'no');       -- 'yes'
SELECT iif(NULL, 'yes', 'no');         -- 'no'
SELECT if(10 > 5, 'big', 'small');     -- 'big'

```

### instr(X, Y)

 Reports the 1-based location of the first string Y inside string X. If Y is absent from X, the result is 0. If either argument is NULL, the result is NULL.

```
SELECT instr('Hello World', 'World');   -- 7
SELECT instr('Hello World', 'xyz');     -- 0
SELECT instr('abcabc', 'bc');           -- 2

```

### length(X) and octet_length(X)

 `length` reports characters for text values and bytes for blob values. NULL input produces NULL. Numeric input is first represented as text and measured from that representation. `octet_length` reports byte length for every type.

```
SELECT length('Hello');          -- 5
SELECT length(x'AABBCC');        -- 3 (bytes for blob)
SELECT length(12345);            -- 5 (text representation)
SELECT octet_length('Hello');    -- 5 (ASCII, 1 byte per char)

```

### like(X, Y) and like(X, Y, Z)

 Yields 1 when string Y matches LIKE pattern X, or 0 when it does not. `%` stands for any character sequence, while `_` stands for one character. ASCII letters compare without regard to case. Optional argument Z supplies the escape character.

```
SELECT like('%ello%', 'Hello World');     -- 1
SELECT like('H_llo', 'Hello');            -- 1
SELECT like('10\%%', '10% discount', '\'); -- 1 (escaped %)

```

 The scalar function `like(X, Y)` corresponds to the operator form `Y LIKE X`. Its parameters are in the opposite order from the operator syntax.

### lower(X), upper(X)

 `lower` copies string X with ASCII letters changed to lowercase. `upper` changes ASCII letters to uppercase.

```
SELECT lower('Hello World');   -- 'hello world'
SELECT upper('Hello World');   -- 'HELLO WORLD'

```

### ltrim(X), rtrim(X), trim(X)

 These functions strip characters from string ends. With no second argument, whitespace is stripped. With second argument Y, any character contained in Y may be stripped.

```
-- Whitespace trimming
SELECT ltrim('   Hello');          -- 'Hello'
SELECT rtrim('Hello   ');          -- 'Hello'
SELECT trim('   Hello   ');        -- 'Hello'

-- Character trimming
SELECT ltrim('xxxHello', 'x');     -- 'Hello'
SELECT rtrim('Helloyyy', 'y');     -- 'Hello'
SELECT trim('***Hello***', '*');   -- 'Hello'
SELECT trim('abcHelloabc', 'abc'); -- 'Hello' (removes any of a, b, or c)

```

### max(X, Y, …) and min(X, Y, …)

 With multiple arguments, `max` chooses the largest value and `min` chooses the smallest. Comparisons follow the usual SQLite rules. A NULL argument makes the result NULL.

```
SELECT max(1, 5, 3);          -- 5
SELECT min(1, 5, 3);          -- 1
SELECT max('apple', 'banana'); -- 'banana'
SELECT min(10, NULL, 3);      -- NULL

```

 With multiple arguments, `max()` and `min()` are scalar functions. In an aggregate query using one argument, such as `SELECT max(salary) FROM employees`, they operate as [aggregate functions](https://docs.turso.tech/sql-reference/functions/aggregate).

### nullif(X, Y)

 Yields NULL when X equals Y; otherwise yields X. This helps turn sentinel values into NULL.

```
SELECT nullif(0, 0);        -- NULL
SELECT nullif(5, 0);        -- 5
SELECT nullif('N/A', 'N/A'); -- NULL
SELECT nullif('hello', '');  -- 'hello'

```

### quote(X)

 Produces SQL literal text representing value X. Strings use single quotes and escaping. BLOBs become hex literals. NULL produces the string `'NULL'`. Numbers are emitted unchanged.

```
SELECT quote('it''s');       -- '''it''s'''
SELECT quote(42);            -- '42'
SELECT quote(NULL);          -- 'NULL'
SELECT quote(x'CAFE');       -- 'X''CAFE'''

```

### random() and randomblob(N)

 `random` produces a pseudo-random signed 64-bit integer. `randomblob` produces a blob containing N pseudo-random bytes.

```
SELECT random();           -- e.g., -4520312828827489743
SELECT hex(randomblob(4)); -- e.g., 'A1B2C3D4' (4 random bytes)
SELECT abs(random()) % 100; -- random number between 0 and 99

```

### replace(X, Y, Z)

 Copies string X and changes each occurrence of string Y to string Z. When Y is empty, X is left unchanged.

```
SELECT replace('Hello World', 'World', 'Turso');   -- 'Hello Turso'
SELECT replace('aabbcc', 'bb', 'XX');               -- 'aaXXcc'
SELECT replace('2024-01-15', '-', '/');              -- '2024/01/15'

```

### round(X) and round(X, Y)

 Rounds X to Y digits after the decimal point. Without Y, the default is 0. The result type is always REAL.

```
SELECT round(3.7);         -- 4.0
SELECT round(3.14159, 2);  -- 3.14
SELECT round(2.5);         -- 3.0
SELECT round(-2.5);        -- -3.0

```

### sign(X)

 Yields -1 for negative values, 0 for zero, and 1 for positive values. If X is NULL, the result is NULL.

```
SELECT sign(-42);    -- -1
SELECT sign(0);      -- 0
SELECT sign(3.14);   -- 1
SELECT sign(NULL);   -- NULL

```

### substr(X, Y) and substr(X, Y, Z)

 Extracts part of X beginning at the Y-th character, using 1-based indexing. When Z is supplied, no more than Z characters are returned. Negative Y starts counting from the string's end. `substring` is another name for it.

```
SELECT substr('Hello World', 7);       -- 'World'
SELECT substr('Hello World', 1, 5);    -- 'Hello'
SELECT substr('Hello World', -5);      -- 'World'
SELECT substring('Hello', 2, 3);       -- 'ell'

```

### typeof(X)

 Reports X's storage class as a lowercase string: `"null"`, `"integer"`, `"real"`, `"text"`, or `"blob"`.

```
SELECT typeof(42);         -- 'integer'
SELECT typeof(3.14);       -- 'real'
SELECT typeof('hello');    -- 'text'
SELECT typeof(NULL);       -- 'null'
SELECT typeof(x'CAFE');    -- 'blob'
SELECT typeof(1 + 1.0);   -- 'real'

```

### unicode(X)

 Reports the Unicode code point for the first character in string X. The result is NULL when X is NULL or empty.

```
SELECT unicode('A');     -- 65
SELECT unicode('Hello'); -- 72 (code point of 'H')

```

### soundex(X)

 Produces the four-character Soundex code for string X. Soundex represents English words by approximate pronunciation, which can help with fuzzy name matching.

```
SELECT soundex('Robert');    -- 'R163'
SELECT soundex('Rupert');    -- 'R163'
SELECT soundex('Smith');     -- 'S530'
SELECT soundex('Smythe');    -- 'S530'

```

### zeroblob(N)

 Creates a blob made of N zero bytes (0x00). It is useful when reserving blob storage ahead of time.

```
SELECT length(zeroblob(10));    -- 10
SELECT hex(zeroblob(4));        -- '00000000'

```

### last_insert_rowid()

 Reports the rowid from the latest successful INSERT on the current database connection. If no INSERT has occurred, the result is 0.

```
CREATE TABLE users (id INTEGER PRIMARY KEY, name TEXT);
INSERT INTO users (name) VALUES ('Alice');
SELECT last_insert_rowid();  -- 1
INSERT INTO users (name) VALUES ('Bob');
SELECT last_insert_rowid();  -- 2

```

### changes() and total_changes()

 `changes` reports row changes from the most recent INSERT, UPDATE, or DELETE statement. `total_changes` reports cumulative row changes since the database connection opened.

```
CREATE TABLE t (x INTEGER);
INSERT INTO t VALUES (1), (2), (3);
SELECT changes();        -- 3

DELETE FROM t WHERE x > 1;
SELECT changes();        -- 2
SELECT total_changes();  -- 5 (3 inserted + 2 deleted)

```

### sqlite_version()

 Reports the SQLite-compatible version string.

```
SELECT sqlite_version();  -- '3.42.0'

```

### load_extension(X)

 Opens a Turso-native extension from the shared library at path X.

```
SELECT load_extension('./my_extension');

```

 The database connection must allow extension loading. For build and load details, see the [Extensions documentation](https://docs.turso.tech/docs/extensions).

### See Also

- [Aggregate Functions](https://docs.turso.tech/sql-reference/functions/aggregate) covers functions applied across row groups
- [Expressions](https://docs.turso.tech/sql-reference/expressions) covers operator syntax, CAST, CASE, and subqueries
- [Data Types](https://docs.turso.tech/sql-reference/data-types) covers storage classes and type affinity
- [SELECT](https://docs.turso.tech/sql-reference/statements/select) covers use of functions inside queries
