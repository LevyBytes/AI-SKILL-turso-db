# Array construction and string parsing

[Context index](array-function-reference.md) · [Continue](array-subscripts-and-slices.md)

### Array Functions

 **Turso Extension**: Turso adds array types and companion functions for handling ordered collections from SQL. This feature is Turso-specific and is not included in the SQLite standard. Defining array columns requires [STRICT tables](https://docs.turso.tech/sql-reference/statements/create-table#strict-tables). Turso includes native support for array columns and functions that build, inspect, and transform arrays. Internally, arrays are compact record-format BLOB values; result output renders them as JSON arrays.

---

### Array Construction

### ARRAY[] Literal

 Builds an array from the supplied expression list.

```
ARRAY[expr1, expr2, ...]

```

```
SELECT ARRAY[1, 2, 3];
-- [1,2,3]

SELECT ARRAY['hello', 'world'];
-- ["hello","world"]

SELECT ARRAY[];
-- []

```

 JSON text can also be used when inserting arrays:

```
CREATE TABLE t (id INTEGER PRIMARY KEY, tags TEXT[]) STRICT;

INSERT INTO t VALUES (1, ARRAY['a', 'b', 'c']);
INSERT INTO t VALUES (2, '["x", "y", "z"]');

SELECT tags FROM t ORDER BY id;
-- ["a","b","c"]
-- ["x","y","z"]

```

### string_to_array

 Divides a string into array elements with the chosen delimiter.

```
string_to_array(text, delimiter)
string_to_array(text, delimiter, null_string)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `text` | TEXT | The string to split |
| `delimiter` | TEXT | Delimiter used for splitting. If NULL, the input is separated into individual characters |
| `null_string` | TEXT | Optional. Values equal to this string are converted to NULL |

 **Returns:** BLOB — an array value.

```
SELECT string_to_array('one,two,three', ',');
-- ["one","two","three"]

SELECT string_to_array('hello', NULL);
-- ["h","e","l","l","o"]

SELECT string_to_array('a,NULL,b', ',', 'NULL');
-- ["a",null,"b"]

```

---

### Element Access
