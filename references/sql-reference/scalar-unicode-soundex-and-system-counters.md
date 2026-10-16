# Scalar unicode soundex and system counters

[Context index](scalar-function-reference.md) · [Setup and preceding context](scalar-function-category-reference.md) · [Previous phase](scalar-quoting-random-replacement-and-substrings.md) · [Continue](scalar-extension-loading.md)

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
