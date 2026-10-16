# JSON object aggregation and each rows

[Context index](json-function-reference.md) · [Setup and preceding context](json-paths-creation-and-binary-format.md) · [Previous phase](json-array-length-and-group-aggregation.md) · [Continue](json-tree-queries-and-document-updates.md)

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
