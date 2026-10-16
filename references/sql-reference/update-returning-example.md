# Update returning example

[Context index](update-upsert-and-vacuum-reference.md) · [Setup and preceding context](update-set-from-and-conflicts.md) · [Previous phase](update-where-returning-and-join-examples.md)

### Update with RETURNING

```
CREATE TABLE counters (name TEXT PRIMARY KEY, value INTEGER);
INSERT INTO counters VALUES ('page_views', 41);

UPDATE counters
SET value = value + 1
WHERE name = 'page_views'
RETURNING name, value;
-- page_views|42

```

### See Also

- [INSERT](https://docs.turso.tech/sql-reference/statements/insert) when adding rows to a table
- [DELETE](https://docs.turso.tech/sql-reference/statements/delete) when rows need to be removed
- [INSERT … ON CONFLICT](https://docs.turso.tech/sql-reference/statements/upsert) when an insert should conditionally update instead
- [SELECT](https://docs.turso.tech/sql-reference/statements/select) when reading rows
