# Qwik database client and route loader

[Context index](typescript-framework-workflows.md)

## Prerequisites

### Prerequisites

 Before continuing, confirm you have the following:
- [Install Turso CLI](https://docs.turso.tech/cli/installation)
- [Create an account or sign in to Turso](https://docs.turso.tech/cli/authentication#signup)
- Have a Qwik app - [learn more](https://qwik.builder.io/docs/getting-started/#create-an-app-using-the-cli)
### Step 1: Add Turso Integration

```
npm run qwik add turso

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

Put the credentials into environment variables in `.env.local`.

```
PRIVATE_TURSO_DATABASE_URL="..."
PRIVATE_TURSO_AUTH_TOKEN="..."

```

### Step 3: Execute SQL

```
import { routeLoader$, type RequestEventBase } from "@builder.io/qwik-city";
import { tursoClient } from "~/utils/turso";

export const useFrameworks = routeLoader$(
  async (requestEvent: RequestEventBase) => {
    const db = tursoClient(requestEvent["env"]);
    const { rows } = await db.execute("select * from table_name");

    return {
      items: rows,
    };
  },
);

```

### Examples

### Social Website

See the full source code

### Shopping Cart

See the full source code
