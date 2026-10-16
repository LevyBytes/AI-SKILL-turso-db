# Domain operations removal and restrictions

[Context index](alter-analyze-attach-and-domain-reference.md) · [Setup and preceding context](domain-base-types-defaults-and-nullability.md) · [Previous phase](domain-updates-casts-and-table-checks.md)

### Operations on Domain Columns

 A domain column supports its base type's operations, including arithmetic, comparisons, ordering, and aggregation.

```
CREATE DOMAIN myint AS integer;

CREATE TABLE data (
    id INTEGER PRIMARY KEY,
    a myint,
    b myint
) STRICT;

INSERT INTO data VALUES (1, 10, 3);
SELECT a + b, a - b, a * b FROM data;
-- 13|7|30

```

```
CREATE DOMAIN myint AS integer;

CREATE TABLE scores (
    id INTEGER PRIMARY KEY,
    val myint
) STRICT;

INSERT INTO scores VALUES (1, 30);
INSERT INTO scores VALUES (2, 10);
INSERT INTO scores VALUES (3, 20);
SELECT val FROM scores ORDER BY val;
-- 10
-- 20
-- 30

```

### Dropping Domains

 Use [DROP DOMAIN](https://docs.turso.tech/sql-reference/statements/drop-domain) to drop domains; do not use DROP TYPE. Using DROP TYPE on a domain, or DROP DOMAIN on a type, raises an error. A domain is not droppable while a table column or another domain still references it.

```
CREATE DOMAIN my_domain AS integer;
CREATE TABLE t(x my_domain) STRICT;

DROP DOMAIN my_domain;
-- Error: type 'my_domain' is in use

DROP TABLE t;
DROP DOMAIN my_domain;
-- OK

```

### Restrictions

- **STRICT tables only**: Domain types are rejected on non-STRICT tables at CREATE TABLE and ALTER TABLE ADD COLUMN time. Non-STRICT tables treat arbitrary type names merely as affinity hints. Domain constraints (CHECK, NOT NULL, DEFAULT) are enforced only on STRICT tables, so allowing domain types in non-STRICT tables would be misleading.
- **Cannot drop while in use**: A domain remains undroppable as long as any table column or another domain references it.
 If a non-STRICT table used a type name that did not yet exist when it was created, such as `CREATE TABLE t(x mydom)`, and a matching domain is later created with `CREATE DOMAIN mydom AS ...`, its domain constraints are **not** applied retroactively to that existing table. This behavior is intentional: non-STRICT tables handle every type name as a plain affinity hint, whether or not a matching domain exists. STRICT tables avoid the scenario by rejecting unknown type names at CREATE TABLE time.
- **No UNIQUE, PRIMARY KEY, or REFERENCES**: Domain definitions do not support these constraints. Put them in table-level constraints instead.
- **No duplicate clauses**: A definition cannot include multiple DEFAULT or NOT NULL clauses. Conflicting NULL and NOT NULL clauses are rejected too.

### See Also

- [DROP DOMAIN](https://docs.turso.tech/sql-reference/statements/drop-domain) when removing domains
- [CREATE TYPE](https://docs.turso.tech/sql-reference/statements/create-type) for custom types that define ENCODE/DECODE logic
- [Data Types](https://docs.turso.tech/sql-reference/data-types) for a type-system overview
- [CREATE TABLE](https://docs.turso.tech/sql-reference/statements/create-table) for defining STRICT tables
