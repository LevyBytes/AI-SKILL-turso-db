# Session supervision storage and management

[Context index](filesystem-and-session-workflows.md) · [Setup and preceding context](shared-session-creation-and-resumption.md)

### Agent Supervision

 Launch an agent and watch its progress from a separate terminal:

```
# Terminal 1: Run the agent
agentfs run --session agent-task python3 agent.py

# Terminal 2: Watch what it's doing
agentfs run --session agent-task /bin/bash
$ watch ls -la  # See files being created
$ tail -f agent.log  # Monitor logs

```

### Managing Sessions

 List files in a session:

```
agentfs fs ls my-session

```

 View session changes:

```
agentfs diff my-session

```

 Delete a session:

```
rm .agentfs/my-session.db

```

### Session Storage

 Session data is kept as SQLite databases under `.agentfs/`:

```
.agentfs/
├── my-project.db
├── experiment.db
└── shared-work.db

```

 Each database contains:
- All modified files
- Deleted file markers (whiteouts)
- Tool call audit log
- Key-value store data

### Next Steps

### Auditing

Review the events that occurred in a session

### Overlay Filesystem

How copy-on-write works
