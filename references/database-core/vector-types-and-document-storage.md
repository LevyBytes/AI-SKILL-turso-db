# Vector types and document storage

[Context index](caching-secrets-and-vector-workflows.md) · [Continue](vector-distances-and-utility-functions.md)

## Vector Types

Turso includes native vector search, so no extension is needed. Embeddings can sit beside relational rows and be searched with built-in distance functions.

### Vector Types

 Turso offers **dense**, **sparse**, **quantized**, and **binary** vector formats, with each format matching different workload needs.

### Dense Vectors

 Dense vectors keep a value in every dimension. Turso supports two precision choices:

| Function | Precision | Storage per dimension | Best for |
| --- | --- | --- | --- |
| `vector32` | 32-bit float | 4 bytes | Common ML embeddings, including OpenAI and sentence transformers |
| `vector64` | 64-bit float | 8 bytes | Applications requiring higher precision |

### Sparse Vectors

 Sparse vectors record only non-zero entries and their indexes, which reduces memory use for high-dimensional data dominated by zeros.

| Function | Storage | Best for |
| --- | --- | --- |
| `vector32_sparse` | Non-zero values + indices | TF-IDF, bag-of-words, and sparse high-dimensional datasets |

### Quantized Vectors

| Function | Storage per dimension | Best for |
| --- | --- | --- |
| `vector8` | 1 byte (+8 bytes overhead) | Large-scale search cases where ~4x compression over Float32 is an acceptable trade for small precision loss |

 Values use min/max scaling for linear quantization into the 0-255 range. Dequantization: `f_i = alpha * q_i + shift`.

### Binary Vectors

| Function | Storage per dimension | Best for |
| --- | --- | --- |
| `vector1bit` | 1 bit | Binary hashing, approximate nearest neighbor search (~32x compression vs Float32) |

 Positive inputs map to 1, while zero or negative inputs map to 0. Extracted output is shown as +1/-1. For most uses, begin with `vector32`; consider smaller formats when the table contains many rows.

### Storing Vectors

 Create a table with a BLOB field so embeddings can be stored with your relational columns:

```
CREATE TABLE documents (
    id INTEGER PRIMARY KEY,
    title TEXT,
    content TEXT,
    embedding BLOB
);

```

 Use the matching conversion function when inserting rows that contain vector embeddings:

```
INSERT INTO documents (title, content, embedding) VALUES
    ('Machine learning basics', 'An introduction to ML concepts...', vector32('[0.2, 0.5, 0.1, 0.8]')),
    ('Database fundamentals', 'How relational databases work...', vector32('[0.1, 0.3, 0.9, 0.2]')),
    ('Neural networks guide', 'Deep learning architectures...', vector32('[0.3, 0.6, 0.2, 0.7]'));

```

 Sparse-vector conversion compresses zero entries automatically:

```
CREATE TABLE sparse_documents (
    id INTEGER PRIMARY KEY, title TEXT, content TEXT, embedding BLOB
);
INSERT INTO sparse_documents (title, content, embedding) VALUES
    ('Sparse example', 'A document with sparse features...', vector32_sparse('[0.0, 1.5, 0.0, 2.3, 0.0]'));

```

### Similarity Search

 Distance functions find vectors nearest to a query vector. Each comparison requires both operands to use the **same type and dimensionality**. Smaller distance values mean closer matches.
