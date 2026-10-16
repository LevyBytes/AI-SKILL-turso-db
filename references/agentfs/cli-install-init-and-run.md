# CLI install init and run

[Context index](installation-and-cli-workflows.md) · [Continue](cli-mount-and-mcp-serving.md)

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
