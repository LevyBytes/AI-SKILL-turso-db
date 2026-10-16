# Expression raise and operator precedence

[Context index](expressions-and-extensions-workflows.md) · [Setup and preceding context](expression-literals-and-boolean-values.md) · [Previous phase](expression-searched-case-and-scalar-subqueries.md)

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
| 2 | `\|\|` (concatenation) |
| 3 | `*`, `/` |
| 4 | `+`, `-` |
| 5 | `<<`, `>>`, `&`, `\|` |
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
