# Drizzle configuration migrations and queries

[Context index](typescript-orm-and-client-quickstarts.md) · [Setup and preceding context](drizzle-installation-and-schema.md) · [Continue](drizzle-vector-type-storage-and-index.md)

### Step 4: Configure Drizzle Kit

At your project root, add `drizzle.config.ts` containing the following:drizzle.config.ts

```
import "dotenv/config";

import type { Config } from "drizzle-kit";

export default {
  schema: "./db/schema.ts",
  out: "./migrations",
  dialect: "turso",
  dbCredentials: {
    url: process.env.TURSO_DATABASE_URL!,
    authToken: process.env.TURSO_AUTH_TOKEN,
  },
} satisfies Config;

```

The example above uses `dotenv`; when working with Next.js, Remix, Astro, or Vite, their native environment variable handling can supply these values instead.

### Step 5: Connect Drizzle with libSQL

Save this server-side connection module as `db.ts` in the project root.

```
import { drizzle } from "drizzle-orm/libsql";
import { createClient } from "@libsql/client";

const turso = createClient({
  url: process.env.TURSO_DATABASE_URL!,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

export const db = drizzle(turso);

```

### Step 6: Database migrations

Database migrations can be produced and run by Drizzle through `drizzle-kit`. After each schema update, execute `db:generate`:

```
npm run db:generate

```

Next, use `db:migrate` to apply the generated updates to the database:

```
npm run db:migrate

```

### Step 7: Query

```
import { db } from "./db";
import { fooTable } from "./db/schema";

const result = await db.select().from(fooTable).all();

```

### Step 8: Connect Drizzle Studio

```
npm run db:studio

```
