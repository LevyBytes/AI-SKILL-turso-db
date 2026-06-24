# Introduction, Reference

> Agentfs reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: Key Features; Installation.

## Key Features

AgentFS gives AI agents a filesystem layer. Its copy-on-write model lets an agent change files in isolation while the source data remains unchanged. You can use it as a CLI that launches an existing program inside a sandboxed session, or as an SDK for building custom agents around a filesystem abstraction.

### Key Features

- **Copy-on-Write Isolation** - Run agents inside sandboxed environments so edits stay separate from your source tree
- **Single File Storage** - Store the full state in one portable SQLite database for simpler sharing and snapshot creation
- **Built-in Auditing** - Record every file operation so it can be queried later
- **Cloud Sync** - Sync agent state to Turso Cloud when that option is enabled

### Quick Start

```
# Install
curl -fsSL https://agentfs.ai/install | bash

# Run a shell in an isolated environment
cd /path/to/project
agentfs run bash

# Any changes are isolated - your original files are safe

```

### Next Steps

### Installation

Install the AgentFS CLI

### Agentic Coding

Run AI coding agents safely

### AgentFS SDKs

Use the AgentFS SDKs to build custom agents. This software is in BETA and may still have bugs or behave unexpectedly, so treat production data carefully and keep backups available.

## Installation

Reference documentation for the `agentfs` command-line tool. For the newest version, see [MANUAL.md](https://github.com/tursodatabase/agentfs/blob/main/MANUAL.md) in the repository.

### Installation

```
curl -fsSL https://github.com/tursodatabase/agentfs/releases/latest/download/agentfs-installer.sh | sh

```

### Commands

### agentfs init

 Initialize a new agent filesystem.

```
agentfs init [OPTIONS] [ID]

```

 **Arguments:**
- `ID` - Agent identifier (default: `agent-{timestamp}`)
 **Options:**
| Option | Description |
| --- | --- |
| `--force` | Overwrite existing agent filesystem |
| `--base <PATH>` | Base directory for overlay filesystem |
| `--sync-remote-url <URL>` | Remote Turso database URL |

 **Example:**

```
agentfs init my-agent
agentfs init my-overlay --base /path/to/project

```

---

### agentfs run

 Run a program inside a sandboxed environment.

```
agentfs run [OPTIONS] <COMMAND> [ARGS]...

```

 **Options:**
| Option | Description |
| --- | --- |
| `--session <ID>` | Named session for persistence |
| `--allow <PATH>` | Allow write access to directory |
| `--no-default-allows` | Disable default allowed directories |
| `--experimental-sandbox` | Use ptrace sandbox (Linux) |
| `--strace` | Show intercepted syscalls |

 **Examples:**

```
agentfs run /bin/bash
agentfs run --session my-project python3 agent.py
agentfs run --allow /tmp --allow ~/.cache /bin/bash

```

---

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

### agentfs serve nfs

 Start an NFS server.

```
agentfs serve nfs <ID_OR_PATH> [OPTIONS]

```

 **Options:**
| Option | Description |
| --- | --- |
| `--bind <IP>` | IP to bind (default: `127.0.0.1`) |
| `--port <PORT>` | Port (default: `11111`) |

 **Example:**

```
agentfs serve nfs my-agent --bind 0.0.0.0 --port 2049

```

 **Mounting:**

```
mount -t nfs -o vers=3,tcp,port=11111,mountport=11111,nolock localhost:/ /mnt

```

---

### agentfs sync

 Sync data with a remote Turso database.

```
agentfs sync <ID_OR_PATH> <SUBCOMMAND>

```

 **Subcommands:**
| Command | Description |
| --- | --- |
| `pull` | Pull remote changes |
| `push` | Push local changes |
| `stats` | View sync statistics |
| `checkpoint` | Create checkpoint |

 **Example:**

```
agentfs sync my-agent pull
agentfs sync my-agent push

```

---

### agentfs fs

 Filesystem operations on agent databases.

#### agentfs fs ls

```
agentfs fs ls <ID_OR_PATH> [FS_PATH]

```

 List files. Output uses `f <name>` for files and `d <name>` for directories.

#### agentfs fs cat

```
agentfs fs cat <ID_OR_PATH> <FILE_PATH>

```

 Display file contents.

#### agentfs fs write

```
agentfs fs write <ID_OR_PATH> <FILE_PATH> <CONTENT>

```

 Write data into a file. **Examples:**

```
agentfs fs ls my-agent
agentfs fs cat my-agent /config.json
agentfs fs write my-agent /hello.txt "Hello, world!"

```

---

### agentfs diff

 Display filesystem changes when overlay mode is used.

```
agentfs diff <ID_OR_PATH>

```

---

### agentfs timeline

 Display agent action timeline.

```
agentfs timeline [OPTIONS] <ID_OR_PATH>

```

 **Options:**
| Option | Description |
| --- | --- |
| `--limit <N>` | Limit entries (default: 100) |
| `--filter <TOOL>` | Filter by tool name |
| `--status <STATUS>` | Filter: `pending`, `success`, `error` |
| `--format <FORMAT>` | Output: `table`, `json` |

 **Examples:**

```
agentfs timeline my-agent
agentfs timeline my-agent --filter web_search --status error
agentfs timeline my-agent --format json

```

---

### agentfs completions

 Manage shell completions.

```
agentfs completions install [SHELL]
agentfs completions uninstall [SHELL]
agentfs completions show

```

 Supported: `bash`, `zsh`, `fish`, `powershell`

### Environment Variables

 Variables set inside the sandbox:
| Variable | Description |
| --- | --- |
| `AGENTFS` | Set to `1` inside sandbox |
| `AGENTFS_SANDBOX` | Sandbox type |
| `AGENTFS_SESSION` | Session ID |

### Files

| Path | Description |
| --- | --- |
| `.agentfs/<ID>.db` | Agent filesystem database |
| `~/.config/agentfs/` | Configuration directory |

### See Also

### Installation

Installation instructions

### Sandbox Guide

Running agents in sandboxes
