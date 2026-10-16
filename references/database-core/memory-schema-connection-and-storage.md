# Memory schema connection and storage

[Context index](agent-memory-and-code-indexing.md) · [Continue](memory-retrieval-correction-and-task-tracking.md)

## Schema

AI agents begin each session without prior context. A persistent memory system lets them save lessons and retrieve relevant context with vector search. Turso's embedded database with built-in vector functions supports this in one local file. This guide explains the schema and queries for creating a memory system based on vector similarity search.

### Schema

 A minimal memory system only needs two tables: one table for memories and another table for tasks. Per-task memory usage needs the retrieval-attribution join table described in the follow-on guidance.

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
// SCHEMA is a string containing both CREATE TABLE statements above.
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
await (await db.prepare(`
  INSERT INTO memories (id, content, embedding, category, created_at)
  VALUES (?, ?, vector8(?), ?, ?)
`)).run(id, content, JSON.stringify(Array.from(embedding)), "correction", Date.now());

```
