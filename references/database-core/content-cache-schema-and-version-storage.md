# Content cache schema and version storage

[Context index](caching-secrets-and-vector-workflows.md) · [Continue](content-cache-session-tracking-and-statistics.md)

## Schema

Agents and tools often spend tokens rereading file contents that have not changed. An embedded Turso database can act as a content-addressed cache, comparing hashes so unchanged files produce diffs or compact status responses rather than full payloads. This guide shows the schema and query flow for a per-session file content cache.

### Schema

 This cache is built from four tables: file-version storage, session-specific read positions, and two statistics tables.

```
CREATE TABLE IF NOT EXISTS file_versions (
  path        TEXT NOT NULL,
  hash        TEXT NOT NULL,
  content     TEXT NOT NULL,
  lines       INTEGER NOT NULL,
  created_at  INTEGER NOT NULL,
  PRIMARY KEY (path, hash)
);

CREATE TABLE IF NOT EXISTS session_reads (
  session_id  TEXT NOT NULL,
  path        TEXT NOT NULL,
  hash        TEXT NOT NULL,
  read_at     INTEGER NOT NULL,
  PRIMARY KEY (session_id, path)
);

CREATE TABLE IF NOT EXISTS stats (
  key   TEXT PRIMARY KEY,
  value INTEGER NOT NULL DEFAULT 0
);

CREATE TABLE IF NOT EXISTS session_stats (
  session_id  TEXT NOT NULL,
  key         TEXT NOT NULL,
  value       INTEGER NOT NULL DEFAULT 0,
  PRIMARY KEY (session_id, key)
);

INSERT OR IGNORE INTO stats (key, value) VALUES ('tokens_saved', 0);

```

### How it fits together

- **`file_versions`** stores content by address. Because `(path, hash)` is the composite primary key, several versions of one path can coexist. If a file returns to an earlier state, such as after switching branches, that prior content is already present.
- **`session_reads`** records the last version observed by each session for each path. The `(session_id, path)` key lets every session maintain a separate view of its read history.
- **`stats`** and **`session_stats`** hold aggregate token-savings counts for all usage and for individual sessions.

### Connecting

```
import { connect } from "@tursodatabase/database";

const db = await connect(".cache/content.db");
await db.exec(SCHEMA); // The CREATE TABLE statements above

```

### Storing a file version

 For an initial read, or any read after content changes, save the file body under its hash. `INSERT OR IGNORE` turns repeated storage of an existing version into a no-op:

```
INSERT OR IGNORE INTO file_versions (path, hash, content, lines, created_at)
VALUES (?, ?, ?, ?, ?);

```

 Derive the hash from the file bytes, for example with a shortened SHA-256. That makes the cache content-addressed, so identical bytes for one path are stored only once.

### Key design points

- **Content-addressed storage** makes branch changes straightforward. Moving from `main` to another branch and back can reuse the cached versions from the original branch.
- **Session isolation** lets every consumer track its own observed state. Session B still receives complete content even when Session A previously cached that file.
- **Hash-based change detection** happens during reads, so correctness does not depend on polling or file watchers.
- An embedded Turso database stores the cache in one local file and avoids network round trips.

### Example

 [cachebro](https://github.com/glommer/cachebro) is an MCP server using this design to provide AI coding agents with a drop-in cached file reader.
