# Astro database client and server queries

[Context index](typescript-framework-workflows.md)

## Prerequisites

### Prerequisites

 For the best use of this guide, have these pieces ready:
- [Install Turso CLI](https://docs.turso.tech/cli/installation)
- [Create an account or sign in to Turso](https://docs.turso.tech/cli/authentication#signup)
- Have an Astro project - [learn more](https://docs.astro.build/en/install/auto/#1-run-the-setup-wizard)
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

### Step 3: Configure libSQL client

src/turso.ts

```
import { createClient } from "@libsql/client/web";

export const turso = createClient({
  url: import.meta.env.TURSO_DATABASE_URL!,
  authToken: import.meta.env.TURSO_AUTH_TOKEN,
});

```

Astro is preparing a new ENV API. [Take a look](https://docs.astro.build/en/reference/configuration-reference/#experimentalenv).

### Step 4: Execute SQL

```astro
---
import { turso } from './turso'

const { rows } = await turso.execute('SELECT * FROM table_name')
---

```

### Examples

### Blog

See the full source code
