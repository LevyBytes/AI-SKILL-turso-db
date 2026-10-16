# Python setup and key value storage

[Context index](agentfs-language-sdks.md) · [Continue](python-key-lookup-and-filesystem.md)

## Installation

The AgentFS Python SDK offers async APIs for AI agents that need durable storage.

### Installation

```
pip install agentfs-sdk

```

### Quick Start

```
import asyncio
from agentfs_sdk import AgentFS, AgentFSOptions

async def main():
    # Open an agent filesystem
    agent = await AgentFS.open(AgentFSOptions(id='my-agent'))

    # Use key-value store
    await agent.kv.set('config', {'debug': True})
    config = await agent.kv.get('config')

    # Use filesystem
    await agent.fs.write_file('/notes.txt', 'Hello, AgentFS!')
    content = await agent.fs.read_file('/notes.txt')

    # Track tool calls
    call_id = await agent.tools.start('search', {'query': 'Python'})
    await agent.tools.success(call_id, {'results': ['result1']})

    await agent.close()

asyncio.run(main())

```

### Opening a Filesystem

### By Agent ID

 Initializes the database under `.agentfs/{id}.db`:

```
agent = await AgentFS.open(AgentFSOptions(id='my-agent'))

```

### By Path

 Specify a custom database path:

```
agent = await AgentFS.open(AgentFSOptions(path='./data/mydb.db'))

```

### Context Manager

 Automatically closes the database:

```
async with await AgentFS.open(AgentFSOptions(id='my-agent')) as agent:
    await agent.kv.set('key', 'value')
    # Database is closed when exiting

```

### Key-Value Store

 Basic key-value persistence using JSON serialization.

### Set a Value

```
await agent.kv.set('user:123', {'name': 'Alice', 'age': 30})
await agent.kv.set('counter', 42)
await agent.kv.set('active', True)

```
