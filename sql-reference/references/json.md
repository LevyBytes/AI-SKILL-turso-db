# Json

> Sql Reference reference. Original prose; identifiers preserved verbatim.

### JSON Functions

 Turso supports the JSON function family that aligns with SQLite's JSON1 extension. The functions can work with JSON kept as TEXT or with Turso's internal binary JSON (JSONB) storage format. Many functions have two forms: `json_*`, which yields TEXT, and `jsonb_*`, which yields BLOB in the internal binary representation. Choose the JSONB form when the output will be saved or fed into another JSON function instead of sent back to application code.

### JSON Path Syntax

 Many JSON functions take a **path** parameter to point at a particular location inside a JSON document.
| Syntax | Meaning |
| --- | --- |
| `$` | The root element |
| `$.key` | Object member named `key` |
| `$[N]` | Array item at zero-based index `N` |
| `$.key1.key2` | Nested object member |
| `$.key[0]` | Leading array item within an object's member |
| `$[0].key` | Object field contained in the first array item |

 Every path argument starts with `$`. When no element matches the path, functions usually produce `NULL`.

```
SELECT json_extract('{"a": {"b": [10, 20, 30]}}', '$.a.b[1]');
-- 20

```

---

### JSON Creation and Validation

### json

 Checks that a JSON string is valid and emits the compact form. Invalid JSON input causes an error.

```
json(json_text)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_text` | TEXT | JSON text to check and compact |

 **Returns:** TEXT - JSON text after minification.

```
SELECT json('  { "name": "Alice" ,  "age": 30 } ');
-- {"name":"Alice","age":30}

```

### jsonb

 Transforms a JSON string into the internal binary JSON representation.

```
jsonb(json_text)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_text` | TEXT | A JSON string to convert |

 **Returns:** BLOB - the binary JSON representation of the value.

```
SELECT typeof(jsonb('{"a":1}'));
-- blob

```

### json_array / jsonb_array

 Builds a JSON array from the supplied arguments.

```
json_array(value1, value2, ...)
jsonb_array(value1, value2, ...)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `value1, value2, ...` | any | Items to place in the array. A SQL NULL value is represented as JSON `null`. |

 **Returns:** TEXT (json_array) or BLOB (jsonb_array) - the resulting JSON array.

```
SELECT json_array(1, 'hello', NULL, 3.14);
-- [1,"hello",null,3.14]

SELECT json_array();
-- []

```

### json_object / jsonb_object

 Builds a JSON object from label/value arguments supplied in alternating order. With `*`, all columns from the current row are expanded into label/value entries, using each column name as the key.

```
json_object(label1, value1, label2, value2, ...)
jsonb_object(label1, value1, label2, value2, ...)
json_object(*)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `label1, label2, ...` | TEXT | JSON object keys; each key must be a string. |
| `value1, value2, ...` | any | Corresponding values. SQL NULL becomes JSON `null`. |

 **Returns:** TEXT (json_object) or BLOB (jsonb_object) - the resulting JSON object.

```
SELECT json_object('name', 'Alice', 'age', 30);
-- {"name":"Alice","age":30}

SELECT json_object('items', json_array(1, 2, 3));
-- {"items":[1,2,3]}

```

### json_quote

 Encodes a SQL value using its JSON form.

```
json_quote(value)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `value` | any | SQL value to encode as JSON |

 **Returns:** TEXT - the value expressed as JSON.

```
SELECT json_quote('hello');
-- "hello"

SELECT json_quote(42);
-- 42

SELECT json_quote(NULL);
-- null

```

### json_valid

 Produces 1 when the input is well-formed JSON, and 0 when it is not.

```
json_valid(json_text)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_text` | TEXT | String to test for JSON validity |

 **Returns:** INTEGER - 1 for valid JSON, otherwise 0.

```
SELECT json_valid('{"name":"Alice"}');
-- 1

SELECT json_valid('not json');
-- 0

SELECT json_valid(NULL);
-- 0

```

### json_error_position

 Reports the character offset of the first JSON syntax problem, or 0 when the string is valid JSON.

```
json_error_position(json_text)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_text` | TEXT | String to scan for JSON syntax problems |

 **Returns:** INTEGER - first error position using 1-based numbering, or 0 for valid input.

```
SELECT json_error_position('{"a":1}');
-- 0

SELECT json_error_position('{"a":}');
-- 6

```

---

### JSON Extraction

### json_extract / jsonb_extract

 Reads one or more values from a JSON document according to the supplied path arguments.

```
json_extract(json_text, path)
json_extract(json_text, path1, path2, ...)
jsonb_extract(json_text, path)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_text` | TEXT or BLOB | A JSON document |
| `path` | TEXT | One or more JSON path expressions |

 **Returns:** With a single path, the result uses the corresponding SQL type, such as INTEGER, REAL, TEXT, or NULL. Objects and arrays are returned as TEXT. When more than one path is supplied, the selected values are returned together in a JSON array. `jsonb_extract` produces BLOB.

```
SELECT json_extract('{"name":"Alice","age":30}', '$.name');
-- Alice

