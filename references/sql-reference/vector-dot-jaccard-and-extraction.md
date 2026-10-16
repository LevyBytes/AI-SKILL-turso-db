# Vector dot jaccard and extraction

[Context index](vector-and-window-function-reference.md) · [Setup and preceding context](vector-float-creation-functions.md) · [Previous phase](vector-cosine-and-euclidean-distance.md) · [Continue](vector-concatenation-and-slicing.md)

### vector_distance_dot

 Calculates distance using the negative dot product of two vectors. Because the dot product is negated, lower and more negative results correspond to stronger similarity.

```
vector_distance_dot(v1, v2)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `v1` | BLOB | First vector |
| `v2` | BLOB | Second vector, with the same dimension count as `v1` |

 **Returns:** REAL - the negative dot product for the pair of vectors.

```
SELECT vector_distance_dot(
    vector32('[1.0, 2.0, 3.0]'),
    vector32('[4.0, 5.0, 6.0]')
);
-- -32.0 (dot product is 32)

```

### vector_distance_jaccard

 Calculates Jaccard distance for two binary vectors. It is intended primarily for vectors produced with `vector1bit`.

```
vector_distance_jaccard(v1, v2)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `v1` | BLOB | First vector |
| `v2` | BLOB | Second vector, with the same dimension count as `v1` |

 **Returns:** REAL - the Jaccard distance for the pair of vectors.

---

### Utility Functions

### vector_extract

 Turns a vector BLOB into its JSON text form.

```
vector_extract(vector_blob)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `vector_blob` | BLOB | Vector produced by `vector32`, `vector64`, or a different vector function |

 **Returns:** TEXT - a JSON array string containing the vector components.

```
SELECT vector_extract(vector32('[1.5, 2.5, 3.5]'));
-- [1.500000,2.500000,3.500000]

```
