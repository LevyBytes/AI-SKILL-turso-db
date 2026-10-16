# Upsert conditions counters and bulk inserts

[Context index](update-upsert-and-vacuum-reference.md) · [Setup and preceding context](upsert-conflict-targets-and-excluded-values.md) · [Continue](upsert-conditional-update-example.md)

### WHERE Clause on DO UPDATE

 `DO UPDATE` may add a `WHERE` expression to limit when the update is performed. When that `WHERE` condition is false for a conflicting row, the row is not changed and the insert is skipped silently, matching `DO NOTHING`.

```
INSERT INTO products (sku, name, price) VALUES ('A001', 'Widget', 8.99)
ON CONFLICT (sku) DO UPDATE
    SET price = excluded.price
    WHERE excluded.price < products.price;

```

 Here, the price changes only when the incoming price is below the current price.

### Multiple ON CONFLICT Clauses

 A single `INSERT` statement may include several `ON CONFLICT` clauses, with each one aimed at a different constraint. They run in sequence; on a constraint violation, the first matching clause applies.

```
INSERT INTO users (id, email, name) VALUES (1, 'alice@example.com', 'Alice')
ON CONFLICT (id) DO UPDATE SET name = excluded.name, email = excluded.email
ON CONFLICT (email) DO NOTHING;

```

 Only one `ON CONFLICT` clause may leave out the conflict target. The targetless clause is a catch-all, must be last, and can only use `DO NOTHING`.

### Examples

### Upsert a counter

```
CREATE TABLE counters (name TEXT PRIMARY KEY, value INTEGER NOT NULL DEFAULT 0);

-- First insert creates the row
INSERT INTO counters (name, value) VALUES ('logins', 1)
ON CONFLICT (name) DO UPDATE SET value = counters.value + excluded.value;

SELECT * FROM counters;
-- logins|1

-- Subsequent inserts increment the counter
INSERT INTO counters (name, value) VALUES ('logins', 1)
ON CONFLICT (name) DO UPDATE SET value = counters.value + excluded.value;

SELECT * FROM counters;
-- logins|2

```

### Bulk insert, skipping duplicates

```
CREATE TABLE tags (id INTEGER PRIMARY KEY, label TEXT UNIQUE);
INSERT INTO tags VALUES (1, 'rust');
INSERT INTO tags VALUES (2, 'sql');

-- Insert several tags, skipping any that already exist
INSERT INTO tags VALUES (2, 'sql'), (3, 'wasm'), (4, 'database')
ON CONFLICT DO NOTHING;

SELECT * FROM tags;
-- 1|rust
-- 2|sql
-- 3|wasm
-- 4|database

```
