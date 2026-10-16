# CLI writing diffs and timeline

[Context index](installation-and-cli-workflows.md) · [Setup and preceding context](cli-install-init-and-run.md) · [Previous phase](cli-nfs-sync-and-file-reading.md) · [Continue](cli-storage-paths-and-setup.md)

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
