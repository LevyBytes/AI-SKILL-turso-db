# Cloud sync setup and commands

[Context index](filesystem-and-session-workflows.md) · [Continue](sync-backups-authentication-and-conflicts.md)

## Setting Up Sync

AgentFS can synchronize with [Turso Cloud](https://turso.tech), supporting backup, collaboration, and distributed agent deployments.

### Setting Up Sync

### 1. Create a Turso Database

 If you do not already have a Turso account, [sign up](https://turso.tech) first. Then create a database for the agent:

```
turso db create my-agent-db

```

 Get the database URL:

```
turso db show my-agent-db --url
# Output: libsql://my-agent-db-username.turso.io

```

### 2. Initialize with Sync

 Create a new synchronized agent filesystem:

```
agentfs init my-agent --sync-remote-url libsql://my-agent-db-username.turso.io

```

 Or enable sync for an agent that already exists:

```
# Edit the database to add sync configuration
# (Feature coming soon)

```

### Sync Commands

### Pull Remote Changes

 Download changes from Turso Cloud:

```
agentfs sync my-agent pull

```

### Push Local Changes

 Send local changes up to Turso Cloud:

```
agentfs sync my-agent push

```

### View Sync Status

 Check sync statistics:

```
agentfs sync my-agent stats

```

### Create Checkpoint

 Force a sync checkpoint:

```
agentfs sync my-agent checkpoint

```

### Use Cases
