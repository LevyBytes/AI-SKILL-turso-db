# Array

> Sql Reference reference. Original prose; identifiers preserved verbatim.

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

### Subscript Operator []

 Use zero-based indexes to read single elements. Out-of-bounds and negative indexes return NULL.

```
array_expr[index]

```

```
CREATE TABLE t (id INTEGER PRIMARY KEY, items TEXT[]) STRICT;
INSERT INTO t VALUES (1, '["first", "second", "third"]');

SELECT items[0], items[1], items[2] FROM t;
-- first|second|third

SELECT items[100] FROM t;
-- NULL

SELECT items[-1] FROM t;
-- NULL

```

 Multi-dimensional arrays can use chained subscripts:

```
CREATE TABLE m (id INTEGER PRIMARY KEY, matrix INTEGER[][]) STRICT;
INSERT INTO m VALUES (1, ARRAY[ARRAY[1,2], ARRAY[3,4]]);

SELECT matrix[0][1] FROM m;
-- 2

SELECT matrix[1][0] FROM m;
-- 3

```

### Slice Operator [start:end]

 Returns a sub-array with the half-open range `[start, end)`.

```
array_expr[start:end]

```

```
CREATE TABLE t (id INTEGER PRIMARY KEY, tags TEXT[]) STRICT;
INSERT INTO t VALUES (1, ARRAY['a','b','c','d']);

SELECT tags[1:3] FROM t;
-- ["b","c"]

```

---

### Scalar Functions

### array_length

 Gives the element count for an array.

```
array_length(array)
array_length(array, dimension)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `array` | BLOB/TEXT | The array to measure |
| `dimension` | INTEGER | Optional. Only dimension 1 is supported at present |

 **Returns:** INTEGER - element count, or NULL when the input is NULL.

```
SELECT array_length(ARRAY[10, 20, 30]);
-- 3

SELECT array_length('[]');
-- 0

SELECT array_length(NULL);
-- NULL

```

### array_append

 Adds an element after the current last element of an array.

```
array_append(array, element)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `array` | BLOB/TEXT | Target array for the append operation. If NULL, the result is a new one-element array |
| `element` | any | The element to append |

 **Returns:** BLOB - a newly produced array with the element added at the end.

```
SELECT array_append('[1, 2]', 3);
-- [1,2,3]

SELECT array_append('[]', 'x');
-- ["x"]

SELECT array_append(NULL, 'x');
-- ["x"]

```

### array_prepend

 Adds an element before the current first element of an array.

```
array_prepend(element, array)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `element` | any | The element to prepend |
| `array` | BLOB/TEXT | The array to prepend to |

 **Returns:** BLOB - a newly produced array with the element added at the beginning.

```
SELECT array_prepend('z', '["a", "b"]');
-- ["z","a","b"]

```

### array_cat

 Concatenates two arrays.

```
array_cat(array1, array2)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `array1` | BLOB/TEXT | The first array |
| `array2` | BLOB/TEXT | The second array |

 **Returns:** BLOB - a newly produced array that includes every element from both input arrays.

```
SELECT array_cat('[1, 2]', '[3, 4]');
-- [1,2,3,4]

SELECT array_cat('[]', '[1, 2]');
-- [1,2]

```

 For array concatenation, the `||` operator is supported too:

```
CREATE TABLE t (id INTEGER PRIMARY KEY, tags TEXT[]) STRICT;
INSERT INTO t VALUES (1, ARRAY['a', 'b']);

-- Append element
SELECT tags || 'c' FROM t;
-- ["a","b","c"]

-- Prepend element
SELECT 'z' || tags FROM t;
-- ["z","a","b"]

-- Concatenate arrays
SELECT tags || ARRAY['x', 'y'] FROM t;
-- ["a","b","x","y"]

```

### array_remove

 Deletes every matching occurrence of an element from an array.

```
array_remove(array, element)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `array` | BLOB/TEXT | The array to remove from |
| `element` | any | The element to remove |

 **Returns:** BLOB - a newly produced array with every occurrence of the element deleted.

```
SELECT array_remove('[1, 2, 3, 2, 1]', 2);
-- [1,3,1]

SELECT array_remove('[1, 2, 3]', 99);
-- [1,2,3]

```

### array_contains

 Checks whether a given element exists in an array.

```
array_contains(array, element)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `array` | BLOB/TEXT | The array to search |
| `element` | any | The element to search for |

 **Returns:** INTEGER - 1 when found, otherwise 0.

```
SELECT array_contains('[1, 2, 3]', 2);
-- 1

SELECT array_contains('[1, 2, 3]', 5);
-- 0

```

### array_position

 Gives the zero-based index for the first matching element.

```
array_position(array, element)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `array` | BLOB/TEXT | The array to search |
| `element` | any | The element to find |

 **Returns:** INTEGER - the zero-based index, or NULL when no match exists.

```
SELECT array_position('[10, 20, 30]', 20);
-- 1

