# JSON extraction and arrow operators

[Context index](json-function-reference.md) · [Setup and preceding context](json-paths-creation-and-binary-format.md) · [Previous phase](json-validation-and-error-positions.md) · [Continue](json-types-and-insertion.md)

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

 **Returns:** With a single path, the result uses the corresponding SQL type, such as INTEGER, REAL, TEXT, or NULL. Objects and arrays are returned as TEXT. When more than one path is supplied, the selected values are returned together in a JSON array. `jsonb_extract` uses the same SQL scalar results; it returns BLOB for selected objects or arrays.

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
