# Sveltekit private client and server load

[Context index](typescript-framework-workflows.md)

## Prerequisites

### Prerequisites

 Before continuing, confirm you have the following:
- [Install Turso CLI](https://docs.turso.tech/cli/installation)
- [Create an account or sign in to Turso](https://docs.turso.tech/cli/authentication#signup)
- Have a SvelteKit app - [learn more](https://kit.svelte.dev/docs/creating-a-project)
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
import { TURSO_DATABASE_URL, TURSO_AUTH_TOKEN } from "$env/static/private";
import { createClient } from "@libsql/client";

export const turso = createClient({
  url: TURSO_DATABASE_URL,
  authToken: TURSO_AUTH_TOKEN,
});

```

### Step 4: Execute SQL

```
import { turso } from "$lib/turso.server";

export async function load() {
  const { rows } = await turso.execute("SELECT * FROM table_name");

  return { rows };
}

```

### Examples

### Blog

See the full source code
