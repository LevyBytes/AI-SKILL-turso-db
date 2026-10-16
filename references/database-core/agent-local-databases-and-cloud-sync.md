# Agent local databases and cloud sync

[Context index](agent-memory-and-code-indexing.md) · [Continue](isolated-shared-and-coordinator-databases.md)

## Embedded Databases (Local-First)

Need a full agent state management layer? See [AgentFS](https://docs.turso.tech/agentfs/introduction), Turso's specialized SDK for AI agents that combines filesystem operations, key-value storage, and automatic tool call tracking in one database file. AI agents use databases to keep context, record state, and carry memory between runs. Turso Database supports two main approaches:
- **Embedded Databases** when agent processing should stay local-first, and
- **Turso Sync** when distributed agents need coordination plus cloud persistence.

### Embedded Databases (Local-First)

 Choose embedded databases for agents that stay within one local workflow, need offline execution, or handle temporary private state. They fit local processing pipelines, sensitive work that must remain on the device, dev/test runs, and analysis performed by one agent.

```
import { connect } from "@tursodatabase/database";

// Create a local embedded database for this agent
const db = await connect("agent-workflow.db");

// Agent can now store and query data locally
const stmt = await db.prepare(`
  CREATE TABLE IF NOT EXISTS steps (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    action TEXT NOT NULL,
    result TEXT,
    timestamp DATETIME DEFAULT CURRENT_TIMESTAMP
  )
`);
await stmt.run();

// Track agent workflow steps
await (await db.prepare("INSERT INTO steps (action, result) VALUES (?, ?)"))
  .run(
  "fetch_data",
  "success",
);

```

 This setup gives zero network latency, offline operation, full data privacy, and straightforward single-file deployment. The stored state belongs to that one agent instance. If you need agents to share it or preserve it across sessions, you must handle the database file lifecycle yourself.

### Turso Sync (Cloud-Connected)

 Choose Turso Sync when agents need memory that survives sessions, coordination with other agents, or cloud-backed durability for long-running workflows. Useful cases include conversational agents with session memory, multi-agent coordination systems, recoverable long-running workflows, and agents that report into central systems.

```
import { connect } from "@tursodatabase/sync";

const db = await connect({
  path: "agent-memory.db",
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});
await db.pull();
const history = await db.prepare(
  "SELECT * FROM conversation_history WHERE session_id = ?"
);
const previousContext = await history.all("session-123");
const actions = await db.prepare(
  "INSERT INTO agent_actions (agent_id, action) VALUES (?, ?)"
);
await actions.run("agent-1", "processed_document");
await db.push();

```

 It provides persistent local memory and cloud synchronization. Call `push()` to upload local writes and `pull()` to receive remote changes; schedule those calls when background synchronization is wanted. There is no `sync: "full"` mode option in this connection contract. The example expects `conversation_history(session_id, ...)` and `agent_actions(agent_id, action)` to exist in the remote database before bootstrap.

### Multi-Agent Architectures
