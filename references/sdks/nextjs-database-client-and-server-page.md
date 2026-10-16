# Nextjs database client and server page

[Context index](typescript-framework-workflows.md)

## Prerequisites

### Prerequisites

 Before continuing, confirm you have the following:
- [Install Turso CLI](https://docs.turso.tech/cli/installation)
- [Create an account or sign in to Turso](https://docs.turso.tech/cli/authentication#signup)
- Have a Next.js app - [learn more](https://nextjs.org/docs/getting-started/installation)
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

```
import { createClient } from "@libsql/client";

export const turso = createClient({
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

```

### Step 4: Execute SQL

```tsx
import { turso } from "@/lib/turso";

export default async function Page() {
  const { rows } = await turso.execute("SELECT * FROM table_name");

  return (
    <ul>
      {rows.map((row) => (
        <li key={row.id}>{row.id}</li>
      ))}
    </ul>
  );
}

```

### Examples

### Full Stack App

Create an app using Next.js, Turso, and Drizzle ORM.
