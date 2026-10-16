# Extension regexp and vector examples

[Context index](expressions-and-extensions-workflows.md) · [Setup and preceding context](extension-loading-uuid-and-regexp.md) · [Continue](extension-time-functions-and-durations.md)

### Examples

```
-- Pattern matching
SELECT regexp('[0-9]+', 'abc123def');
-- 1

-- The REGEXP operator uses this extension
SELECT 'hello123' REGEXP '[a-z]+[0-9]+';
-- 1

-- Extract matching substring
SELECT regexp_substr('Price: $42.50', '[0-9]+\.[0-9]+');
-- '42.50'

-- Capture groups
SELECT regexp_capture('2025-03-15', '(\d{4})-(\d{2})-(\d{2})', 1);
-- '2025'

-- Replace
SELECT regexp_replace('Hello World', '[aeiou]', '*');
-- 'H*ll* W*rld'

```

### Vector Extension

 **Turso Extension**: Native vector search supports similarity search and semantic search use cases. The vector extension includes functions to create, store, and search vector embeddings. Vectors use BLOB storage. For the full function list, see [Vector Functions](https://docs.turso.tech/sql-reference/functions/vector).

### Quick Reference

| Function | Description |
| --- | --- |
| `vector32(json)` | Create a 32-bit float vector |
| `vector64(json)` | Create a 64-bit float vector |
| `vector_distance_cos(v1, v2)` | Cosine distance |
| `vector_distance_l2(v1, v2)` | Euclidean distance |
| `vector_extract(v)` | Convert vector to JSON |
| `vector_concat(v1, v2)` | Concatenate vectors |
| `vector_slice(v, start, end)` | Extract vector slice |

### Example

```
CREATE TABLE docs (
    id INTEGER PRIMARY KEY,
    content TEXT,
    embedding BLOB
);

INSERT INTO docs VALUES (1, 'database systems', vector32('[0.1, 0.2, 0.3]'));
INSERT INTO docs VALUES (2, 'machine learning', vector32('[0.4, 0.5, 0.6]'));

-- Find similar documents
SELECT content, vector_distance_cos(embedding, vector32('[0.1, 0.25, 0.35]')) AS dist
FROM docs
ORDER BY dist
LIMIT 5;

```

### Time Extension

 The time extension works with [sqlean-time](https://github.com/nalgeon/sqlean/blob/main/docs/time.md) compatibility and adds advanced functions for manipulating time values.
