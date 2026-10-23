# Local dumps and SQLite connections

[Context index](database-and-group-commands.md) · [Setup and preceding context](api-token-list-and-scopes.md) · [Previous phase](database-token-creation-and-invalidation.md) · [Continue](local-libsql-server-and-database-guis.md)

## Using a dump locally

For local Turso development, choose one of these approaches:
- SQLite: local SQLite database file
- Turso CLI: managed libSQL server
- Turso Database: remote Turso database

### Using a dump locally

 You can dump your production database whenever needed and use that dump for local development:

### Step 1: Generate a dump with the Turso CLI.

```
turso db shell your-database .dump > dump.sql

```
### Step 2: Create SQLite file from dump

```
cat dump.sql | sqlite3 local.db

```
### Step 3: Connect to SQLite file

Use any approach below with `local.db`, or choose a different filename when starting from a fresh database.

### SQLite

 Keep these points in mind when using SQLite for local development:
- Lacks some libSQL features.
- Compatible with non-serverless based Turso SDKs.
 When using an [SDK](https://docs.turso.tech/sdk), provide a `file:` URL so it connects to a SQLite database file rather than a remote Turso database:

```
import { createClient } from "@libsql/client";

const client = createClient({
  url: "file:local.db",
});

```

 In development, `authToken` is not required. For smoother local setup, put both `url` and `authToken` in environment variables.
