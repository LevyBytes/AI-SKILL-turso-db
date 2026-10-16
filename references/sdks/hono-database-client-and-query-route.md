# Hono database client and query route

[Context index](typescript-framework-workflows.md)

## Prerequisites

### Prerequisites

 Before continuing, confirm you have the following:
- [Install Turso CLI](https://docs.turso.tech/cli/installation)
- [Create an account or sign in to Turso](https://docs.turso.tech/cli/authentication#signup)
- Have a Hono app - [learn more](https://hono.dev/top#quick-start)
### Step 1: Install the libSQL SDK

Start by adding `@libsql/client` as a dependency in your project:

```
npm install @libsql/client

```

### Step 2: Retrieve database credentials

Get the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the credentials into environment variables in `.env`.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

### Step 3: Configure libSQL client

```
import { createClient } from "@libsql/client";

export const turso = createClient({
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

```

### Step 4: Execute SQL

```
import { Hono } from "hono";
import { turso } from "./lib/turso";

const app = new Hono();

app.get("/items", async (c) => {
  const { rows } = await turso.execute("SELECT * FROM items");

  return c.json({ rows });
});

```

### Examples

### Expenses tracker app with Hono & Turso

See the full source code
