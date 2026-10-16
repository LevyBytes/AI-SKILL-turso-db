# Drop domain and dependency restrictions

[Context index](virtual-tables-delete-and-drop-reference.md) · [Continue](drop-domain-after-table-removal.md)

## DROP DOMAIN

 **Turso Extension**: DROP DOMAIN is Turso-specific and is not part of standard SQLite. This experimental feature must be [enabled explicitly](https://docs.turso.tech/sql-reference/experimental-features) before it is used. DROP DOMAIN deletes a user-defined domain from the database. After removal, new column definitions cannot use that domain name.

### Syntax

```
DROP DOMAIN [IF EXISTS] domain-name;

```

### Description

 DROP DOMAIN deletes the specified domain from the database. It must already exist unless `IF EXISTS` is included.

### Clauses

| Clause | Description |
| --- | --- |
| `IF EXISTS` | Avoids the error raised when the domain is missing. If no matching domain is found, the statement does nothing. |
| `domain-name` | Domain name to remove. |

### Restrictions

 You cannot drop a domain while a table column uses it or another domain has it as a base type. Remove or update all dependent definitions first.

```
CREATE DOMAIN base_d AS integer;
CREATE DOMAIN child_d AS base_d CHECK (value > 0);

-- Fails: child_d depends on base_d
DROP DOMAIN base_d;

-- Drop child first, then base
DROP DOMAIN child_d;
DROP DOMAIN base_d;

```

### DROP DOMAIN vs DROP TYPE

 DROP DOMAIN applies only to domains made with CREATE DOMAIN. Using DROP DOMAIN for a type made with CREATE TYPE raises an error, as does the reverse case.

```
CREATE TYPE my_type BASE integer ENCODE value DECODE value;
DROP DOMAIN my_type;
-- Error: 'my_type' is a type, not a domain. Use DROP TYPE instead

CREATE DOMAIN my_domain AS integer;
DROP TYPE my_domain;
-- Error: 'my_domain' is a domain, not a type. Use DROP DOMAIN instead

```

### Examples

### Drop a Domain

```
CREATE DOMAIN positive_int AS integer CHECK (value > 0);
DROP DOMAIN positive_int;

```

### Drop with IF EXISTS

```
-- Safe to run even if the domain does not exist
DROP DOMAIN IF EXISTS nonexistent_domain;

```