SELECT array_position('[10, 20, 30]', 10);
-- 0

SELECT array_position('[10, 20, 30]', 99);
-- NULL

```

### array_slice

 Returns a sub-array selected by an index range.

```
array_slice(array, start, end)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `array` | BLOB/TEXT | The source array |
| `start` | INTEGER | Start index (zero-based, inclusive). NULL means 0 |
| `end` | INTEGER | End index (exclusive) |

 **Returns:** BLOB - a newly produced array containing elements from `start` through `end - 1`.

```
SELECT array_slice('[1, 2, 3, 4]', 1, 3);
-- [2,3]

SELECT array_slice('[1, 2, 3, 4]', NULL, 2);
-- [1,2]

```

### array_to_string

 Combines array elements into one string separated by a delimiter.

```
array_to_string(array, delimiter)
array_to_string(array, delimiter, null_string)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `array` | BLOB/TEXT | The array to join |
| `delimiter` | TEXT | The separator between elements |
| `null_string` | TEXT | Optional. Text used in place of NULL elements. If omitted, NULL values are skipped |

 **Returns:** TEXT — the joined string.

```
SELECT array_to_string('[1, 2, 3]', ',');
-- 1,2,3

SELECT array_to_string('["hello", "world"]', ' ');
-- hello world

SELECT array_to_string('[1, null, 3]', ',');
-- 1,3

SELECT array_to_string('[1, null, 3]', ',', 'N/A');
-- 1,N/A,3

```

### array_contains_all

 Checks whether one array includes every element from another array.

```
array_contains_all(haystack, needles)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `haystack` | BLOB/TEXT | The array to search in |
| `needles` | BLOB/TEXT | Array containing the elements to find |

 **Returns:** INTEGER - 1 if every element of `needles` appears in `haystack`, otherwise 0.

