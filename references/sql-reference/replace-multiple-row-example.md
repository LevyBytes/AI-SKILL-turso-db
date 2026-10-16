# Replace multiple row example

[Context index](explain-insert-and-replace-reference.md) · [Setup and preceding context](replace-constraint-and-delete-insert-semantics.md)

### Multi-row REPLACE

```
CREATE TABLE inventory (sku TEXT PRIMARY KEY, quantity INTEGER);
INSERT INTO inventory VALUES ('A001', 10);
INSERT INTO inventory VALUES ('B002', 20);

REPLACE INTO inventory VALUES
    ('A001', 15),
    ('B002', 25),
    ('C003', 30);

SELECT * FROM inventory;
-- A001|15
-- B002|25
-- C003|30

```

### See Also

- [INSERT](https://docs.turso.tech/sql-reference/statements/insert) for adding rows when conflict handling is not requested.
- [INSERT ... ON CONFLICT](https://docs.turso.tech/sql-reference/statements/upsert) for controlled insert-or-update behavior that chooses which columns change.
- [UPDATE](https://docs.turso.tech/sql-reference/statements/update) for changing existing rows without replacing them.
