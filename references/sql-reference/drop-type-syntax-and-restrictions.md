# Drop type syntax and restrictions

[Context index](virtual-tables-delete-and-drop-reference.md)

## DROP TYPE

 **Turso Extension**: DROP TYPE is a Turso-specific statement and is not part of standard SQLite. This experimental feature must be [enabled explicitly](https://docs.turso.tech/sql-reference/experimental-features) before use. DROP TYPE deletes a user-defined custom type from the database. After removal, new column definitions cannot use that type name.

### Syntax

```
DROP TYPE [IF EXISTS] type-name;

```

### Description

 DROP TYPE deletes the specified custom type from the database. It must already exist unless `IF EXISTS` is included.

### Clauses

| Clause | Description |
| --- | --- |
| `IF EXISTS` | Avoids the error raised when the type is missing. If no matching type is found, the statement does nothing. |
| `type-name` | Custom type name to remove. |

### Restrictions

 A type cannot be dropped while any table column still uses it. Remove those columns or drop the table before dropping the type.

```
CREATE TYPE cents BASE integer ENCODE value * 100 DECODE value / 100;

CREATE TABLE prices (
    id INTEGER PRIMARY KEY,
    amount cents
) STRICT;

-- This fails because the 'prices' table has a column of type 'cents'
DROP TYPE cents;
-- Error: type 'cents' is in use

-- Drop the table first, then the type
DROP TABLE prices;
DROP TYPE cents;

```

### Examples

### Drop a Custom Type

```
CREATE TYPE reversed_text BASE text
    ENCODE turso_reverse(value)
    DECODE turso_reverse(value);

DROP TYPE reversed_text;

```

### Drop a Type with IF EXISTS

```
-- Safe to run even if the type does not exist
DROP TYPE IF EXISTS reversed_text;

```

### See Also

- [CREATE TYPE](https://docs.turso.tech/sql-reference/statements/create-type) when defining custom types
- [DROP DOMAIN](https://docs.turso.tech/sql-reference/statements/drop-domain) when removing domains
- [Data Types](https://docs.turso.tech/sql-reference/data-types) for a broader view of the type system
