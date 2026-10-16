# TypeScript provisioning and reseller workflows

[Context index](group-api-and-sdk-provisioning.md) · [Continue](platform-api-resource-navigation.md)

## Quickstart with the TypeScript SDK

 For JavaScript or TypeScript, the quickest path to the Platform API is the official [`@tursodatabase/api`](https://www.npmjs.com/package/@tursodatabase/api) client:

```
npm install @tursodatabase/api

```

```
import { createClient } from "@tursodatabase/api";

const turso = createClient({
  org: process.env.TURSO_ORG!,
  token: process.env.TURSO_PLATFORM_TOKEN!,
});

// Create a database
const db = await turso.databases.create("user-abc123", { group: "default" });

// Generate a scoped auth token for that database
const { jwt } = await turso.databases.createToken("user-abc123");

// Delete it when no longer needed
await turso.databases.delete("user-abc123");

```

 See the resource endpoints below for the raw HTTP reference.

### Common use cases

### Database per user / database per agent (multi-tenant)

 Create a separate database for every user or AI agent. Each entity has an isolated SQLite database that you create through the API, token-scope, and later tear down.

```
import { createClient } from "@tursodatabase/api";

const turso = createClient({
  org: process.env.TURSO_ORG!,
  token: process.env.TURSO_PLATFORM_TOKEN!,
});

// On user signup or agent creation — provision a database
const db = await turso.databases.create(`agent-${agentId}`, { group: "default" });

// Generate a scoped token for that database
const { jwt } = await turso.databases.createToken(`agent-${agentId}`);

// Store db.hostname and jwt for the session

```

 After creating the database, connect from your application:
- **Over the wire** - run remote queries with [`@tursodatabase/serverless`](https://docs.turso.tech/sdk/ts/quickstart)
- **Local-first with sync** - synchronize between the database and a local file with [`@tursodatabase/sync`](https://docs.turso.tech/sync/usage)

### Platform / reseller

 Manage and create databases for your end users. The Platform API lets you build a database-as-a-service offering on Turso.

```
// List all databases in your organization
const databases = await turso.databases.list();

// Get details for a specific database
const db = await turso.databases.get("customer-db");

```

### API Quickstart

A step-by-step walkthrough for creating your first database through the API.

### API Resources

 Begin connecting your platform to the Turso API with a few basic steps.
