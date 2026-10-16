# Array length append and prepend

[Context index](array-function-reference.md) · [Setup and preceding context](array-construction-and-string-parsing.md) · [Previous phase](array-subscripts-and-slices.md) · [Continue](array-concatenation-and-removal.md)

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
