# Content Caching, Secrets Vault, Vector Search

> Database Core reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: Schema; Vector Types.

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

### Tracking reads per session

 A session must remember the version it most recently read for each file. On the first access, create that read pointer:

```
INSERT OR REPLACE INTO session_reads (session_id, path, hash, read_at)
VALUES (?, ?, ?, ?);

```

 For later accesses, look up the version previously observed by that session:

```
SELECT hash FROM session_reads WHERE session_id = ? AND path = ?;

```

 When the saved hash equals the current hash, the file has not changed, so return a brief status response. When the hashes differ, load the earlier content and use it to produce a diff:

```
SELECT content FROM file_versions WHERE path = ? AND hash = ?;

```

 Afterward, move the session read pointer so it references the new hash:

```
UPDATE session_reads SET hash = ?, read_at = ? WHERE session_id = ? AND path = ?;

```

### Tracking token savings

 Maintain global counts with an atomic counter, and use an upsert for session-level statistics:

```
-- Global counter
UPDATE stats SET value = value + ? WHERE key = 'tokens_saved';

-- Per-session counter (upsert)
INSERT INTO session_stats (session_id, key, value)
VALUES (?, 'tokens_saved', ?)
ON CONFLICT(session_id, key) DO UPDATE SET value = value + ?;

```

### Querying statistics

```
-- Number of distinct files cached
SELECT COUNT(DISTINCT path) as file_count FROM file_versions;

-- Global tokens saved
SELECT value FROM stats WHERE key = 'tokens_saved';

-- Tokens saved in the current session
SELECT value FROM session_stats WHERE session_id = ? AND key = 'tokens_saved';

```

### Cleanup

 If a file disappears from disk, delete both its stored versions and any session pointers for that path:

```
DELETE FROM file_versions WHERE path = ?;
DELETE FROM session_reads WHERE path = ?;

```

 To clear the entire cache:

```
DELETE FROM file_versions;
DELETE FROM session_reads;
DELETE FROM session_stats;
UPDATE stats SET value = 0;

```

### Key design points

- **Content-addressed storage** makes branch changes straightforward. Moving from `main` to another branch and back can reuse the cached versions from the original branch.
- **Session isolation** lets every consumer track its own observed state. Session B still receives complete content even when Session A previously cached that file.
- **Hash-based change detection** happens during reads, so correctness does not depend on polling or file watchers.
- An embedded Turso database stores the cache in one local file and avoids network round trips.

