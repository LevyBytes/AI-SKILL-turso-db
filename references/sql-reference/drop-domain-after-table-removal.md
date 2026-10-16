# Drop domain after table removal

[Context index](virtual-tables-delete-and-drop-reference.md) · [Setup and preceding context](drop-domain-and-dependency-restrictions.md)

### Drop After Removing Table Dependency

```
CREATE DOMAIN score AS integer CHECK (value >= 0);
CREATE TABLE results (id INTEGER PRIMARY KEY, val score) STRICT;

-- Fails: table 'results' uses domain 'score'
-- DROP DOMAIN score;

DROP TABLE results;
DROP DOMAIN score;
-- OK

```

### See Also

- [CREATE DOMAIN](https://docs.turso.tech/sql-reference/statements/create-domain) when defining domains
- [DROP TYPE](https://docs.turso.tech/sql-reference/statements/drop-type) when removing custom types