```
SELECT array_contains_all('[1, 2, 3, 4, 5]', '[2, 4]');
-- 1

SELECT array_contains_all('[1, 2, 3]', '[2, 4]');
-- 0

SELECT array_contains_all('[1, 2, 3]', '[]');
-- 1

```

 The `@>` operator aliases this function. See [Array Operators](https://docs.turso.tech/sql-reference/expressions#array-operators).

### array_overlap

 Checks whether two arrays have at least one element in common.

```
array_overlap(array1, array2)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `array1` | BLOB/TEXT | The first array |
| `array2` | BLOB/TEXT | The second array |

 **Returns:** INTEGER - 1 when at least one element is present in both arrays, otherwise 0.

```
SELECT array_overlap('[1, 2, 3]', '[3, 4, 5]');
-- 1

SELECT array_overlap('[1, 2, 3]', '[4, 5, 6]');
-- 0

SELECT array_overlap('[]', '[1, 2]');
-- 0

```

 `array_overlaps` is also accepted as an alias. The `&&` operator is an alias as well. See [Array Operators](https://docs.turso.tech/sql-reference/expressions#array-operators).

---

### Aggregate Function

### array_agg

 Aggregates row values from a group into an array.

```
array_agg(expression)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `expression` | any | Value to add to the aggregate array. NULL values are retained |

 **Returns:** BLOB - an array with one element for each row in the group. Empty groups return NULL.

```
CREATE TABLE t (grp TEXT, val TEXT);
INSERT INTO t VALUES ('x', 'a'), ('x', 'b'), ('y', 'c'), ('y', 'd'), ('y', 'e');

SELECT grp, array_agg(val) FROM t GROUP BY grp ORDER BY grp;

```

| grp | array_agg(val) |
| --- | --- |
| x | [“a”,“b”] |
| y | [“c”,“d”,“e”] |

```
-- Control order with a subquery
SELECT array_agg(val) FROM (SELECT val FROM t ORDER BY val);
-- ["a","b","c","d","e"]

-- Combine with array_length
SELECT array_length(array_agg(val)) FROM t;
-- 5

```

---

### Array Operators

### Containment: @>

 Checks whether the left array includes every element from the right array. Equivalent to `array_contains_all(left, right)`.

```
array1 @> array2

```

```
SELECT ARRAY[1,2,3] @> ARRAY[1,2];
-- 1

SELECT ARRAY[1,2,3] @> ARRAY[1,4];
-- 0

```

### Overlap: &&

 Checks whether two arrays have at least one shared element. Equivalent to `array_overlap(left, right)`.

```
array1 && array2

```

```
SELECT ARRAY[1,2,3] && ARRAY[3,4,5];
-- 1

SELECT ARRAY[1,2] && ARRAY[3,4];
-- 0

```

### Comparison: =, !=, <, >, <=, >=

 Arrays compare element by element. Pairwise comparison proceeds left to right; when all compared elements match, the shorter array sorts before the longer array.

```
SELECT ARRAY[1,2,3] = ARRAY[1,2,3];   -- 1
SELECT ARRAY[1,2] < ARRAY[1,3];       -- 1
SELECT ARRAY[1,2] < ARRAY[1,2,3];     -- 1 (shorter is less)

```

### Concatenation: ||

 If either operand is an array, `||` uses array concatenation or element append/prepend behavior rather than string concatenation.

```
SELECT ARRAY[1,2] || ARRAY[3,4];  -- [1,2,3,4]  (array + array)
SELECT ARRAY[1,2] || 3;           -- [1,2,3]    (append element)
SELECT 0 || ARRAY[1,2];           -- [0,1,2]    (prepend element)

```

### Using operators in WHERE clauses

```
CREATE TABLE docs (id INTEGER PRIMARY KEY, tags TEXT[]) STRICT;
INSERT INTO docs VALUES (1, ARRAY['sql','database','tutorial']);
INSERT INTO docs VALUES (2, ARRAY['rust','systems']);
INSERT INTO docs VALUES (3, ARRAY['sql','rust']);

-- Find docs tagged with both 'sql' and 'rust'
SELECT id FROM docs WHERE tags @> ARRAY['sql','rust'];
-- 3

-- Find docs with any overlap with a set of tags
SELECT id FROM docs WHERE tags && ARRAY['database','systems'] ORDER BY id;
-- 1
-- 2

```

---

### Subscript Assignment

 UPDATE statements can modify individual array elements through subscript syntax.

```
UPDATE table SET column[index] = value WHERE ...;

```

```
CREATE TABLE t (id INTEGER PRIMARY KEY, tags TEXT[]) STRICT;
INSERT INTO t VALUES (1, ARRAY['a','b','c']);

UPDATE t SET tags[0] = 'X' WHERE id = 1;
SELECT tags FROM t;
-- ["X","b","c"]

-- Multiple subscripts in one UPDATE
UPDATE t SET tags[0] = 'A', tags[2] = 'C' WHERE id = 1;
SELECT tags FROM t;
-- ["A","b","C"]

```

 Assignments beyond the array bounds are ignored without an error, leaving the array unchanged.

---

### Examples

### Tagging system

```
CREATE TABLE articles (
    id INTEGER PRIMARY KEY,
    title TEXT NOT NULL,
    tags TEXT[]
) STRICT;

INSERT INTO articles VALUES (1, 'Getting Started with SQL', ARRAY['sql','beginner','tutorial']);
INSERT INTO articles VALUES (2, 'Advanced Indexing', ARRAY['sql','performance','advanced']);
INSERT INTO articles VALUES (3, 'Rust for Systems', ARRAY['rust','systems']);

-- Find articles tagged 'sql'
SELECT title FROM articles WHERE array_contains(tags, 'sql');

-- Find articles with both 'sql' and 'advanced'
SELECT title FROM articles WHERE tags @> ARRAY['sql','advanced'];
-- Advanced Indexing

-- Count tags per article
SELECT title, array_length(tags) AS tag_count FROM articles;

-- Collect all unique tags
SELECT array_agg(DISTINCT tag) FROM (
    SELECT array_to_string(tags, ',') AS tag FROM articles
);

```

### Multi-dimensional arrays

```
CREATE TABLE matrices (
    id INTEGER PRIMARY KEY,
    data INTEGER[][]
) STRICT;

INSERT INTO matrices VALUES (1, ARRAY[ARRAY[1,2,3], ARRAY[4,5,6]]);

-- Access a row
SELECT data[0] FROM matrices WHERE id = 1;
-- [1,2,3]

-- Access a single element
SELECT data[1][2] FROM matrices WHERE id = 1;
-- 6

-- Get number of rows
SELECT array_length(data) FROM matrices WHERE id = 1;
-- 2

```

### Type checking

 In STRICT tables, array columns check element types during insert and update:

```
CREATE TABLE scores (id INTEGER PRIMARY KEY, vals INTEGER[]) STRICT;

-- OK: exact integers
INSERT INTO scores VALUES (1, '[1, 2, 3]');

-- OK: floats that are exact integers are coerced
INSERT INTO scores VALUES (2, '[1.0, 2.0]');

-- OK: numeric strings are coerced
INSERT INTO scores VALUES (3, '["42", "100"]');

-- Error: non-numeric string in INTEGER array
INSERT INTO scores VALUES (4, '["hello"]');
-- Error: cannot store TEXT value in INTEGER

-- NULL elements are always allowed
INSERT INTO scores VALUES (5, '[1, null, 3]');

```

### See Also

- [Data Types](https://docs.turso.tech/sql-reference/data-types#array-types) covers array column type declarations
- [CREATE TABLE](https://docs.turso.tech/sql-reference/statements/create-table) explains STRICT table requirements
- [Expressions](https://docs.turso.tech/sql-reference/expressions#array-operators) documents array operator syntax
- [Aggregate Functions](https://docs.turso.tech/sql-reference/functions/aggregate) covers `array_agg`
