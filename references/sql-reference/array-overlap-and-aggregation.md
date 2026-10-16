# Array overlap and aggregation

[Context index](array-function-reference.md) · [Setup and preceding context](array-construction-and-string-parsing.md) · [Previous phase](array-string-conversion-and-containment.md) · [Continue](array-containment-comparison-and-concatenation-operators.md)

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
