# Isolated shared and coordinator databases

[Context index](agent-memory-and-code-indexing.md) · [Setup and preceding context](agent-local-databases-and-cloud-sync.md) · [Continue](provision-databases-per-agent.md)

### Pattern 1: Isolated Agent Databases

 Give each agent its own embedded database when isolation matters. This works best for independent agents that do not share state.

```
import { connect as connectLocal } from "@tursodatabase/database";

// Agent 1
const agent1DB = await connectLocal("agent-1.db");

// Agent 2
const agent2DB = await connectLocal("agent-2.db");

```

 Independent agents that run separate work, need no coordination, and require the strongest isolation.

### Pattern 2: Shared Database with Sync

 Have several agents connect to one synced database. This pattern fits agent systems that coordinate around shared data.

```
import { connect } from "@tursodatabase/sync";

// Provision the shared tasks(status, ...) table before starting agents.
const remote = {
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
};
const agent1DB = await connect({ path: "agent-1-local.db", ...remote });
const insert = await agent1DB.prepare("INSERT INTO tasks (status) VALUES (?)");
await insert.run("pending");
await agent1DB.push();

const agent2DB = await connect({ path: "agent-2-local.db", ...remote });
await agent2DB.pull();
const select = await agent2DB.prepare("SELECT * FROM tasks WHERE status = ?");
const pendingTasks = await select.all("pending");

```

 Agents that coordinate through shared task queues or a shared knowledge base.

### Pattern 3: Hub-and-Spoke

 Use a central synced database where agents submit results and retrieve new tasks.

```
import { connect } from "@tursodatabase/sync";

const workerDB = await connect({
  path: "worker-local.db",
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.WORKER_TOKEN,
});
// After the worker writes its results locally:
await workerDB.push();

const coordinatorDB = await connect({
  path: "coordinator-local.db",
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.COORDINATOR_TOKEN,
});
await coordinatorDB.pull();
// The coordinator can now query received results locally.

```

 Many worker agents send results to a central coordinator for monitoring or aggregation when data should mainly flow in one direction.
