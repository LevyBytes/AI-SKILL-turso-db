# Vector slicing and semantic search

[Context index](caching-secrets-and-vector-workflows.md) · [Setup and preceding context](vector-types-and-document-storage.md) · [Previous phase](vector-distances-and-utility-functions.md)

### vector_slice

 Take a contiguous slice from a vector, using zero-based indexes with an exclusive end:

```
SELECT vector_extract(
    vector_slice(vector32('[1.0, 2.0, 3.0, 4.0, 5.0]'), 1, 4)
);
-- [2.000000,3.000000,4.000000]

```

### Limitations

- Euclidean distance is **not supported** for `vector1bit` vectors
- Maximum vector dimensionality is 65,536
- For `vector1bit`, cosine distance reports Hamming distance, the count of different bits, rather than standard cosine distance

### Example: Semantic Search

 A full example that combines vector storage with similarity search:

```
-- Create a table with vector embeddings
CREATE TABLE articles (
    id INTEGER PRIMARY KEY,
    title TEXT NOT NULL,
    content TEXT,
    embedding BLOB
);

-- Insert articles with precomputed embeddings
INSERT INTO articles (title, content, embedding) VALUES
    ('Introduction to Machine Learning', 'ML is a subset of AI...', vector32('[0.12, -0.34, 0.56, 0.78]')),
    ('Database Design Patterns', 'Relational databases organize...', vector32('[0.82, 0.15, -0.44, 0.23]')),
    ('Neural Networks Explained', 'Neural networks are computing...', vector32('[0.14, -0.28, 0.62, 0.71]')),
    ('SQL Query Optimization', 'Efficient queries start with...', vector32('[0.75, 0.22, -0.38, 0.19]'));

-- Find the 3 most similar articles to a query vector
SELECT
    title,
    content,
    vector_distance_cos(embedding, vector32('[0.10, -0.30, 0.50, 0.80]')) AS distance
FROM articles
ORDER BY distance
LIMIT 3;

```

 Similarity search scans the table linearly. On large datasets, narrow the candidate rows with a `WHERE` clause when possible.

### See Also

- [Vector Functions](https://docs.turso.tech/sql-reference/functions/vector) - complete SQL reference for vector functions
- [Data Types](https://docs.turso.tech/sql-reference/data-types) - details on how Turso handles BLOBs
