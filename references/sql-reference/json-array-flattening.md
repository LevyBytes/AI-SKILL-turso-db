# JSON array flattening

[Context index](json-function-reference.md) · [Setup and preceding context](json-paths-creation-and-binary-format.md) · [Previous phase](json-tree-queries-and-document-updates.md)

### Flattening JSON arrays with json_each

```
CREATE TABLE orders (id INTEGER PRIMARY KEY, items TEXT);
INSERT INTO orders VALUES (1, '["widget","gadget","gizmo"]');
INSERT INTO orders VALUES (2, '["sprocket"]');

-- Expand each order's items into individual rows
SELECT orders.id, each.value AS item
FROM orders, json_each(orders.items) AS each;
-- 1 | widget
-- 1 | gadget
-- 1 | gizmo
-- 2 | sprocket

```

### See Also

- See [Data Types](https://docs.turso.tech/sql-reference/data-types) for the SQL type mapping used by JSON values
- See [Expressions](https://docs.turso.tech/sql-reference/expressions) for using JSON operators within expressions
