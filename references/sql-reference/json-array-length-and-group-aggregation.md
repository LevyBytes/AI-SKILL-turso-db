# JSON array length and group aggregation

[Context index](json-function-reference.md) · [Setup and preceding context](json-paths-creation-and-binary-format.md) · [Previous phase](json-patching-and-pretty-printing.md) · [Continue](json-object-aggregation-and-each-rows.md)

### json_array_length

 Counts entries in a JSON array. Empty arrays yield 0, while JSON values that are not arrays yield 0.

```
json_array_length(json_text)
json_array_length(json_text, path)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_text` | TEXT or BLOB | A JSON document |
| `path` | TEXT | Optional JSON path to an array inside the document. |

 **Returns:** INTEGER - element count; 0 for a non-array value, and NULL for a missing path or SQL NULL input.

```
SELECT json_array_length('[1, 2, 3, 4]');
-- 4

SELECT json_array_length('{"items": [10, 20]}', '$.items');
-- 2

SELECT json_array_length('{"a": 1}');
-- 0

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
