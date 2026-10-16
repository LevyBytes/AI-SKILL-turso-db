# Array concatenation and removal

[Context index](array-function-reference.md) · [Setup and preceding context](array-construction-and-string-parsing.md) · [Previous phase](array-length-append-and-prepend.md) · [Continue](array-membership-position-and-slicing.md)

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