SELECT json_extract('{"name":"Alice","age":30}', '$.age');
-- 30

-- Multiple paths return a JSON array
SELECT json_extract('{"a":1,"b":2,"c":3}', '$.a', '$.c');
-- [1,3]

```

### -> operator

 Gets a value from JSON and returns that result as JSON. This shorthand for `json_extract` always yields JSON text: objects and arrays stay JSON, while strings are JSON-quoted.

```
json_text -> path

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_text` | TEXT or BLOB | A JSON document |
| `path` | TEXT | A JSON path expression |

 **Returns:** TEXT - selected value represented as JSON.

```
SELECT '{"name":"Alice"}' -> '$.name';
-- "Alice"

SELECT '{"items":[1,2,3]}' -> '$.items';
-- [1,2,3]

```

### ->> operator

 Gets a value from JSON and converts it to a SQL value. Strings lose their JSON quotes, numbers come back as INTEGER or REAL, and booleans are represented with integers (0 or 1).

```
json_text ->> path

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_text` | TEXT or BLOB | A JSON document |
| `path` | TEXT | A JSON path expression |

 **Returns:** The selected value using its natural SQL type (TEXT, INTEGER, REAL, or NULL).

```
SELECT '{"name":"Alice"}' ->> '$.name';
-- Alice

SELECT '{"count":42}' ->> '$.count';
-- 42

```

### json_type

 Reports the JSON value type as one of these strings: `"null"`, `"true"`, `"false"`, `"integer"`, `"real"`, `"text"`, `"array"`, or `"object"`.

```
json_type(json_text)
json_type(json_text, path)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_text` | TEXT or BLOB | A JSON document |
| `path` | TEXT | Optional JSON path to examine. Without it, the root is examined. |

 **Returns:** TEXT - the JSON type label.

```
SELECT json_type('{"a":1}');
-- object

SELECT json_type('[1, 2, 3]');
-- array

SELECT json_type('{"a": 1}', '$.a');
-- integer

SELECT json_type('{"a": "hello"}', '$.a');
-- text

```

---

### JSON Modification

### json_insert / jsonb_insert

 Adds new values to a JSON document without changing existing ones. When the target path is already present, the current value is preserved.

```
json_insert(json_text, path1, value1, path2, value2, ...)
jsonb_insert(json_text, path1, value1, path2, value2, ...)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_text` | TEXT or BLOB | A JSON document |
| `path` | TEXT | JSON path at which to add the value |
| `value` | any | The value to insert |

 **Returns:** TEXT (json_insert) or BLOB (jsonb_insert) - JSON after modification.

```
SELECT json_insert('{"a":1}', '$.b', 2);
-- {"a":1,"b":2}

-- Existing values are NOT overwritten
SELECT json_insert('{"a":1}', '$.a', 99);
-- {"a":1}

```

### json_replace / jsonb_replace

 Updates values that are already present in a JSON document. Missing paths are ignored rather than created.

```
json_replace(json_text, path1, value1, path2, value2, ...)
jsonb_replace(json_text, path1, value1, path2, value2, ...)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_text` | TEXT or BLOB | A JSON document |
| `path` | TEXT | JSON path that points to the value being replaced |
| `value` | any | The replacement value |

 **Returns:** TEXT (json_replace) or BLOB (jsonb_replace) - JSON after modification.

```
SELECT json_replace('{"a":1,"b":2}', '$.a', 99);
-- {"a":99,"b":2}

-- Non-existent paths are ignored
SELECT json_replace('{"a":1}', '$.b', 2);
-- {"a":1}

```

### json_set / jsonb_set

 Adds or updates values in a JSON document. It combines `json_insert` with `json_replace`: an existing path is updated, while a missing path is created.

```
json_set(json_text, path1, value1, path2, value2, ...)
jsonb_set(json_text, path1, value1, path2, value2, ...)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_text` | TEXT or BLOB | A JSON document |
| `path` | TEXT | JSON path associated with the value |
| `value` | any | The value to set |

 **Returns:** TEXT (json_set) or BLOB (jsonb_set) - JSON after modification.

```
-- Replace existing
SELECT json_set('{"a":1}', '$.a', 99);
-- {"a":99}

-- Insert new
SELECT json_set('{"a":1}', '$.b', 2);
-- {"a":1,"b":2}

```

### json_remove / jsonb_remove

 Deletes one or more entries from a JSON document.

```
json_remove(json_text, path1, path2, ...)
jsonb_remove(json_text, path1, path2, ...)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_text` | TEXT or BLOB | A JSON document |
| `path` | TEXT | One or more JSON paths for values to delete |

 **Returns:** TEXT (json_remove) or BLOB (jsonb_remove) - JSON after modification.

```
SELECT json_remove('{"a":1,"b":2,"c":3}', '$.b');
-- {"a":1,"c":3}

