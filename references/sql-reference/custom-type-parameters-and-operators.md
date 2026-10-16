# Custom type parameters and operators

[Context index](custom-types-and-view-reference.md) · [Setup and preceding context](custom-type-base-encoding-and-decoding.md) · [Continue](custom-type-defaults-validation-and-casts.md)

### Parameters

 Custom types may declare parameters that ENCODE and DECODE can reference. Declare them in parentheses after the type name; every parameter has a name and may include a type annotation.

```
CREATE TYPE varchar(value text, maxlen integer) BASE text
    ENCODE CASE
        WHEN length(value) <= maxlen THEN value
        ELSE RAISE(ABORT, 'value too long for varchar')
    END
    DECODE value;

```

 For a column that uses a parametric type, place the arguments in parentheses after the type name:

```
CREATE TABLE users (
    id INTEGER PRIMARY KEY,
    name varchar(100),
    bio varchar(500)
) STRICT;

```

 The initial parameter is always the input `value`. Later parameters correspond, in order, to the arguments from the column type declaration.

### OPERATOR

 OPERATOR binds SQL operators to functions, allowing custom-typed columns to work in comparisons, arithmetic expressions, and sort ordering.

```
OPERATOR 'op' function-name

```

| Component | Description |
| --- | --- |
| `'op'` | The operator symbol as a string literal: `'+'`, `'-'`, `'*'`, `'/'`, `'<'`, `'='`, etc. |
| `function-name` | The name of a SQL function to call when this operator is used. Omit to use the base type’s native comparison. |

 With an operator definition present, an expression such as `column + 1` becomes `function_name(column, 1)`.

#### Ordering with OPERATOR ’<’

 The `<` operator has special behavior because it defines sorting for this type in ORDER BY, MIN, MAX, and CREATE INDEX.
- **`OPERATOR '<'`** (no function name): Orders by the base type's native comparison. This is enough when the encoded representation already sorts correctly (e.g., text dates in ISO 8601 format).
- **`OPERATOR '<' my_compare`**: Orders using `my_compare(a, b)` as the comparator. The function must yield a negative integer for `a < b`, zero for `a = b`, and a positive integer for `a > b`.
- **No `<` operator**: Values of the type are not allowed in ORDER BY or CREATE INDEX.

```
-- Use base type comparison for ordering
CREATE TYPE cents BASE integer
    ENCODE value * 100
    DECODE value / 100
    OPERATOR '<';

-- Full operator set for a numeric type
CREATE TYPE uint BASE integer
    ENCODE CASE WHEN value >= 0 THEN value ELSE RAISE(ABORT, 'uint must be non-negative') END
    DECODE value
    OPERATOR '+' uint_add
    OPERATOR '-' uint_sub
    OPERATOR '*' uint_mul
    OPERATOR '<'
    OPERATOR '=';

```

 When `=` exists, it is also used to produce `!=`. The `<` definition automatically supplies `>`, `>=`, and `<=` by reversing arguments or negating the result.
