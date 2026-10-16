# Audit files diffs and related guides

[Context index](filesystem-and-session-workflows.md) · [Setup and preceding context](audit-timeline-filters-and-json.md) · [Continue](audit-sql-debugging-and-performance.md)

### Inspecting Files

 Show every file in a session:

```
agentfs fs ls my-session

```

 Output:

```
d artifacts
f config.json
f output.txt

```

 List a subdirectory:

```
agentfs fs ls my-session /artifacts

```

 Read file contents:

```
agentfs fs cat my-session /output.txt

```

### Viewing Changes (Diff)

 Display the differences from the original filesystem:

```
agentfs diff my-session

```

 This shows:
- New files created
- Modified files
- Deleted files

### Overlay Filesystem

Understanding copy-on-write

### Syncing

Sync sessions to Turso Cloud
