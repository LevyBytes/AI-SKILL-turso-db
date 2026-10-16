# JSON array object and string construction

[Context index](json-function-reference.md) · [Setup and preceding context](json-paths-creation-and-binary-format.md) · [Continue](json-validation-and-error-positions.md)

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
