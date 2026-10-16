# JSON tree queries and document updates

[Context index](json-function-reference.md) · [Setup and preceding context](json-paths-creation-and-binary-format.md) · [Previous phase](json-object-aggregation-and-each-rows.md) · [Continue](json-array-flattening.md)

### json_tree

 `json_tree` is only partially supported, so some advanced traversal behavior may be unavailable or unreliable. It is a table-valued function that recursively traverses a JSON document and returns one row for each element at each nesting depth.

```
SELECT * FROM json_tree(json_text);
SELECT * FROM json_tree(json_text, path);

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_text` | TEXT or BLOB | A JSON document |
| `path` | TEXT | Optional JSON path that selects the subtree to traverse. If omitted, `$` is used. |

 **Output columns:** Same as `json_each`.

```
SELECT key, value, type, path FROM json_tree('{"a": [1, 2]}');
-- NULL | {"a":[1,2]} | object  | $
-- a    | [1,2]       | array   | $
-- 0    | 1           | integer | $.a
-- 1    | 2           | integer | $.a

```

---

### Practical Examples

### Storing and querying JSON data

```
CREATE TABLE events (id INTEGER PRIMARY KEY, data TEXT);
INSERT INTO events VALUES (1, '{"type":"click","x":100,"y":200}');
INSERT INTO events VALUES (2, '{"type":"scroll","offset":500}');

-- Extract specific fields
SELECT id, data ->> '$.type' AS event_type FROM events;
-- 1 | click
-- 2 | scroll

-- Filter by JSON value
SELECT * FROM events WHERE data ->> '$.type' = 'click';

```

### Modifying JSON in place

```
UPDATE events
SET data = json_set(data, '$.timestamp', '2025-01-15T10:30:00Z')
WHERE id = 1;

SELECT data FROM events WHERE id = 1;
-- {"type":"click","x":100,"y":200,"timestamp":"2025-01-15T10:30:00Z"}

```

### Building JSON from relational data

```
CREATE TABLE users (id INTEGER PRIMARY KEY, name TEXT, email TEXT);
INSERT INTO users VALUES (1, 'Alice', 'alice@example.com');
INSERT INTO users VALUES (2, 'Bob', 'bob@example.com');

SELECT json_object('users', json_group_array(
    json_object('id', id, 'name', name, 'email', email)
)) FROM users;
-- {"users":[{"id":1,"name":"Alice","email":"alice@example.com"},{"id":2,"name":"Bob","email":"bob@example.com"}]}

```
