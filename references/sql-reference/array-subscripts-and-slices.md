# Array subscripts and slices

[Context index](array-function-reference.md) · [Setup and preceding context](array-construction-and-string-parsing.md) · [Continue](array-length-append-and-prepend.md)

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
