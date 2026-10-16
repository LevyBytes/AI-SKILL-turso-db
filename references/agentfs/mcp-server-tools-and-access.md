# MCP server tools and access

[Context index](filesystem-and-session-workflows.md)

## Starting the MCP Server

AgentFS can operate as an [MCP (Model Context Protocol)](https://modelcontextprotocol.io/) server, making filesystem and key-value operations available to AI assistants such as Claude Desktop.

### Starting the MCP Server

```
agentfs serve mcp my-agent

```

 This launches an MCP server and publishes AgentFS tools through stdio.

### Available Tools

### Filesystem Tools

| Tool | Description |
| --- | --- |
| `read_file` | Read file contents |
| `write_file` | Write file contents |
| `readdir` | List directory contents |
| `mkdir` | Create directory |
| `remove` | Remove file or directory |
| `rename` | Rename file or directory |
| `stat` | Get file metadata |
| `access` | Check file access permissions |

### Key-Value Tools

| Tool | Description |
| --- | --- |
| `kv_get` | Get value by key |
| `kv_set` | Set key-value pair |
| `kv_delete` | Delete key |
| `kv_list` | List all keys |

### Limiting Exposed Tools

 For a tighter security boundary, restrict the tools that are exposed:

```
# Read-only mode
agentfs serve mcp my-agent --tools read_file,readdir,stat,kv_get,kv_list

# Filesystem only
agentfs serve mcp my-agent --tools read_file,write_file,readdir,mkdir,remove

# Key-value only
agentfs serve mcp my-agent --tools kv_get,kv_set,kv_delete,kv_list

```

### Security Considerations

 The MCP server grants AI assistants direct access to the AgentFS filesystem. Apply these security practices:
- **Restrict tools** - Expose only the tools the assistant actually requires
- **Use overlays** - Prefer overlay filesystems over direct access to real directories
- **Inspect the agent database** - Review `.agentfs/*.db` on a regular basis

### Debugging

 Verify that the MCP server is responding:

```
# Run manually to see output
agentfs serve mcp my-agent

# Check if the database exists
ls -la .agentfs/my-agent.db

```

### Next Steps

### NFS Server

Remote access via NFS

### Copy-on-Write Overlays

How isolation works
