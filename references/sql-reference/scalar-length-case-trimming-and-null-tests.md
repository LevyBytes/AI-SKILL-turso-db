# Scalar length case trimming and null tests

[Context index](scalar-function-reference.md) · [Setup and preceding context](scalar-function-category-reference.md) · [Previous phase](scalar-format-pattern-hex-and-conditionals.md) · [Continue](scalar-quoting-random-replacement-and-substrings.md)

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
