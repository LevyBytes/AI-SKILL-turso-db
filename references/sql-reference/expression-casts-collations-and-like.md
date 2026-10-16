# Expression casts collations and like

[Context index](expressions-and-extensions-workflows.md) · [Setup and preceding context](expression-literals-and-boolean-values.md) · [Previous phase](expression-arithmetic-comparison-and-array-operators.md) · [Continue](expression-glob-regexp-between-and-in.md)

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
