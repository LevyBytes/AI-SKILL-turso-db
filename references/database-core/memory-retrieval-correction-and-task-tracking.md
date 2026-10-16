# Memory retrieval correction and task tracking

[Context index](agent-memory-and-code-indexing.md) · [Setup and preceding context](memory-schema-connection-and-storage.md)

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
