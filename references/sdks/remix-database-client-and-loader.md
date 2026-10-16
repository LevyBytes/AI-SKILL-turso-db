# Remix database client and loader

[Context index](typescript-framework-workflows.md)

## Prerequisites

### Prerequisites

 Before continuing, confirm you have the following:
- [Install Turso CLI](https://docs.turso.tech/cli/installation)
- [Create an account or sign in to Turso](https://docs.turso.tech/cli/authentication#signup)
- Have a Remix app - [learn more](https://remix.run/docs/en/main/start/quickstart#quick-start)
### Step 1: Install the libSQL SDK

Start by adding `@libsql/client` as a dependency in your project:

```
npm install @libsql/client

```

### Step 2: Configure database credentials

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

### Step 3: Configure libSQL Client.

```
import { createClient } from "@libsql/client";

export const turso = createClient({
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

```

### Step 4: Execute SQL

app/routes/_index.ts

```
import type { LoaderFunction } from "@remix-run/node";

import { turso } from "~/lib/turso";

export const loader: LoaderFunction = async () => {
  const { rows } = await turso.execute("SELECT * from TABLE_NAME");

  return {
    items: rows,
  };
};

```

### Examples

### E-commerce Store

See the full source code

### CRM App

See the full source code
