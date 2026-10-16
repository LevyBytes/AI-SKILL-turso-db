# Upsert conditional update example

[Context index](update-upsert-and-vacuum-reference.md) · [Setup and preceding context](upsert-conflict-targets-and-excluded-values.md) · [Previous phase](upsert-conditions-counters-and-bulk-inserts.md)

### Conditional update with excluded

```
CREATE TABLE prices (
    product_id INTEGER PRIMARY KEY,
    price REAL NOT NULL,
    updated_at TEXT NOT NULL
);
INSERT INTO prices VALUES (1, 29.99, '2025-01-01');

-- Only update the price if the new timestamp is more recent
INSERT INTO prices (product_id, price, updated_at) VALUES (1, 24.99, '2025-06-15')
ON CONFLICT (product_id) DO UPDATE
    SET price = excluded.price,
        updated_at = excluded.updated_at
    WHERE excluded.updated_at > prices.updated_at;

SELECT * FROM prices;
-- 1|24.99|2025-06-15

```

### See Also

- [INSERT](https://docs.turso.tech/sql-reference/statements/insert) for ordinary insert statements
- [REPLACE](https://docs.turso.tech/sql-reference/statements/replace) when conflict handling should delete and insert again
- [UPDATE](https://docs.turso.tech/sql-reference/statements/update) when existing rows should be changed directly
- [CREATE TABLE](https://docs.turso.tech/sql-reference/statements/create-table) when defining `UNIQUE` and `PRIMARY KEY` constraints
