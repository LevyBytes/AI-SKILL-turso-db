# Agent Databases, Ai Memory, Code Indexing

> Database Core reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: Embedded Databases (Local-First); Schema.

## Embedded Databases (Local-First)

Need a full agent state management layer? See [AgentFS](https://docs.turso.tech/agentfs/introduction), Turso's specialized SDK for AI agents that combines filesystem operations, key-value storage, and automatic tool call tracking in one database file. AI agents use databases to keep context, record state, and carry memory between runs. Turso Database supports two main approaches:
- **Embedded Databases** when agent processing should stay local-first, and
- **Turso Sync** when distributed agents need coordination plus cloud persistence.

### Embedded Databases (Local-First)

 Choose embedded databases for agents that stay within one local workflow, need offline execution, or handle temporary private state. They fit local processing pipelines, sensitive work that must remain on the device, dev/test runs, and analysis performed by one agent.

```
import { connect } from "@tursodatabase/database";

// Create a local embedded database for this agent
const db = await connect("agent-workflow.db");

// Agent can now store and query data locally
const stmt = db.prepare(`
  CREATE TABLE IF NOT EXISTS steps (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    action TEXT NOT NULL,
    result TEXT,
    timestamp DATETIME DEFAULT CURRENT_TIMESTAMP
  )
`);
stmt.run();

// Track agent workflow steps
db.prepare("INSERT INTO steps (action, result) VALUES (?, ?)").run(
  "fetch_data",
  "success",
);

```

 This setup gives zero network latency, offline operation, full data privacy, and straightforward single-file deployment. The stored state belongs to that one agent instance. If you need agents to share it or preserve it across sessions, you must handle the database file lifecycle yourself.

### Turso Sync (Cloud-Connected)

 Choose Turso Sync when agents need memory that survives sessions, coordination with other agents, or cloud-backed durability for long-running workflows. Useful cases include conversational agents with session memory, multi-agent coordination systems, recoverable long-running workflows, and agents that report into central systems.

```
import { connect } from "@tursodatabase/database";

// Connect to a synced database (embedded locally, synced to Turso Cloud)
const db = await connect({
  path: "agent-memory.db",
  url: "https://your-database.turso.io",
  authToken: "your-auth-token",
  sync: "full", // Bidirectional sync
});

// Agent can access data from previous sessions or other agents
const previousContext = db
  .prepare("SELECT * FROM conversation_history WHERE session_id = ?")
  .all("session-123");

// New data is automatically synced to cloud and other agent instances
db.prepare("INSERT INTO agent_actions (agent_id, action) VALUES (?, ?)").run(
  "agent-1",
  "processed_document",
);

// Manually trigger sync if needed
await db.sync();

```

 It provides persistent memory across restarts, shared-state coordination across agents, cloud backup and durability, fast local reads, and background sync. **Sync modes:** `sync` (bidirectional), `pull` (cloud to agent), `push` (agent to cloud).

### Multi-Agent Architectures

### Pattern 1: Isolated Agent Databases

 Give each agent its own embedded database when isolation matters. This works best for independent agents that do not share state.

```
// Agent 1
const agent1DB = await connect("agent-1.db");

// Agent 2
const agent2DB = await connect("agent-2.db");

```

 Independent agents that run separate work, need no coordination, and require the strongest isolation.

### Pattern 2: Shared Database with Sync

 Have several agents connect to one synced database. This pattern fits agent systems that coordinate around shared data.

```
// Agent 1 connects and writes tasks
const agent1DB = await connect({
  path: "agent-1-local.db",
  url: "https://shared-tasks.turso.io",
  authToken: process.env.TURSO_TOKEN,
  sync: "full",
});

agent1DB.prepare("INSERT INTO tasks (status) VALUES (?)").run("pending");

// Agent 2 connects to same database and reads tasks
const agent2DB = await connect({
  path: "agent-2-local.db",
  url: "https://shared-tasks.turso.io", // Same database
  authToken: process.env.TURSO_TOKEN,
  sync: "full",
});

const pendingTasks = agent2DB
  .prepare("SELECT * FROM tasks WHERE status = ?")
  .all("pending");

```

 Agents that coordinate through shared task queues or a shared knowledge base.

### Pattern 3: Hub-and-Spoke

 Use a central synced database where agents submit results and retrieve new tasks.

```
// Worker agents push results only
const workerDB = await connect({
  path: "worker-local.db",
  url: "https://hub.turso.io",
  authToken: process.env.WORKER_TOKEN,
  sync: "push", // Only push results to cloud
});

// Coordinator agent pulls from all workers
const coordinatorDB = await connect({
  path: "coordinator-local.db",
  url: "https://hub.turso.io",
  authToken: process.env.COORDINATOR_TOKEN,
  sync: "pull", // Only pull data from cloud
});

```

 Many worker agents send results to a central coordinator for monitoring or aggregation when data should mainly flow in one direction.

### Database-Per-Agent Pattern

 Systems that operate many agents can provision one database per agent with the [Platform API](https://docs.turso.tech/api-reference):

```
import { createClient } from "@tursodatabase/api";

const turso = createClient({
  token: process.env.TURSO_PLATFORM_API_TOKEN,
  org: process.env.TURSO_ORG_NAME,
});

// Create a dedicated database for each agent
async function provisionAgentDatabase(agentId) {
  const database = await turso.databases.create(`agent-${agentId}`, {
    group: "default",
  });

  console.log(`Database created for agent ${agentId}: ${database.Name}`);
  return database;
}

```

 This gives agents full isolation, lets each agent scale independently, improves security because a compromised agent affects only one database, and makes cleanup straightforward when agents are retired.

### Examples

 These examples demonstrate the agent database patterns:
- [sync-node](https://github.com/tursodatabase/turso/blob/main/examples/javascript/sync-node) - Node.js using bidirectional sync with Turso Cloud
- [sync-wasm-vite](https://github.com/tursodatabase/turso/blob/main/examples/javascript/sync-wasm-vite) - Browser (WASM) using bidirectional sync with Turso Cloud
- [database-node](https://github.com/tursodatabase/turso/blob/main/examples/javascript/database-node) - Node.js with a local file database and no sync
- [database-wasm-vite](https://github.com/tursodatabase/turso/blob/main/examples/javascript/database-wasm-vite) - Browser (WASM) with a local database stored in the browser

## Schema

AI agents begin each session without prior context. A persistent memory system lets them save lessons and retrieve relevant context with vector search. Turso's embedded database with built-in vector functions supports this in one local file. This guide explains the schema and queries for creating a memory system based on vector similarity search.

### Schema

 A minimal memory system only needs two tables: one table for memories and another table recording which memories were used for each task.

```
CREATE TABLE IF NOT EXISTS memories (
    id              TEXT PRIMARY KEY,
    content         TEXT NOT NULL,
    embedding       F8_BLOB(384),
    category        TEXT NOT NULL,
    created_at      INTEGER NOT NULL,
    last_retrieved  INTEGER,
    retrieval_count INTEGER DEFAULT 0,
    source_task     TEXT
);

CREATE TABLE IF NOT EXISTS tasks (
    id               TEXT PRIMARY KEY,
    description      TEXT,
    embedding        F8_BLOB(384),
    started_at       INTEGER,
    finished_at      INTEGER
);

```

- **`memories`** keeps each lesson, its vector embedding, and a category such as `correction`, `insight`, `user`, or `discovery`. The embedding is a 384-dimensional int8-quantized vector (`F8_BLOB(384)`).
- **`tasks`** stores every task handled by the agent and links task descriptions to embeddings for later retrieval.

### Connecting

```
import { connect } from "@tursodatabase/database";

const db = await connect(".memory/agent.db");
await db.exec(SCHEMA);

```

### Storing a memory

 When the agent learns a correction, user preference, or insight, save it with an embedding. Use `vector8()` to turn a JSON float array into an int8-quantized vector:

```
INSERT INTO memories (id, content, embedding, category, created_at, source_task)
VALUES (?, ?, vector8(?), ?, ?, ?);

```

 Pass `vector8()` a JSON-stringified float array, for example a 384-dimension embedding from a model like `all-MiniLM-L6-v2`. Turso performs the quantization internally:

```
db.prepare(`
  INSERT INTO memories (id, content, embedding, category, created_at)
  VALUES (?, ?, vector8(?), ?, ?)
`).run(id, content, JSON.stringify(Array.from(embedding)), "correction", Date.now());

```

### Retrieving relevant memories

 At the start of a new task, retrieve the most relevant memories with Turso's `vector_distance_cos()` function:

```
SELECT id, content, category, created_at, retrieval_count,
       vector_distance_cos(embedding, vector8(?)) AS distance
FROM memories
WHERE embedding IS NOT NULL
ORDER BY distance ASC
LIMIT ?;

```

 `vector_distance_cos()` outputs cosine **distance** (0 = identical, 2 = opposite), where lower values rank higher. Convert it to similarity with `1.0 - distance`. After retrieval, refresh the memory's metadata:

```
UPDATE memories SET last_retrieved = ?, retrieval_count = retrieval_count + 1
WHERE id = ?;

```

### Correcting wrong memories

 If a memory is discovered to be incorrect, remove it and insert the corrected version:

```
DELETE FROM memories WHERE id = ?;

INSERT INTO memories (id, content, embedding, category, created_at)
VALUES (?, ?, vector8(?), 'correction', ?);

```

### Task tracking

 Store task records so you can review the agent's recent work:

```
-- Start a task
INSERT INTO tasks (id, description, embedding, started_at)
VALUES (?, ?, vector8(?), ?);

-- Complete a task
UPDATE tasks SET finished_at = ? WHERE id = ?;

```

### Purging old memories

 Delete memories that are old and never retrieved:

```
DELETE FROM memories
WHERE retrieval_count = 0 AND created_at < ?;

```

### Key design points

- **Vector search** relies on Turso's `vector8()` and `vector_distance_cos()` to compute cosine similarity in SQL. Int8 quantization cuts storage by 75% compared to float32 while only slightly affecting search quality.
- For small per-project datasets, scanning the whole table with `ORDER BY distance LIMIT k` is typically fast enough without a vector index.
- The whole system uses one embedded database file and does not require external services.

### Going further

 The preceding schema is intentionally small. Real systems should test strategies for keeping the memory store valuable as it expands. Possible directions include:
- **Weighted memories** - add a `weight REAL` column, then raise or lower memory rank based on whether the agent judged it useful after retrieval. An exponential moving average can update weights over time.
- **Time decay** - include a recency factor in ranking so older memories sink in results. Example: `(1.0 - distance) * POWER(0.95, days_since_last_retrieved)`.
- **Garbage collection** - on a schedule, delete memories with low weight and high retrieval count (retrieved often but never useful), or memories that remain unretrieved beyond a chosen age.
- **Task scoring** - capture task outcome metrics (tokens used, errors, corrections) and derive a credit signal for memories retrieved during that task.
- **Retrieval attribution** - add a join table `(memory_id, task_id, similarity, credit)` so each task records which memories were used and how they were rated.
 No strategy is universally best; it depends on the agent's workload, memory creation rate, and pruning aggressiveness. Begin with the minimal design and refine it.

### Example

 [memelord](https://github.com/glommer/memelord) is an MCP server and hooks system that implements this pattern. It uses reinforcement learning plus weight decay to maintain long-lived memory for AI coding agents.

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
- **`chunks`** contains the semantic units extracted from source files, such as functions, structs, classes, impl blocks, and similar units. Each chunk includes a `name`, `signature`, code `snippet`, line range, and optional vector `embedding`. The `chunk_key` is a stable unique identifier, for example `file_path::kind::name`, used for upsert operations.
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
