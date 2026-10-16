# Array membership position and slicing

[Context index](array-function-reference.md) · [Setup and preceding context](array-construction-and-string-parsing.md) · [Previous phase](array-concatenation-and-removal.md) · [Continue](array-string-conversion-and-containment.md)

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
