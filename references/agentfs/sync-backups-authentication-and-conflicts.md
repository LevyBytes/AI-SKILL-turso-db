# Sync backups authentication and conflicts

[Context index](filesystem-and-session-workflows.md) · [Setup and preceding context](cloud-sync-setup-and-commands.md)

### Backup and Recovery

 Automatically back up agent state:

```
# After important work
agentfs sync my-agent push

# On a new machine
agentfs init my-agent --sync-remote-url libsql://...
agentfs sync my-agent pull

```

### Multi-Machine Agents

 Run agents on several machines while sharing state:

```
# Machine A
agentfs run --session shared-agent python3 agent.py
agentfs sync shared-agent push

# Machine B
agentfs sync shared-agent pull
agentfs run --session shared-agent python3 agent.py

```

### Partial Sync

 For large filesystems, partial sync lets you fetch only the required data:

```
agentfs init my-agent \
  --sync-remote-url libsql://... \
  --sync-partial-prefetch \
  --sync-partial-segment-size 1000

```

### Partial Sync Options

| Option | Description |
| --- | --- |
| `--sync-partial-prefetch` | Enable prefetching |
| `--sync-partial-segment-size` | Segment size for partial sync |
| `--sync-partial-bootstrap-query` | Custom bootstrap query |
| `--sync-partial-bootstrap-length` | Bootstrap prefix length |

### Authentication

 Sync needs a Turso auth token. Provide it with an environment variable:

```
export TURSO_AUTH_TOKEN=$(turso db tokens create my-agent-db)

```

 Alternatively, generate a long-lived token:

```
turso db tokens create my-agent-db --expiration none

```

### Conflict Resolution

 AgentFS resolves conflicts with last-write-wins. If multiple machines edit the same file:
1. Push from Machine A succeeds
2. Machine B cannot push because the operation conflicts
3. Machine B needs to pull first, then push

### Next Steps

### Turso Cloud

Learn more about Turso Cloud

### NFS Server

Remote access via NFS
