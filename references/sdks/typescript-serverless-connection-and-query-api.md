# TypeScript serverless connection and query API

[Context index](typescript-package-and-api-selection.md)

### @tursodatabase/serverless

 Use this package for any app that connects to a remote Turso Cloud database, including Node.js servers, Docker containers, serverless functions such as AWS Lambda and Vercel Functions, and edge runtimes such as Cloudflare Workers and Deno Deploy. It relies only on `fetch`, has no native dependencies, and runs wherever `fetch` exists.

### Installing

```
npm install @tursodatabase/serverless

```

### Initializing

```
import { connect } from "@tursodatabase/serverless";

const conn = connect({
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

```

 Use the compat module when you need behavior aligned with the `@libsql/client` API:

```
import { createClient } from "@tursodatabase/serverless/compat";

const client = createClient({
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

```

### Querying

```
const stmt = await conn.prepare("SELECT * FROM users");
const rows = await stmt.all();

const stmt2 = await conn.prepare("SELECT * FROM users WHERE id = ?");
const row = await stmt2.get([1]);

```