SELECT json_remove('[1,2,3,4]', '$[1]');
-- [1,3,4]

```

### json_patch / jsonb_patch

 Applies an RFC 7396 merge patch against a JSON document. Members from the patch object replace matching target members, and a patch member set to `null` deletes the matching member.

```
json_patch(json_text, patch)
jsonb_patch(json_text, patch)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_text` | TEXT or BLOB | The target JSON document |
| `patch` | TEXT or BLOB | The merge patch to apply |

 **Returns:** TEXT (json_patch) or BLOB (jsonb_patch) - JSON after patching.

```
SELECT json_patch('{"a":1,"b":2}', '{"b":3,"c":4}');
-- {"a":1,"b":3,"c":4}

-- null in the patch removes a key
SELECT json_patch('{"a":1,"b":2}', '{"b":null}');
-- {"a":1}

```

### json_pretty

 Outputs a JSON document in an indented, human-readable layout.

```
json_pretty(json_text)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_text` | TEXT or BLOB | A JSON document |

 **Returns:** TEXT - indented JSON text.

```
SELECT json_pretty('{"name":"Alice","scores":[90,85,92]}');
/*
{
    "name": "Alice",
    "scores": [
        90,
        85,
        92
    ]
}
*/

```

---

### JSON Array Functions

### json_array_length

 Counts entries in a JSON array. Empty arrays yield 0, while JSON values that are not arrays yield NULL.

```
json_array_length(json_text)
json_array_length(json_text, path)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_text` | TEXT or BLOB | A JSON document |
| `path` | TEXT | Optional JSON path to an array inside the document. |

 **Returns:** INTEGER - element count, or NULL when the path value is not an array.

```
SELECT json_array_length('[1, 2, 3, 4]');
-- 4

SELECT json_array_length('{"items": [10, 20]}', '$.items');
-- 2

SELECT json_array_length('{"a": 1}');
-- NULL

```

---

### JSON Aggregate Functions

### json_group_array / jsonb_group_array

 Aggregate function that gathers grouped values into a JSON array.

```
json_group_array(value)
jsonb_group_array(value)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `value` | any | Value taken from each row for aggregation |

 **Returns:** TEXT (json_group_array) or BLOB (jsonb_group_array) - JSON array containing every grouped value.

```
CREATE TABLE items (category TEXT, name TEXT);
INSERT INTO items VALUES ('fruit', 'apple'), ('fruit', 'banana'), ('veggie', 'carrot');

SELECT category, json_group_array(name) FROM items GROUP BY category;
-- fruit  | ["apple","banana"]
-- veggie | ["carrot"]

```

### json_group_object / jsonb_group_object

 Aggregate function that gathers label/value pairs from grouped rows into a JSON object.

```
json_group_object(label, value)
jsonb_group_object(label, value)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `label` | TEXT | The key for each entry |
| `value` | any | The value for each entry |

 **Returns:** TEXT (json_group_object) or BLOB (jsonb_group_object) - a JSON object.

```
CREATE TABLE settings (key TEXT, value TEXT);
INSERT INTO settings VALUES ('theme', 'dark'), ('lang', 'en');

SELECT json_group_object(key, value) FROM settings;
-- {"theme":"dark","lang":"en"}

```

---

### JSON Table-Valued Functions

### json_each

 Table-valued function that iterates over the immediate children of a JSON array or object and returns one row for each child.

```
SELECT * FROM json_each(json_text);
SELECT * FROM json_each(json_text, path);

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_text` | TEXT or BLOB | A JSON document |
| `path` | TEXT | Optional JSON path to the array or object to iterate over. If omitted, `$` is used. |

 **Output columns:**
| Column | Type | Description |
| --- | --- | --- |
| `key` | TEXT or INTEGER | Object key as TEXT, or array index as INTEGER |
| `value` | any | Element value, represented as JSON text for objects/arrays and as a SQL value for primitive values |
| `type` | TEXT | JSON type: `null`, `true`, `false`, `integer`, `real`, `text`, `array`, or `object` |
| `atom` | any | SQL value for primitive values; arrays and objects return NULL |
| `id` | INTEGER | Sequential element identifier |
| `parent` | INTEGER | Parent element id, or NULL for a top-level element |
| `fullkey` | TEXT | Complete JSON path for this element |
| `path` | TEXT | JSON path for this element's parent |

```
SELECT key, value, type FROM json_each('[10, "hello", null]');
-- 0 | 10    | integer
-- 1 | hello | text
-- 2 | null  | null

SELECT key, value FROM json_each('{"a":1, "b":2}');
-- a | 1
-- b | 2

-- With a path
SELECT key, value FROM json_each('{"data": [1, 2, 3]}', '$.data');
-- 0 | 1
-- 1 | 2
-- 2 | 3

```

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
