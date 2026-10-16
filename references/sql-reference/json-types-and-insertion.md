# JSON types and insertion

[Context index](json-function-reference.md) · [Setup and preceding context](json-paths-creation-and-binary-format.md) · [Previous phase](json-extraction-and-arrow-operators.md) · [Continue](json-replacement-setting-and-removal.md)

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
