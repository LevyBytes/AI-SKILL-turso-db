# Vector distances and utility functions

[Context index](caching-secrets-and-vector-workflows.md) · [Setup and preceding context](vector-types-and-document-storage.md) · [Continue](vector-slicing-and-semantic-search.md)

### Cosine Distance

 Computes vector angle while disregarding magnitude. Results range from 0 for identical direction to 2 for opposite direction.

```
SELECT title,
       vector_distance_cos(embedding, vector32('[0.25, 0.55, 0.15, 0.75]')) AS distance
FROM documents
ORDER BY distance
LIMIT 5;

```

 Useful for document similarity and text embeddings when direction is more important than magnitude.

### Euclidean (L2) Distance

 Computes straight-line distance in n-dimensional space. `vector1bit` vectors do not support it.

```
SELECT title,
       vector_distance_l2(embedding, vector32('[0.25, 0.55, 0.15, 0.75]')) AS distance
FROM documents
ORDER BY distance
LIMIT 5;

```

 Useful for image embeddings, spatial values, and unnormalized embeddings where absolute differences are meaningful.

### Dot Product Distance

 Calculates the negative dot product, `-sum(v1[i] * v2[i])`. More negative results represent stronger similarity.

```
SELECT title,
       vector_distance_dot(embedding, vector32('[0.25, 0.55, 0.15, 0.75]')) AS distance
FROM documents
ORDER BY distance
LIMIT 5;

```

 Works well for normalized embeddings, where it produces the same ranking as cosine distance for unit-length vectors (cosine distance equals one plus the negative dot product), and for maximum inner product search (MIPS).

### Jaccard Distance

 Calculates weighted Jaccard distance from the ratio between per-dimension minimum and maximum values. For `vector1bit`, it uses binary Jaccard distance.

```
SELECT title,
       vector_distance_jaccard(embedding, vector32_sparse('[0.0, 1.0, 0.0, 2.0, 0.0]')) AS distance
FROM sparse_documents
ORDER BY distance
LIMIT 5;

```

 Useful for sparse vectors, set-style comparisons, TF-IDF data, and binary similarity with `vector1bit`.

### Utility Functions

### vector_extract

 Turn a vector BLOB into a readable JSON representation:

```
SELECT vector_extract(embedding) FROM documents WHERE id = 1;
-- [0.200000,0.500000,0.100000,0.800000]

```

### vector_concat

 Concatenate two vectors into one:

```
SELECT vector_extract(
    vector_concat(vector32('[1.0, 2.0]'), vector32('[3.0, 4.0]'))
);
-- [1.000000,2.000000,3.000000,4.000000]

```
