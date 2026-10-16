# Audit timeline filters and JSON

[Context index](filesystem-and-session-workflows.md) · [Continue](audit-files-diffs-and-related-guides.md)

This reference covers timeline inspection, MCP and NFS server startup, overlay behavior, supported agent workflows, session creation, sync setup, and installation.

## Viewing the Timeline

AgentFS logs each file operation and tool call, so agent activity can be inspected end to end. That record supports debugging, compliance review, and analysis of how agents behave.

### Viewing the Timeline

 Show tool calls in time order:

```
agentfs timeline my-session

```

 Output:

```
ID   TOOL                 STATUS       DURATION STARTED
4    execute_code         pending            -- 2024-01-05 09:44:20
3    api_call             error           300ms 2024-01-05 09:44:15
2    read_file            success          50ms 2024-01-05 09:44:10
1    web_search           success        1200ms 2024-01-05 09:43:45

```

### Filtering Results

 Show only specific tool types:

```
agentfs timeline my-session --filter web_search

```

 Show only errors:

```
agentfs timeline my-session --status error

```

 Limit number of entries:

```
agentfs timeline my-session --limit 20

```

### JSON Output

 For programmatic analysis:

```
agentfs timeline my-session --format json

```

```
[
  {
    "id": 1,
    "name": "web_search",
    "status": "success",
    "started_at": 1704447825,
    "completed_at": 1704447826,
    "duration_ms": 1200,
    "parameters": {"query": "AI agents"},
    "result": {"results": ["result1", "result2"]}
  }
]

```