### Example

 [cachebro](https://github.com/glommer/cachebro) is an MCP server using this design to provide AI coding agents with a drop-in cached file reader.

## Schema

AI coding agents often need to operate with real credentials, such as a GitHub API token, a billing-service key, or a deployment token. Pasting those secrets into chat is easy, but it leaves them in model context, transcripts, and provider logs. With an encrypted Turso database as a vault, the agent can use a secret without reading it: application code injects the value into a child process, runs the command, and redacts the captured output before returning anything. This guide explains the schema, encrypted connection setup, and queries for a local vault that allows use without disclosure.

### Schema

 The vault needs two tables: one table for stored secrets and another append-only table for audits.

```
CREATE TABLE IF NOT EXISTS secrets (
  name         TEXT PRIMARY KEY,
  value        TEXT NOT NULL,
  provider     TEXT,
  account      TEXT,
  environment  TEXT,
  access       TEXT,
  tags         TEXT NOT NULL DEFAULT '',
  description  TEXT,
  created_at   TEXT NOT NULL,
  last_used_at TEXT
);

CREATE TABLE IF NOT EXISTS audit_log (
  id         INTEGER PRIMARY KEY AUTOINCREMENT,
  ts         TEXT NOT NULL,
  secrets    TEXT NOT NULL,
  command    TEXT NOT NULL,
  cwd        TEXT NOT NULL,
  exit_code  INTEGER,
  outcome    TEXT NOT NULL
);

```

### How it fits together

- **`secrets`** keeps credentials under the `name` key. The `value` field contains the secret and is accessed only for child-process injection, not for agent display. Metadata fields (`provider`, `account`, `environment`, `access`, `tags`) let an agent choose a credential from attributes without exposing the underlying value.
- **`audit_log`** appends one record for each use, including injected secrets, command text, working directory, exit code, and whether the outcome was `ran` or `denied`.

### Connecting

 The vault lives in one encrypted database file. Supplying a key when connecting makes Turso encrypt pages at rest, while `multiprocess_wal` allows multiple processes, such as agent sessions and a CLI, to open the file safely at the same time:

```
import { connect } from "@tursodatabase/database";

const db = await connect("vault.db", {
  encryption: { cipher: "aes256gcm", hexkey },
  experimental: ["multiprocess_wal"],
});
await db.exec(SCHEMA); // The CREATE TABLE statements above

```

 `hexkey` must be a 64-character hex string. Its source is your choice: derive it from a passphrase with scrypt and avoid storing it, unwrap it from the OS keychain, or retrieve it from a cloud KMS. Turso only requires the key during `connect()`; storage behaves the same no matter where the key originated.

### Storing a secret

 Use `name` as the upsert key so changing a secret value preserves its metadata:

```
INSERT INTO secrets (name, value, provider, account, environment, access, tags, description, created_at)
VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?)
ON CONFLICT(name) DO UPDATE SET
  value       = excluded.value,
  provider    = COALESCE(excluded.provider, secrets.provider),
  account     = COALESCE(excluded.account, secrets.account),
  environment = COALESCE(excluded.environment, secrets.environment),
  access      = COALESCE(excluded.access, secrets.access),
  description = COALESCE(excluded.description, secrets.description);

```

### Listing without exposing values

 The agent-facing list query is written so it does not select the `value` column:

```
SELECT name, provider, account, environment, access, tags, description, created_at, last_used_at
FROM secrets ORDER BY name;

```

 The result includes names and metadata, giving the agent enough information to select a credential while the secret value remains inside the database.

### Using a secret

 To use a credential, retrieve its value, place it in the environment of a child process, execute the command, then redact that value from captured output before returning results:

```
SELECT value FROM secrets WHERE name = ?;

```

 Application code performs both environment injection and output redaction; the database does not do that work:

```
const { value } = await db.prepare(
  "SELECT value FROM secrets WHERE name = ?"
).get([name]);

const result = await runCommand(command, {
  env: { ...process.env, [name]: value },
});

// Replace every occurrence of the value before the agent sees the output
const safe = result.stdout.replaceAll(value, "***");

```

 Redacting captured output does not create a sandbox. Any command able to read a secret could intentionally disclose it by writing a file or sending network traffic. The vault offers a smaller guarantee that is still valuable: the *model* does not receive the value, and accidental output such as `echo $TOKEN` is masked. The secret exists only in the encrypted file and in the temporary process that consumed it.

### Auditing every use

 For every access, append an audit row and refresh the secret's `last_used_at` value:

```
INSERT INTO audit_log (ts, secrets, command, cwd, exit_code, outcome)
VALUES (?, ?, ?, ?, ?, ?);

UPDATE secrets SET last_used_at = ? WHERE name = ?;

```

 Questions such as who used the production key, at what time, and for which command become ordinary database queries:

```
SELECT ts, secrets, command, cwd, exit_code, outcome
FROM audit_log ORDER BY id DESC LIMIT ?;

```

### Key design points

- **Encrypted at rest, for free.** Turso provides whole-database encryption with AES-256-GCM or faster AEGIS options. The vault does not implement ciphers or page handling itself; it derives a key and passes it to `connect()`.
- **No coordinating daemon.** With `multiprocess_wal`, each agent session or CLI run can open the encrypted file directly, using the database itself as shared state instead of relying on a persistent server.
- **The value never appears in a list.** The listing path uses a `SELECT` that leaves out `value`, so agents see only names and attributes.
- **Fail closed.** If the requested secret name is unknown, execution stops before the command starts instead of running with no credential.
- **Auditing is just a table.** Each use creates an `INSERT`, and reports are `SELECT` queries that can feed exports or alerts.

### Example

 [keymaxxer](https://github.com/glommer/keymaxxer) is an MCP server that applies this approach as an encrypted secrets vault for AI coding agents.

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
INSERT INTO documents (title, content, embedding) VALUES
    ('Sparse example', 'A document with sparse features...', vector32_sparse('[0.0, 1.5, 0.0, 2.3, 0.0]'));

```

### Similarity Search

 Distance functions find vectors nearest to a query vector. Each comparison requires both operands to use the **same type and dimensionality**. Smaller distance values mean closer matches.

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

 Works well for normalized embeddings, where it matches cosine distance for unit-length vectors, and for maximum inner product search (MIPS).

### Jaccard Distance

 Calculates weighted Jaccard distance from the ratio between per-dimension minimum and maximum values. For `vector1bit`, it uses binary Jaccard distance.

```
SELECT title,
       vector_distance_jaccard(embedding, vector32_sparse('[0.0, 1.0, 0.0, 2.0]')) AS distance
FROM documents
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
