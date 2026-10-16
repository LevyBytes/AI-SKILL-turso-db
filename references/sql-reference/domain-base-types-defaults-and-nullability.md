# Domain base types defaults and nullability

[Context index](alter-analyze-attach-and-domain-reference.md) · [Continue](domain-checks-and-chaining.md)

## CREATE DOMAIN

 **Turso Extension**: CREATE DOMAIN is Turso-specific and is not part of standard SQLite. Domains require STRICT tables plus the custom types experimental feature, which must be [enabled before use](https://docs.turso.tech/sql-reference/experimental-features). The CREATE DOMAIN statement creates a named type alias and can add constraints. Unlike [CREATE TYPE](https://docs.turso.tech/sql-reference/statements/create-type), a domain supplies no custom ENCODE/DECODE logic and no OPERATORs. It layers NOT NULL and CHECK constraints over a base type, while values continue to use the base type's native storage format.

### Syntax

```
CREATE DOMAIN [IF NOT EXISTS] domain-name AS base-type
    [DEFAULT default-expr]
    [NOT NULL]
    [[CONSTRAINT constraint-name] CHECK (expr)]
    ...;

```

### Description

 A domain places validation rules around a base type. On writes to a domain-typed column, Turso verifies CHECK constraints and any NOT NULL restriction. Passing values are stored in the base type's native format. Reads return the stored value without changes. For ORDER BY, indexing, arithmetic, aggregation, and similar work, domains are transparent to the query engine. A domain column acts like its base-type column except that inputs are validated. Domains apply only in STRICT tables. In a non-STRICT table, a domain name has no effect.

### Clauses

### IF NOT EXISTS

 Prevents the error that would otherwise be raised when a domain with the same name already exists. The existing domain remains unchanged.

```
CREATE DOMAIN IF NOT EXISTS positive_int AS integer CHECK (value > 0);

```

### AS base-type

 Sets the underlying type. Use a primitive base (`integer`, `real`, `text`, or `blob`) or another domain; using another domain layers the domains.

```
CREATE DOMAIN percentage AS integer
    CHECK (value >= 0)
    CHECK (value <= 100);

```

### DEFAULT

 Defines the default for columns using this domain type. A column-level DEFAULT takes precedence over the domain-level DEFAULT.

```
CREATE DOMAIN status AS text DEFAULT 'active';

CREATE TABLE accounts (
    id INTEGER PRIMARY KEY,
    state status
) STRICT;

INSERT INTO accounts(id) VALUES (1);
SELECT state FROM accounts;
-- active

```

### NOT NULL

 Applies a NOT NULL constraint to the domain. INSERT or UPDATE of NULL into a column of this domain type fails, even when the column definition omits NOT NULL.

```
CREATE DOMAIN required_text AS text NOT NULL;

CREATE TABLE contacts (
    id INTEGER PRIMARY KEY,
    name required_text
) STRICT;

INSERT INTO contacts VALUES (1, 'Alice');
-- OK

INSERT INTO contacts VALUES (2, NULL);
-- Error: domain required_text does not allow null values

```

 Declaring NULL at the column level does not supersede the domain's NOT NULL constraint.
