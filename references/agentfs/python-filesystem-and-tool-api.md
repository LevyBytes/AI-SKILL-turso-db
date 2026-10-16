# Python filesystem and tool API

[Context index](agentfs-language-sdks.md) · [Setup and preceding context](python-setup-and-key-value-storage.md) · [Previous phase](python-complete-example-and-core-api.md)

### Filesystem

| Method | Description |
| --- | --- |
| `write_file(path, content)` | Write file contents |
| `read_file(path, encoding)` | Read file contents |
| `readdir(path)` | List directory entries |
| `stat(path)` | Get file metadata |
| `delete_file(path)` | Delete a file |
| `mkdir(path)` | Create directory |

### ToolCalls

| Method | Description |
| --- | --- |
| `start(name, params)` | Start tracking a call |
| `success(id, result)` | Mark call as successful |
| `error(id, message)` | Mark call as failed |
| `record(...)` | Record a completed call |
| `get(id)` | Get call by ID |
| `get_by_name(name, limit)` | Query by tool name |
| `get_recent(since)` | Get recent calls |
| `get_stats()` | Get usage statistics |

### Next Steps

### OpenAI Agents

How to use AgentFS together with OpenAI Agents SDK

### LlamaIndex

Using AgentFS with LlamaIndex
