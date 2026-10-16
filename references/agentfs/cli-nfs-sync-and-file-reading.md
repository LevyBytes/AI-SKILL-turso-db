# CLI NFS sync and file reading

[Context index](installation-and-cli-workflows.md) · [Setup and preceding context](cli-install-init-and-run.md) · [Previous phase](cli-mount-and-mcp-serving.md) · [Continue](cli-writing-diffs-and-timeline.md)

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
mount -t nfs -o vers=3,tcp,port=2049,mountport=2049,nolock localhost:/ /mnt

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
