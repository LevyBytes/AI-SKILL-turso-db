# TypeScript serverless client quickstart

[Context index](typescript-orm-and-client-quickstarts.md)

### Recommended: @tursodatabase/serverless (Remote / Over-the-Wire)

When an application talks to Turso Cloud directly over the network, use `@tursodatabase/serverless` for Node.js servers, Docker containers, serverless functions, and edge runtimes such as Cloudflare Workers, Vercel Edge, and Deno Deploy. It depends only on `fetch`, has zero native dependencies, and runs broadly.

### Step 1: Retrieve database credentials

Continuing requires an existing database. If none exists yet, [create one](https://docs.turso.tech/quickstart).Then retrieve the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the required credentials into the `.env` environment variables.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

These values should be stored as environment variables.

### Step 2: Install

```
npm install @tursodatabase/serverless

```

### Step 3: Connect to your database

```
import { connect } from "@tursodatabase/serverless";

const conn = connect({
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

```

Use the compat module when you need compatibility with the `@libsql/client` API:

```
import { createClient } from "@tursodatabase/serverless/compat";

const client = createClient({
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

```

### Step 4: Execute a query using SQL

```
const stmt = await conn.prepare("SELECT * FROM users");
const rows = await stmt.all();

```

Placeholders can be used when values need to be bound later:

```
const stmt = await conn.prepare("SELECT * FROM users WHERE id = ?");
const row = await stmt.get([1]);

```
