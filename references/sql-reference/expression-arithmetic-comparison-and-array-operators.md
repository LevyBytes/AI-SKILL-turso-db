# Expression arithmetic comparison and array operators

[Context index](expressions-and-extensions-workflows.md) · [Setup and preceding context](expression-literals-and-boolean-values.md) · [Continue](expression-casts-collations-and-like.md)

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
| `\|` | Bitwise OR | `5 \| 3` | `7` |
| `~` | Bitwise NOT | `~5` | `-6` |
| `<<` | Left shift | `1 << 4` | `16` |
| `>>` | Right shift | `16 >> 2` | `4` |

### String Concatenation

| Operator | Description | Example | Result |
| --- | --- | --- | --- |
| `\|\|` | Concatenation | `'hello' \|\| ' ' \|\| 'world'` | `'hello world'` |

 If an operand is an array, `||` instead concatenates arrays. See Array Operators.

### Array Operators

 **Turso Extension**: Array operators are specific to Turso and are not part of SQLite.

| Operator | Description | Example | Result |
| --- | --- | --- | --- |
| `@>` | Contains all | `ARRAY[1,2,3] @> ARRAY[1,2]` | `1` |
| `&&` | Overlaps | `ARRAY[1,2,3] && ARRAY[3,4,5]` | `1` |
| `\|\|` | Array concat | `ARRAY[1,2] \|\| ARRAY[3,4]` | `[1,2,3,4]` |
| `=`, `!=`, `<`, `>`, `<=`, `>=` | Comparison | `ARRAY[1,2] < ARRAY[1,3]` | `1` |

 The `@>` operator checks whether every element in the right array is present in the left array. The `&&` operator checks whether two arrays have at least one shared element. Each operator returns `1` (true) or `0` (false). Arrays are compared element by element, moving left to right. When all checked elements match, the shorter array sorts before the longer array.

```
SELECT ARRAY[1,2,3] @> ARRAY[1,3];     -- 1 (contains both 1 and 3)
SELECT ARRAY[1,2] && ARRAY[3,4];       -- 0 (no common elements)
SELECT ARRAY[1,2] < ARRAY[1,2,3];      -- 1 (shorter is less)

```

 For complete details, see [Array Functions](https://docs.turso.tech/sql-reference/functions/array).
