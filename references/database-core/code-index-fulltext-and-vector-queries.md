# Code index fulltext and vector queries

[Context index](agent-memory-and-code-indexing.md) · [Setup and preceding context](code-index-schema-and-connection.md) · [Previous phase](code-index-upserts-and-embeddings.md) · [Continue](code-index-hybrid-search-and-cleanup.md)

### Creating the FTS index

 Create one FTS table for each codebase, then fill it from the chunks:

```
-- Create the FTS table
CREATE TABLE IF NOT EXISTS fts_1 (
    chunk_id    INTEGER NOT NULL REFERENCES chunks(id) ON DELETE CASCADE,
    name        TEXT NOT NULL DEFAULT '',
    signature   TEXT NOT NULL DEFAULT ''
);

-- Populate from chunks
INSERT INTO fts_1 (chunk_id, name, signature)
SELECT id, name, signature FROM chunks WHERE codebase_id = 1;

-- Create the FTS index with weighted columns
CREATE INDEX IF NOT EXISTS idx_fts_1 ON fts_1
    USING fts (name, signature)
    WITH (
        tokenizer = 'default',
        weights = 'name=5.0,signature=3.0'
    );

```

 The `weights` parameter tunes BM25 scoring; in this example, hits in the function/type `name` receive 5x weight and hits in `signature` receive 3x weight.

### Searching with FTS

```
SELECT chunk_id, fts_score(name, signature, ?1) AS score
FROM fts_1
WHERE fts_match(name, signature, ?1)
ORDER BY score DESC
LIMIT ?;

```

 Next, retrieve the complete chunk data:

```
SELECT chunk_key, file_path, name, kind, signature, snippet, start_line, end_line
FROM chunks WHERE id = ?;

```

### Vector search

 Use vector cosine distance for semantic or natural language queries:

```
SELECT chunk_key, file_path, name, kind, signature, snippet, start_line, end_line,
       vector_distance_cos(embedding, vector8(?)) AS distance
FROM chunks
WHERE embedding IS NOT NULL
ORDER BY distance ASC
LIMIT ?;

```

 Calculate the score as `1 - distance`, which converts cosine distance into cosine similarity.
