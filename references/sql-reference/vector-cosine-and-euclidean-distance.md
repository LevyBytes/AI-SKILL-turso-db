# Vector cosine and euclidean distance

[Context index](vector-and-window-function-reference.md) · [Setup and preceding context](vector-float-creation-functions.md) · [Previous phase](vector-alias-quantization-and-binary-creation.md) · [Continue](vector-dot-jaccard-and-extraction.md)

### vector_distance_cos

 Calculates cosine distance for two vectors, expressed as `1 - cosine_similarity`. A value of 0 indicates the same direction, while 1 indicates orthogonality.

```
vector_distance_cos(v1, v2)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `v1` | BLOB | First vector |
| `v2` | BLOB | Second vector, with the same dimension count as `v1` |

 **Returns:** REAL - the cosine distance for the pair of vectors.

```
SELECT vector_distance_cos(
    vector32('[1.0, 0.0, 0.0]'),
    vector32('[0.0, 1.0, 0.0]')
);
-- 1.0 (orthogonal vectors)

SELECT vector_distance_cos(
    vector32('[1.0, 0.0]'),
    vector32('[1.0, 0.0]')
);
-- 0.0 (identical vectors)

```

### vector_distance_l2

 Calculates the Euclidean (L2) distance for two vectors. If the result is 0, the vectors match exactly.

```
vector_distance_l2(v1, v2)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `v1` | BLOB | First vector |
| `v2` | BLOB | Second vector, with the same dimension count as `v1` |

 **Returns:** REAL - the Euclidean distance for the pair of vectors.

```
SELECT vector_distance_l2(
    vector32('[1.0, 0.0, 0.0]'),
    vector32('[0.0, 1.0, 0.0]')
);
-- 1.4142135623730951

SELECT vector_distance_l2(
    vector32('[3.0, 4.0]'),
    vector32('[0.0, 0.0]')
);
-- 5.0

```
