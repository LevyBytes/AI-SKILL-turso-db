# Custom type JSON unsigned and decimal examples

[Context index](custom-types-and-view-reference.md) · [Setup and preceding context](custom-type-base-encoding-and-decoding.md) · [Previous phase](custom-array-types-and-monetary-values.md)

### JSON Validation

 Check that inserted values contain valid JSON:

```
CREATE TYPE json BASE text
    ENCODE json(value)
    DECODE value;

CREATE TABLE configs (
    id INTEGER PRIMARY KEY,
    data json
) STRICT;

INSERT INTO configs VALUES (1, '{"key": "value"}');
-- succeeds

INSERT INTO configs VALUES (2, 'not json');
-- Error: malformed JSON

```

### Unsigned Integer with Operators

 A non-negative integer type that defines arithmetic and comparison operators:

```
CREATE TYPE uint BASE integer
    ENCODE CASE
        WHEN value >= 0 THEN value
        ELSE RAISE(ABORT, 'uint must be non-negative')
    END
    DECODE value
    OPERATOR '<'
    OPERATOR '=';

CREATE TABLE counters (
    id INTEGER PRIMARY KEY,
    count uint DEFAULT 0
) STRICT;

INSERT INTO counters VALUES (1, 5);
SELECT * FROM counters WHERE count > 0 ORDER BY count;

```

### Fixed-Point Decimal

 For precise decimal arithmetic, use the built-in `numeric` type:

```
CREATE TABLE transactions (
    id INTEGER PRIMARY KEY,
    amount numeric(10, 2),
    tax numeric(10, 2)
) STRICT;

INSERT INTO transactions VALUES (1, 99.99, 8.25);
SELECT amount + tax FROM transactions;
-- 108.24

```

### See Also

- [DROP TYPE](https://docs.turso.tech/sql-reference/statements/drop-type) explains how to remove custom types
- [CREATE DOMAIN](https://docs.turso.tech/sql-reference/statements/create-domain) covers constrained type aliases without ENCODE/DECODE logic
- [Data Types](https://docs.turso.tech/sql-reference/data-types) gives an overview of type affinity and the type system
- [Array Types](https://docs.turso.tech/sql-reference/data-types#array-types) describes native array columns (`INTEGER[]`, `TEXT[]`, etc.)
- [Array Functions](https://docs.turso.tech/sql-reference/functions/array) covers creating, changing, and operating on arrays
- [CREATE TABLE](https://docs.turso.tech/sql-reference/statements/create-table) documents STRICT table definitions
