# Code index upserts and embeddings

[Context index](agent-memory-and-code-indexing.md) · [Setup and preceding context](code-index-schema-and-connection.md) · [Continue](code-index-fulltext-and-vector-queries.md)

### Upserting chunks

 After parsing a file, upsert its chunks. On conflict, setting `embedding = NULL` ensures changed content is embedded again:

```
INSERT INTO chunks (codebase_id, file_path, chunk_key, language, kind, name,
                    signature, snippet, start_line, end_line, file_hash, indexed_at)
VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?)
ON CONFLICT(chunk_key) DO UPDATE SET
  language = excluded.language,
  kind = excluded.kind,
  name = excluded.name,
  signature = excluded.signature,
  snippet = excluded.snippet,
  start_line = excluded.start_line,
  end_line = excluded.end_line,
  file_hash = excluded.file_hash,
  indexed_at = excluded.indexed_at,
  embedding = NULL,
  embedding_model = '';

```

### Tracking indexed files

```
INSERT INTO indexed_files (codebase_id, file_path, file_hash, chunk_count, indexed_at)
VALUES (?, ?, ?, ?, ?)
ON CONFLICT(codebase_id, file_path) DO UPDATE SET
  file_hash = excluded.file_hash,
  chunk_count = excluded.chunk_count,
  indexed_at = excluded.indexed_at;

```

### Skipping unchanged files

 Check whether a file changed before parsing it:

```
SELECT file_hash FROM indexed_files WHERE codebase_id = ? AND file_path = ?;

```

 Skip the file completely when the hash is unchanged.

### Storing embeddings

 Store embeddings as int8-quantized vectors with Turso's `vector8()` function. This reduces storage from 1,536 bytes (float32, 384 dims) to 395 bytes:

```
UPDATE chunks SET embedding = vector8(?), embedding_model = ? WHERE chunk_key = ?;

```

 `vector8()` accepts a JSON-stringified float array, and Turso performs quantization internally. Query for chunks that still need embeddings:

```
SELECT chunk_key, name, signature, file_path, kind, snippet
FROM chunks
WHERE codebase_id = ? AND (embedding IS NULL OR embedding_model != ?)
LIMIT ?;

```

### Full-text search

 Turso provides an FTS index with weighted BM25 scoring, independent of SQLite's `fts5` virtual tables.
