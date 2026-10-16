# Code index schema and connection

[Context index](agent-memory-and-code-indexing.md) · [Continue](code-index-upserts-and-embeddings.md)

## Schema

AI coding agents are more effective when they can ask semantic questions about a codebase, such as where authentication is implemented, instead of depending only on filenames or grep. A Turso-backed code index can keep identifier-oriented full-text search and embedding-based vector similarity together in one embedded database. This guide describes the table layout and query flow for a code indexer that uses FTS alongside vector search.

### Schema

```
CREATE TABLE IF NOT EXISTS codebases (
    id          INTEGER PRIMARY KEY AUTOINCREMENT,
    root_path   TEXT NOT NULL UNIQUE,
    name        TEXT NOT NULL DEFAULT '',
    indexed_at  INTEGER NOT NULL DEFAULT 0
);

CREATE TABLE IF NOT EXISTS chunks (
    id              INTEGER PRIMARY KEY AUTOINCREMENT,
    codebase_id     INTEGER NOT NULL REFERENCES codebases(id),
    file_path       TEXT NOT NULL,
    chunk_key       TEXT NOT NULL UNIQUE,
    language        TEXT NOT NULL,
    kind            TEXT NOT NULL,
    name            TEXT NOT NULL DEFAULT '',
    signature       TEXT NOT NULL DEFAULT '',
    snippet         TEXT NOT NULL,
    start_line      INTEGER NOT NULL,
    end_line        INTEGER NOT NULL,
    file_hash       TEXT NOT NULL,
    indexed_at      INTEGER NOT NULL,
    embedding       F8_BLOB(384),
    embedding_model TEXT DEFAULT ''
);

CREATE TABLE IF NOT EXISTS indexed_files (
    id          INTEGER PRIMARY KEY AUTOINCREMENT,
    codebase_id INTEGER NOT NULL REFERENCES codebases(id),
    file_path   TEXT NOT NULL,
    file_hash   TEXT NOT NULL,
    chunk_count INTEGER NOT NULL DEFAULT 0,
    indexed_at  INTEGER NOT NULL,
    UNIQUE(codebase_id, file_path)
);

```

### How it fits together

- **`codebases`** identifies each project root. One database can contain indexes for multiple codebases.
- **`chunks`** contains the semantic units extracted from source files, such as functions, structs, classes, impl blocks, and similar units. Each chunk includes a `name`, `signature`, code `snippet`, line range, and optional vector `embedding`. The `chunk_key` is a stable unique identifier, for example `codebase_id::file_path::stable_unit_id`, used for upsert operations. Derive `stable_unit_id` from the parser's qualified declaration identity, including an overload discriminator where needed.
- **`indexed_files`** stores the indexed files and their content hashes so incremental re-indexing can process only files that changed.

### Connecting

 FTS index support must be enabled with an experimental flag when connecting:

```
import { connect } from "@tursodatabase/database";

const db = await connect(".index/code.db", {
  experimental: ["index_method"],
});
await db.exec(SCHEMA);

```

 The `experimental: ["index_method"]` flag turns on Turso's `USING fts` index syntax along with the `fts_match()` / `fts_score()` functions.

### Indexing files
