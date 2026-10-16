# Drizzle installation and schema

[Context index](typescript-orm-and-client-quickstarts.md) · [Continue](drizzle-configuration-migrations-and-queries.md)

## Prerequisites

(Figure: Drizzle banner) For Turso integration, Drizzle relies on `@libsql/client`. Local and embedded scenarios can also use [beta support for `@tursodatabase/database`](https://orm.drizzle.team/docs/connect-turso-database).

### Prerequisites

 Before continuing, verify that you have completed these steps:
- [Install the Turso CLI](https://docs.turso.tech/cli/installation)
- [Sign up or login to Turso](https://docs.turso.tech/cli/authentication#signup)
### Step 1: Add Drizzle together with the libSQL SDK

```
npm i drizzle-orm @libsql/client dotenv
npm i -D drizzle-kit

```

Then edit `package.json` so it defines these three `scripts`:

```
{
  "scripts": {
    "db:generate": "drizzle-kit generate",
    "db:migrate": "drizzle-kit migrate",
    "db:studio": "drizzle-kit studio"
  }
}

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

Put the required credentials into the `.env` environment variables.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

### Step 3: Create a Drizzle schema

db/schema.ts

```
import { sql } from "drizzle-orm";
import { text, sqliteTable } from "drizzle-orm/sqlite-core";

export const fooTable = sqliteTable("foo", {
  bar: text("bar").notNull().default("Hey!"),
});

```
