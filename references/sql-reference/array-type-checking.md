# Array type checking

[Context index](array-function-reference.md) · [Setup and preceding context](array-construction-and-string-parsing.md) · [Previous phase](array-assignment-tags-and-nested-arrays.md)

### Type checking

 In STRICT tables, array columns check element types during insert and update:

```
CREATE TABLE scores (id INTEGER PRIMARY KEY, vals INTEGER[]) STRICT;

-- OK: exact integers
INSERT INTO scores VALUES (1, '[1, 2, 3]');

-- OK: floats that are exact integers are coerced
INSERT INTO scores VALUES (2, '[1.0, 2.0]');

-- OK: numeric strings are coerced
INSERT INTO scores VALUES (3, '["42", "100"]');

-- Error: non-numeric string in INTEGER array
INSERT INTO scores VALUES (4, '["hello"]');
-- Error: cannot store TEXT value in INTEGER

-- NULL elements are always allowed
INSERT INTO scores VALUES (5, '[1, null, 3]');

```

### See Also

- [Data Types](https://docs.turso.tech/sql-reference/data-types#array-types) covers array column type declarations
- [CREATE TABLE](https://docs.turso.tech/sql-reference/statements/create-table) explains STRICT table requirements
- [Expressions](https://docs.turso.tech/sql-reference/expressions#array-operators) documents array operator syntax
- [Aggregate Functions](https://docs.turso.tech/sql-reference/functions/aggregate) covers `array_agg`
