# JSON validation and error positions

[Context index](json-function-reference.md) · [Setup and preceding context](json-paths-creation-and-binary-format.md) · [Previous phase](json-array-object-and-string-construction.md) · [Continue](json-extraction-and-arrow-operators.md)

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
