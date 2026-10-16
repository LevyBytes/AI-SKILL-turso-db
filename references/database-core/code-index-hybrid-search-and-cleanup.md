# Code index hybrid search and cleanup

[Context index](agent-memory-and-code-indexing.md) · [Setup and preceding context](code-index-schema-and-connection.md) · [Previous phase](code-index-fulltext-and-vector-queries.md)

### Hybrid search

 Combine FTS and vector search with Reciprocal Rank Fusion (RRF) for stronger results. Run the FTS and vector queries independently, then merge the matches in application code:

```
// Run both searches
const ftsResults = await ftsSearch(query, limit);
const vecResults = await vectorSearch(queryEmbedding, limit);

// Reciprocal Rank Fusion
const k = 60; // RRF constant
const scores = new Map();

ftsResults.forEach((r, i) => {
  const key = r.chunk_key;
  scores.set(key, (scores.get(key) || 0) + 1 / (k + i + 1));
});

vecResults.forEach((r, i) => {
  const key = r.chunk_key;
  scores.set(key, (scores.get(key) || 0) + 1 / (k + i + 1));
});

// Sort by combined score
const merged = [...scores.entries()]
  .sort((a, b) => b[1] - a[1])
  .slice(0, limit);

```

### Removing stale files

 When source files are removed from a codebase, delete their chunks and tracking rows:

```
DELETE FROM chunks WHERE codebase_id = ? AND file_path = ?;
DELETE FROM indexed_files WHERE codebase_id = ? AND file_path = ?;

```

 After stale rows are deleted, rebuild the FTS table so the index stays consistent:

```
DROP TABLE IF EXISTS fts_1;
-- Then recreate and repopulate as shown above

```

### Key design points

- **Incremental indexing** uses file hashes so only changed files are parsed and embedded again, which keeps updates fast on large codebases.
- **Two search modes** - FTS for identifier or keyword lookup, and vector search for semantic or natural language queries - address different search needs. Hybrid search uses both.
- **Weighted FTS** (`name=5.0, signature=3.0`) favors results that match function and type names, which is the common developer search target.
- **Int8 quantized vectors** through `vector8()` lower storage by 75% compared to float32 with little effect on search quality.
- The index lives in one embedded database file and needs no external services.

### Example

 [codemogger](https://github.com/glommer/codemogger) is an MCP server that uses this pattern as a code indexer for AI coding agents.
