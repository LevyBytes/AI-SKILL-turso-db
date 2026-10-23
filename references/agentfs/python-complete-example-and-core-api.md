# Python complete example and core API

[Context index](agentfs-language-sdks.md) · [Setup and preceding context](python-setup-and-key-value-storage.md) · [Previous phase](python-tool-call-tracking-and-statistics.md) · [Continue](python-filesystem-and-tool-api.md)

### Complete Example

The complete program is in the [upstream source](https://raw.githubusercontent.com/tursodatabase/turso-docs/fb78fbb00393f0a9eeab1f302a2bfa48c1725384/agentfs/sdk/python.mdx).

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
