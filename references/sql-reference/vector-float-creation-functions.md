# Vector float creation functions

[Context index](vector-and-window-function-reference.md) · [Continue](vector-alias-quantization-and-binary-creation.md)

## Vector Functions

 **Turso Extension**: Turso includes vector functions for handling vector embeddings from SQL. They are Turso-specific additions rather than SQLite standard features. With these built-ins, embeddings can be saved and searched in SQL. The vector values are encoded as compact BLOB data and can be evaluated with distance functions for similarity search.

---

### Vector Creation

### vector32

 Builds a 32-bit floating-point vector from a JSON numeric array.

```
vector32(json_array)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_array` | TEXT | JSON numeric array, for example `'[1.0, 2.0, 3.0]'` |

 **Returns:** BLOB - a vector encoded in 32-bit float binary form. This is the typical vector representation, balancing precision with storage size. Each dimension takes 4 bytes.

```
SELECT vector32('[1.0, 2.0, 3.0]');
-- (BLOB: 12 bytes)

-- Store embeddings in a table
CREATE TABLE documents (
    id INTEGER PRIMARY KEY,
    content TEXT,
    embedding BLOB
);

INSERT INTO documents VALUES (1, 'Introduction to databases', vector32('[0.1, 0.3, 0.5, 0.7]'));

```

### vector64

 Builds a 64-bit floating-point vector from a JSON numeric array.

```
vector64(json_array)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_array` | TEXT | JSON numeric array, for example `'[1.0, 2.0, 3.0]'` |

 **Returns:** BLOB - a vector encoded in 64-bit float binary form. Choose this representation for greater precision. Each dimension takes 8 bytes.

```
SELECT vector64('[1.0, 2.0, 3.0]');
-- (BLOB: 24 bytes)

```
