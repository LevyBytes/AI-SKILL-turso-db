# Local development dumps and connections

[Context index](database-products-and-cloud-setup.md) · [Continue](local-database-gui-options.md)

## Using a dump locally

For local Turso development, developers can choose either approach below:
- SQLite — local SQLite database file
- Turso CLI — managed libSQL server
- Turso Database — remote Turso database

### Using a dump locally

 For local development, you can make a dump from production and work against it: 1

Generate the dump with the Turso CLI

```
turso db shell your-database .dump > dump.sql

```

### Step 2: Create SQLite file from dump

```
cat dump.sql | sqlite3 local.db

```

### Step 3: Connect to SQLite file

Use any option below with `local.db`, or pick another file name when starting a fresh database.

### SQLite

 Keep these considerations in mind for SQLite-based local development:
- Lacks some libSQL features
- Compatible with Turso SDKs that are not serverless-based
 When using an [SDK](https://docs.turso.tech/sdk), provide a `file:` URL so the SDK connects to a local SQLite database file rather than a remote Turso database:

```
import { createClient } from "@libsql/client";

const client = createClient({
  url: "file:local.db",
});

```

 During development, `authToken` is not required. For a smoother developer workflow, keep both `url` and `authToken` in environment variables.

### Turso CLI

 When your local work depends on [libSQL](https://docs.turso.tech/libsql) features such as [extensions](https://docs.turso.tech/libsql#extensions), use the Turso CLI:

```
turso dev

```

 That command launches a local libSQL server and creates the database. Then point your SDK at it through the `url` option:

```
import { createClient } from "@libsql/client";

const client = createClient({
  url: "http://127.0.0.1:8080",
});

```

 Stopping the server discards changes. To retain data, or to load a production dump, provide the SQLite file name with `--db-file`:

```
turso dev --db-file local.db

```

### Turso Database

 If a Turso database already exists, you can reuse it for development by passing its connection value through the SDK's `url` option. Remember that a hosted Turso database can create platform charges and consume quota, so SQLite or Turso CLI is usually better for cost-free local work.
