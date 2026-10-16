# Content cache session tracking and statistics

[Context index](caching-secrets-and-vector-workflows.md) · [Setup and preceding context](content-cache-schema-and-version-storage.md)

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
