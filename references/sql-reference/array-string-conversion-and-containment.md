# Array string conversion and containment

[Context index](array-function-reference.md) · [Setup and preceding context](array-construction-and-string-parsing.md) · [Previous phase](array-membership-position-and-slicing.md) · [Continue](array-overlap-and-aggregation.md)

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
