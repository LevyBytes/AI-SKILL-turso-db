# Vector concatenation and slicing

[Context index](vector-and-window-function-reference.md) · [Setup and preceding context](vector-float-creation-functions.md) · [Previous phase](vector-dot-jaccard-and-extraction.md) · [Continue](vector-storage-and-similarity-examples.md)

### vector_concat

 Joins two or more vectors into one vector.

```
vector_concat(v1, v2, ...)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `v1, v2, ...` | BLOB | Two or more vectors to join. Every vector must share the same type. |

 **Returns:** BLOB - a new vector whose dimensions are the input vectors laid out sequentially.

```
SELECT vector_extract(
    vector_concat(
        vector32('[1.0, 2.0]'),
        vector32('[3.0, 4.0]')
    )
);
-- [1.000000,2.000000,3.000000,4.000000]

```

### vector_slice

 Takes a continuous segment from a vector.

```
vector_slice(vector_blob, start, end)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `vector_blob` | BLOB | The source vector |
| `start` | INTEGER | Start index (zero-based, inclusive) |
| `end` | INTEGER | End index (exclusive) |

 **Returns:** BLOB - a new vector containing the dimensions beginning at `start` and ending at `end - 1` (excluding `end`).

```
SELECT vector_extract(
    vector_slice(vector32('[10.0, 20.0, 30.0, 40.0, 50.0]'), 1, 4)
);
-- [20.000000,30.000000,40.000000]

```

---

### Examples
