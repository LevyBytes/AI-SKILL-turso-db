# TypeScript libSQL client quickstart

[Context index](typescript-orm-and-client-quickstarts.md)

### @libsql/client

 Choose `@libsql/client` for ORM integration, including Drizzle and Prisma, or for an existing codebase that already uses it. The complete documentation is in the [reference](https://docs.turso.tech/sdk/ts/reference#libsqlclient).

### Step 1: Install

First, add the `@libsql/client` dependency to your project:

```
npm install @libsql/client

```

### Step 2: Connect

```
import { createClient } from "@libsql/client";

export const turso = createClient({
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

```

### Step 3: Execute

```
await turso.execute("SELECT * FROM users");

```

### Step 4: Sync

For syncing a local database with a remote Turso Cloud database, including local reads and writes plus cloud push/pull, use [Turso Sync](https://docs.turso.tech/sync/usage). Built on the Turso Database engine, Turso Sync supports local-first synchronization through explicit `push()` and `pull()` operations.
