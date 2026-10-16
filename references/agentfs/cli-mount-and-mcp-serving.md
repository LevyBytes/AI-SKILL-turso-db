# CLI mount and MCP serving

[Context index](installation-and-cli-workflows.md) · [Setup and preceding context](cli-install-init-and-run.md) · [Continue](cli-nfs-sync-and-file-reading.md)

### agentfs mount

 Mount an agent filesystem, or display the current mounts.

```
agentfs mount [OPTIONS] [ID_OR_PATH] [MOUNT_POINT]

```

 With no arguments, it lists every mounted filesystem. **Options:**

| Option | Description |
| --- | --- |
| `-a, --auto-unmount` | Automatically unmount on exit |
| `--allow-root` | Allow root user access |
| `-f, --foreground` | Run in foreground |
| `--uid <UID>` | User ID for files |
| `--gid <GID>` | Group ID for files |

 **Examples:**

```
agentfs mount                              # List mounts
agentfs mount my-agent ./mnt               # Mount agent
agentfs mount my-agent ./mnt -a -f         # Foreground with auto-unmount

```

 **Unmounting:**
- Linux: `fusermount -u <MOUNT_POINT>`
- macOS: `umount <MOUNT_POINT>`

---

### agentfs serve mcp

 Launch an MCP (Model Context Protocol) server.

```
agentfs serve mcp <ID_OR_PATH> [OPTIONS]

```

 **Options:**

| Option | Description |
| --- | --- |
| `--tools <TOOLS>` | Comma-separated list of tools |

 **Available Tools:**

| Category | Tools |
| --- | --- |
| Filesystem | `read_file`, `write_file`, `readdir`, `mkdir`, `remove`, `rename`, `stat`, `access` |
| Key-Value | `kv_get`, `kv_set`, `kv_delete`, `kv_list` |

 **Examples:**

```
agentfs serve mcp my-agent
agentfs serve mcp my-agent --tools read_file,readdir,stat

```

---
