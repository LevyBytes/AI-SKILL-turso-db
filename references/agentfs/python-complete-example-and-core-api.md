# Python complete example and core API

[Context index](agentfs-language-sdks.md) · [Setup and preceding context](python-setup-and-key-value-storage.md) · [Previous phase](python-tool-call-tracking-and-statistics.md) · [Continue](python-filesystem-and-tool-api.md)

### Complete Example

```
import asyncio
import time
from agentfs_sdk import AgentFS, AgentFSOptions

async def main():
    async with await AgentFS.open(AgentFSOptions(id='demo-agent')) as agent:
        # Store configuration
        await agent.kv.set('agent:config', {
            'model': 'gpt-4',
            'temperature': 0.7
        })

        # Create a research document
        research = """
        # Research Notes

        ## Topic: AgentFS

        AgentFS provides persistent storage for AI agents.
        """

        await agent.fs.write_file('/research/agentfs.md', research)

        # Track a simulated tool call
        call_id = await agent.tools.start('web_search', {
            'query': 'AgentFS documentation'
        })

        # Simulate some work
        await asyncio.sleep(0.1)

        await agent.tools.success(call_id, {
            'results_count': 5,
            'top_result': 'https://docs.turso.tech/agentfs'
        })

        # List what we created
        files = await agent.fs.readdir('/research')
        print(f"Files: {files}")

        # Get tool stats
        stats = await agent.tools.get_stats()
        for stat in stats:
            print(f"{stat.name}: {stat.total_calls} calls")

asyncio.run(main())

```

### API Reference

### AgentFS

| Method | Description |
| --- | --- |
| `AgentFS.open(options)` | Create or access an agent filesystem |
| `agent.close()` | Close the database connection |
| `agent.kv` | Key-value store interface |
| `agent.fs` | Filesystem interface |
| `agent.tools` | Tool calls interface |

### AgentFSOptions

| Property | Type | Description |
| --- | --- | --- |
| `id` | `str` | Agent identifier (creates `.agentfs/{id}.db`) |
| `path` | `str` | Custom database path |

### KvStore

| Method | Description |
| --- | --- |
| `set(key, value)` | Store a value |
| `get(key)` | Retrieve a value |
| `delete(key)` | Delete a key |
| `list(prefix)` | List keys with prefix |
