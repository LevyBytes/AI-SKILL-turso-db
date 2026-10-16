# JSON replacement setting and removal

[Context index](json-function-reference.md) · [Setup and preceding context](json-paths-creation-and-binary-format.md) · [Previous phase](json-types-and-insertion.md) · [Continue](json-patching-and-pretty-printing.md)

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
