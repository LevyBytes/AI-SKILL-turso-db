# Scalar extension loading

[Context index](scalar-function-reference.md) · [Setup and preceding context](scalar-function-category-reference.md) · [Previous phase](scalar-unicode-soundex-and-system-counters.md)

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
