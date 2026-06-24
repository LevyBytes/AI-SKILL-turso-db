# Orm, Quickstart

> Sdks reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: Prerequisites; Recommended: @tursodatabase/database (Local / Embedded).

## Prerequisites

(Figure: Drizzle banner) For Turso integration, Drizzle relies on `@libsql/client`. Local and embedded scenarios can also use [beta support for `@tursodatabase/database`](https://orm.drizzle.team/docs/connect-turso-database).

### Prerequisites

 Before continuing, verify that you have completed these steps:
- [Install the Turso CLI](https://docs.turso.tech/cli/installation)
- [Sign up or login to Turso](https://docs.turso.tech/cli/authentication#signup)
 1

Add Drizzle together with the libSQL SDK

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

2

Retrieve database credentials

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

3

Create a Drizzle schema

db/schema.ts

```
import { sql } from "drizzle-orm";
import { text, sqliteTable } from "drizzle-orm/sqlite-core";

export const fooTable = sqliteTable("foo", {
  bar: text("bar").notNull().default("Hey!"),
});

```

4

Configure Drizzle Kit

At your project root, add `drizzle.config.ts` containing the following:drizzle.config.ts

```
require("dotenv").config();

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

The example above uses `dotenv`; when working with Next.js, Remix, Astro, or Vite, their native environment variable handling can supply these values instead.5

Connect Drizzle with libSQL

```
import { drizzle } from "drizzle-orm/libsql";
import { createClient } from "@libsql/client";

const turso = createClient({
  url: process.env.TURSO_DATABASE_URL!,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

export const db = drizzle(turso);

```

6

Database migrations

Database migrations can be produced and run by Drizzle through `drizzle-kit`. After each schema update, execute `db:generate`:

```
npm run db:generate

```

Next, use `db:migrate` to apply the generated updates to the database:

```
npm run db:migrate

```

7

Query

```
import { db } from "./db";
import { fooTable } from "./schema";

const result = await db.select().from(fooTable).all();

```

8

Connect Drizzle Studio

```
npm run db:studio

```

### Vector Embeddings

 Drizzle can be expanded for Turso native vector support; [learn more](https://docs.turso.tech/features/ai-and-embeddings). 1

Define custom vector type

Add this content inside `db/schema.ts`:

```
import { sql } from "drizzle-orm";
import { customType } from "drizzle-orm/sqlite-core";

const float32Array = customType<{
  data: number[];
  config: { dimensions: number };
  configRequired: true;
  driverData: Buffer;
}>({
  dataType(config) {
    return `F32_BLOB(${config.dimensions})`;
  },
  fromDriver(value: Buffer) {
    return Array.from(new Float32Array(value.buffer));
  },
  toDriver(value: number[]) {
    return sql`vector32(${JSON.stringify(value)})`;
  },
});

```

2

Define a table that includes a vector column

When declaring the schema, call `float32Array` to define the vector-storage column:

```
import { sqliteTable, integer } from "drizzle-orm/sqlite-core";

export const vectorTable = sqliteTable("vector_table", {
  id: integer("id").primaryKey(),
  vector: float32Array("vector", { dimensions: 3 }),
});

```

3

Create a vector index

Raw SQL is required for creating the index:

```
import { drizzle } from "drizzle-orm/libsql";
import { createClient } from "@libsql/client";

const client = createClient({
  url: process.env.TURSO_DATABASE_URL!,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

const db = drizzle(client);

await db.run(sql`
  CREATE INDEX IF NOT EXISTS vector_index
  ON vector_table(vector)
  USING vector_cosine(3)
`);

```

4

Insert vector data

```
await db
  .insert(vectorTable)
  .values([{ vector: sql`vector32(${JSON.stringify([1.1, 2.2, 3.3])})` }]);

```

5

Query vector data

Calculate vector distance:

```
const res = await db
  .select({
    distance: sql<number>`vector_distance_cos(${vectorTable.vector}, vector32(${JSON.stringify([2.2, 3.3, 4.4])}))`,
  })
  .from(vectorTable);

console.log(res);

```

Perform efficient nearest neighbor search:

```
const topK = await db
  .select({
    id: sql`id`,
    distance: sql`distance`,
  })
  .from(
    sql`vector_top_k('vector_index', vector32(${JSON.stringify([2.2, 3.3, 4.4])}), 5)`,
  )
  .leftJoin(vectorTable, sql`${vectorTable}.id = id`);

console.log(topK);

```

 For performant vector work, add indexes that match the query patterns and set dimensions to fit the application.

## Prerequisites

(Figure: Prisma banner)

### Prerequisites

 Before continuing, verify that you have completed these steps:
- [Install the Turso CLI](https://docs.turso.tech/cli/installation)
- [Sign up or login to Turso](https://docs.turso.tech/cli/authentication#signup)
- Prisma versions 5.4.2 and later
 1

Add the libSQL SDK along with its Prisma driver

```
npm install @libsql/client @prisma/adapter-libsql

```

2

Retrieve database credentials

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

3

Turn on the `driverAdapters` preview feature flag:

prisma/schema.prisma

```
generator client {
  provider        = "prisma-client-js"
  previewFeatures = ["driverAdapters"]
}

datasource db {
  provider = "sqlite"
  url      = "file:./dev.db"
}

```

4

Generate Prisma client

```
npx prisma generate

```

5

Update your Prisma Client Instance

```
import { PrismaClient } from "@prisma/client";
import { PrismaLibSQL } from "@prisma/adapter-libsql";

const adapter = new PrismaLibSQL({
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
})
const prisma = new PrismaClient({ adapter })

```

6

Database Migrations

Turso does not currently support Prisma Migrate or Introspection workflows; [learn more](https://www.prisma.io/docs/orm/overview/databases/turso#how-to-manage-schema-changes).Start by creating a migration file with prisma migrate dev against a local SQLite database

```
npx prisma migrate dev --name init

```

After that, use the Turso CLI to apply the migration to your Turso database

```
turso db shell turso-prisma-db < ./prisma/migrations/20230922132717_init/migration.sql

```

Change `20230922132717_init` to the migration name produced by the `npx prisma migrate dev` command.7

Query

```
const response = await prisma.table_name.findMany();

```

## Recommended: @tursodatabase/database (Local / Embedded)

This TypeScript quickstart covers the following tasks:
- Install the Turso package
- Connect to a local or remote database
- Execute a query using SQL
- Sync changes to the cloud

### Recommended: @tursodatabase/database (Local / Embedded)

 For local and embedded deployments such as Node.js, Electron, mobile, or IoT, `@tursodatabase/database` is the preferred package. It runs on the Turso Database engine, a from-scratch SQLite rewrite with concurrent writes (MVCC) and async I/O. 1

Install

```
npm install @tursodatabase/database

```

2

Connect

```
import { connect } from "@tursodatabase/database";

const db = await connect("app.db");

```

3

Execute

```
const createTable = db.prepare(`
  CREATE TABLE IF NOT EXISTS users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    username TEXT NOT NULL
  )
`);
await createTable.run();

const insertUser = db.prepare("INSERT INTO users (username) VALUES (?)");
await insertUser.run("alice");

const stmt = db.prepare("SELECT * FROM users");
const users = await stmt.all();
console.log(users);

```

4

Sync (push and pull)

To synchronize a local database with Turso Cloud, use [`@tursodatabase/sync`](https://docs.turso.tech/sync/usage):

```
npm install @tursodatabase/sync

```

```
import { connect } from "@tursodatabase/sync";

const db = await connect({
  path: "./app.db",
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

await db.exec("INSERT INTO users (username) VALUES ('bob')");

// Push local writes to Turso Cloud
await db.push();

// Pull remote changes to local database
const changed = await db.pull();

```

Reads and writes use the local database file, which makes operation quick and available offline. `push()` uploads local changes, while `pull()` retrieves changes from the remote side. For conflict handling, checkpointing, and related behavior, see [Turso Sync](https://docs.turso.tech/sync/usage).

### Recommended: @tursodatabase/serverless (Remote / Over-the-Wire)

When an application talks to Turso Cloud directly over the network, use `@tursodatabase/serverless` for Node.js servers, Docker containers, serverless functions, and edge runtimes such as Cloudflare Workers, Vercel Edge, and Deno Deploy. It depends only on `fetch`, has zero native dependencies, and runs broadly. 1

Retrieve database credentials

Continuing requires an existing database. If none exists yet, [create one](https://docs.turso.tech/quickstart).Then retrieve the database URL:

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

These values should be stored as environment variables.2

Install

```
npm install @tursodatabase/serverless

```

3

Connect to your database

```
import { connect } from "@tursodatabase/serverless";

const conn = connect({
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

```

Use the compat module when you need compatibility with the `@libsql/client` API:

```
import { createClient } from "@tursodatabase/serverless/compat";

const client = createClient({
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

```

4

Execute a query using SQL

```
const stmt = await conn.prepare("SELECT * FROM users");
const rows = await stmt.all();

```

Placeholders can be used when values need to be bound later:

```
const stmt = await conn.prepare("SELECT * FROM users WHERE id = ?");
const row = await stmt.get([1]);

```

### @libsql/client

 Choose `@libsql/client` for ORM integration, including Drizzle and Prisma, or for an existing codebase that already uses it. The complete documentation is in the [reference](https://docs.turso.tech/sdk/ts/reference#libsqlclient). 1

Install

First, add the `@libsql/client` dependency to your project:

```
npm install @libsql/client

```

2

Connect

```
import { createClient } from "@libsql/client";

export const turso = createClient({
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

```

3

Execute

```
await turso.execute("SELECT * FROM users");

```

4

Sync

For syncing a local database with a remote Turso Cloud database, including local reads and writes plus cloud push/pull, use [Turso Sync](https://docs.turso.tech/sync/usage). Built on the Turso Database engine, Turso Sync supports local-first synchronization through explicit `push()` and `pull()` operations.
