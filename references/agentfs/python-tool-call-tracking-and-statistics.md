# Python tool call tracking and statistics

[Context index](agentfs-language-sdks.md) · [Setup and preceding context](python-setup-and-key-value-storage.md) · [Previous phase](python-key-lookup-and-filesystem.md) · [Continue](python-complete-example-and-core-api.md)

### Tool Calls

 Capture tool/function calls so they can be reviewed during debugging and audits.

### Start and Complete

```
# Start a tool call
call_id = await agent.tools.start('search', {'query': 'Python'})

# ... perform the operation ...

# Mark as successful
await agent.tools.success(call_id, {'results': ['result1', 'result2']})

# Or mark as failed
await agent.tools.error(call_id, 'Connection timeout')

```

### Record Completed Call

 When both the start time and end time are available:

```
await agent.tools.record(
    name='search',
    started_at=1234567890.0,
    completed_at=1234567892.0,
    parameters={'query': 'Python'},
    result={'results': ['result1']}
)

```

### Query Tool Calls

```
# Get by name
calls = await agent.tools.get_by_name('search', limit=10)

# Get recent calls
recent = await agent.tools.get_recent(since=1234567890.0)

# Get by ID
call = await agent.tools.get(42)

```

### Get Statistics

```
stats = await agent.tools.get_stats()
for stat in stats:
    print(f"{stat.name}:")
    print(f"  Total calls: {stat.total_calls}")
    print(f"  Successful: {stat.successful}")
    print(f"  Failed: {stat.failed}")
    print(f"  Avg duration: {stat.avg_duration_ms:.2f}ms")

```
