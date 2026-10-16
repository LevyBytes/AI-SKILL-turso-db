# Scalar quoting random replacement and substrings

[Context index](scalar-function-reference.md) · [Setup and preceding context](scalar-function-category-reference.md) · [Previous phase](scalar-length-case-trimming-and-null-tests.md) · [Continue](scalar-unicode-soundex-and-system-counters.md)

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
