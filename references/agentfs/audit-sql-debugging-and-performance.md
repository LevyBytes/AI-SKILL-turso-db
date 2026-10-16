# Audit SQL debugging and performance

[Context index](filesystem-and-session-workflows.md) · [Setup and preceding context](audit-timeline-filters-and-json.md) · [Previous phase](audit-files-diffs-and-related-guides.md)

### Querying with SQL

 Because AgentFS is backed by SQLite, arbitrary SQL queries are supported:
-  Turso
-  SQLite

```
tursodb .agentfs/my-session.db

```

```
sqlite3 .agentfs/my-session.db

```

 Example queries:

```
-- Find all tool calls that took longer than 1 second
SELECT name, duration_ms
FROM tool_calls
WHERE duration_ms > 1000;

-- Count tool usage by type
SELECT name, COUNT(*) as count
FROM tool_calls
GROUP BY name
ORDER BY count DESC;

-- Find inodes modified in the last hour
SELECT ino, mtime
FROM fs_inode
WHERE mtime > CAST(strftime('%s', 'now', '-1 hour') AS INTEGER);

-- Sum current regular-file sizes (not cumulative writes)
SELECT COALESCE(SUM(size), 0) as total_bytes
FROM fs_inode
WHERE (mode & 0xF000) = 0x8000;  -- regular files only

```

### Use Cases

### Debugging Agent Failures

 If an agent fails, review the timeline to reconstruct the sequence of events:

```
# See recent activity
agentfs timeline my-session --limit 20

# Focus on errors
agentfs timeline my-session --status error

# Check what files were created/modified
agentfs fs ls my-session

```

### Performance Analysis

 Find slow operations:

```
SELECT name, AVG(duration_ms) as avg_ms, COUNT(*) as count
FROM tool_calls
GROUP BY name
ORDER BY avg_ms DESC;

```

### Compliance Auditing

 Produce a full export of agent activity:

```
agentfs timeline my-session --format json > audit-log.json

```

### Next Steps
