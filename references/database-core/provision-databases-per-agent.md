# Provision databases per agent

[Context index](agent-memory-and-code-indexing.md) · [Setup and preceding context](agent-local-databases-and-cloud-sync.md) · [Previous phase](isolated-shared-and-coordinator-databases.md)

### Database-Per-Agent Pattern

 Systems that operate many agents can provision one database per agent with the [Platform API](https://docs.turso.tech/api-reference):

```
import { createClient } from "@tursodatabase/api";

const turso = createClient({
  token: process.env.TURSO_PLATFORM_API_TOKEN,
  org: process.env.TURSO_ORG_NAME,
});

// Create a dedicated database for each agent
async function provisionAgentDatabase(agentId) {
  const database = await turso.databases.create(`agent-${agentId}`, {
    group: "default",
  });

  console.log(`Database created for agent ${agentId}: ${database.Name}`);
  return database;
}

```

 This gives agents full isolation, lets each agent scale independently, improves security because a compromised agent affects only one database, and makes cleanup straightforward when agents are retired.

### Examples

 These examples demonstrate the agent database patterns:
- [sync-node](https://github.com/tursodatabase/turso/blob/main/examples/javascript/sync-node) - Node.js using bidirectional sync with Turso Cloud
- [sync-wasm-vite](https://github.com/tursodatabase/turso/blob/main/examples/javascript/sync-wasm-vite) - Browser (WASM) using bidirectional sync with Turso Cloud
- [database-node](https://github.com/tursodatabase/turso/blob/main/examples/javascript/database-node) - Node.js with a local file database and no sync
- [database-wasm-vite](https://github.com/tursodatabase/turso/blob/main/examples/javascript/database-wasm-vite) - Browser (WASM) with a local database stored in the browser
