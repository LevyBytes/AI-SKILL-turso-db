# JSON paths creation and binary format

[Context index](json-function-reference.md) · [Continue](json-array-object-and-string-construction.md)

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
