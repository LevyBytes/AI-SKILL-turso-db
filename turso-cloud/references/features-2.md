# Embedded Replicas, Multi Db Schemas, Point In Time Recovery

> Turso Cloud reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: How it works; Prerequisites.

## How it works

Embedded Replicas maintain a local read replica for a Turso Cloud database. Reads come from the local file in microseconds, while writes go to the cloud primary before being propagated back to the replica. Embedded Replicas are production-supported. For new projects that require sync, Turso recommends [Turso Sync](https://docs.turso.tech/sync/usage). Sync runs on the Turso Database engine, uses logical change-data-capture, supports local-first writes and explicit `push()` / `pull()`, and uses [significantly less bandwidth and lower latency](https://turso.tech/blog/sync-benchmark) than the page-level replication used by Embedded Replicas. With Turso Cloud's embedded replicas, an application can replicate a database inside its own runtime. This helps VM or VPS deployments and mobile apps that cannot rely on stable connectivity, because the local database remains available without interruption. Embedded replicas let the same database move between local and remote operation patterns, and by syncing local copies from the remote database they provide microsecond-level read access.

### How it works

1.  Set a local file as the main database.
  - Configure that file through the client `url` parameter.

2.  Set the remote database that the client should sync with.
  - Use the client configuration parameter `syncUrl` for that remote database.

3.  You read from a database:
  - All reads come from the local replica identified by `url`.

4.  You write to a database:
  - By default, **writes go to the remote primary database** specified by `syncUrl`; they are NOT first persisted to the local file.
  - To write locally, set the `offline` configuration option to `true`.
  - Write transactions that include reads are sent to the remote primary database as well.
  - After a write succeeds, the local database receives those changes automatically; read your own writes is enabled unless disabled.

### Periodic sync

 Automatic syncing to an embedded replica is available through the periodic sync interval property. Provide `syncInterval` when creating the client:

```
import { createClient } from "@libsql/client";

const client = createClient({
  url: "file:path/to/db-file.db",
  authToken: "...",
  syncUrl: "...",
  syncInterval: 60,
  offline: true, // Optional: Enable offline mode (default: false)
});

```

### Read your writes

 Embedded Replicas also provide read-your-writes semantics. After a successful write, the replica that made that write can immediately read the new data even without calling `sync()`. Other replicas receive the data when they call `sync()`, or on the next interval when Periodic Sync is enabled. (Figure: Read your writes)

### Encryption at rest

 Encryption at rest is supported for Embedded Replicas when using one of the libSQL client SDKs. Supply `encryptionKey` as you instantiate the client:

```
import { createClient } from "@libsql/client";

const db = createClient({
  url: "file:encrypted.db",
  encryptionKey: process.env.ENCRYPTION_KEY,
});

```

 You are responsible for generating and managing the encryption key.

### Usage

 To use embedded replicas, create a client that includes `syncUrl`. That parameter gives the remote Turso Cloud database URL the client will sync against:

```
import { createClient } from "@libsql/client";

const client = createClient({
  url: "file:replica.db",
  syncUrl: "libsql://...",
  authToken: "...",
});

```

 Remote changes can be synced to the local replica manually:

```
await client.sync();

```

 Call `.sync()` in the background at the points where the application should align the remote database and local embedded replica. Common examples are every 5 minutes or whenever the application starts.

### Things to know

- While an embedded replica is syncing, do not open the local database, because that can corrupt data.
- Embedded replicas cannot be used in some contexts, including serverless environments that do not provide a filesystem.
- Some scenarios can cause more frames to sync than expected.
  - If a write makes the internal btree split at any node, many new frames would be added to the replication log.
  - If a server restart leaves the on-disk wal dirty, the replication log would be regenerated and extra frames would sync.
  - Deleting or invalidating the on-disk local files could force the embedded replica to sync again from the beginning.

- A frame is 4kB of data, meaning one on disk page frame; writing a 1 byte row still appears as a 4kB write because libsql writes in that unit.

### Deployment Guides

### Turso + Fly

Deploy a JavaScript project that uses Embedded Replicas on Fly.io

### Turso + Koyeb

Deploy a JavaScript/Rust project using Embedded Replicas on Koyeb

### Turso + Railway

Deploy a JavaScript/Rust project using Embedded Replicas on Railway

### Turso + Render

Deploy a JavaScript project using Embedded Replicas on Render

### Turso + Linode by Akamai

Deploy a JavaScript/Rust project using Embedded Replicas on Akamai

## Prerequisites

(Figure: Akamai banner)

### Prerequisites

 Before starting, confirm that you:
- Have the [Turso CLI](https://docs.turso.tech/cli/installation) installed
- Have [signed up or logged in to Turso Cloud](https://docs.turso.tech/cli/authentication#signup)
- Have an Akamai account; [create one](https://login.linode.com/signup) if needed
 1

Retrieve database credentials

An existing database is required to continue. If one is not available, [create one](https://docs.turso.tech/quickstart). Then get the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the credentials into the environment variables in `.env`.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

Keep these credentials as environment variables.2Fork one of the GitHub embedded replica projects listed below

### My Expenses Tracker - (Elysia + Bun)

See the full source code

### Web Traffic API - (Rust)

See the full source code3

Set up a Linode server

Configure and create a new linode, then [set up SSH authentication](https://www.linode.com/docs/guides/use-public-key-authentication-with-ssh/) so your terminal can securely access the Linode server. Connect to the newly created linode server and prepare the environment for Rust/JavaScript development based on the project forked earlier. Also install and configure [Git](https://git-scm.com/). 4

Transfer project to Linode server

Use SSH to access the server, clone the GitHub project, and complete the setup steps from its README. 5

Deploy

Build and run the project, then configure load balancing for it. [pm2](https://www.npmjs.com/package/pm2) is a strong option because it includes load balancing, log monitoring, and bug/exception alerts. Choose your preferred domain provider, reverse proxy approach, and SSL certificate setup. [Caddy](https://caddyserver.com/) is another suitable choice.

## Prerequisites

(Figure: Koyeb banner)

### Prerequisites

 Before starting, confirm that you:
- Have the [Turso CLI](https://docs.turso.tech/cli/installation) installed
- Have [signed up or logged in to Turso Cloud](https://docs.turso.tech/cli/authentication#signup)
- Have the [Fly.io CLI](https://fly.io/docs/hands-on/install-flyctl/) installed
 1

Locate your application

You should already have an application that uses the Turso Cloud database you plan to deploy to Fly. 2

Launch with Fly

Launch it with the Fly CLI:

```
fly launch

```

Fly will deploy the application automatically, but additional setup is still required. 3

Create a shared volume

Create the volume that will hold the embedded replica(s):

```
fly volumes create libsql_data

```

4

Mount and configure volumes

The earlier launch created `fly.toml` and `Dockerfile`. Update `fly.toml` so it mounts the new volume:

```
[[mounts]]
source = "libsql_data"
destination = "/app/data"

```

Inside `Dockerfile`, ensure that `ca-certificates` is installed and updated:

```
RUN apt-get update -qq && \
    apt-get install -y ca-certificates && \
    update-ca-certificates

```

Also place the following line after any `COPY` commands so the certificates are copied:

```
COPY --from=build /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/

```

5

Configure the libSQL client

Change `url` so it targets a local file, and set `syncUrl` to the Turso Cloud database URL:

```
import { createClient } from "@libsql/client";

const client = createClient({
  url: "file:/app/data/local.db",
  syncUrl: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
  syncInterval: 60,
});

```

6

Deploy your updated app

```
fly deploy

```

## Prerequisites

(Figure: Koyeb banner)

### Prerequisites

 Before starting, confirm that you:
- Have the [Turso CLI](https://docs.turso.tech/cli/installation) installed
- Have [signed up or logged in to Turso Cloud](https://docs.turso.tech/cli/authentication#signup)
- Have a Koyeb account; [create one](https://app.koyeb.com/) if needed
 1

Retrieve database credentials

An existing database is required to continue. If one is not available, [create one](https://docs.turso.tech/quickstart). Then get the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the credentials into the environment variables in `.env`.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

Keep these credentials as environment variables.2Fork one of the GitHub embedded replica project examples listed below

### My Expenses Tracker - (Elysia + Bun)

See the full source code

### Web Traffic API - (Rust)

Review the complete source codeOr, choose the deploy path:

### Deploy to Koyeb with a single-click

3

Add a new Koyeb app

1.  In the Koyeb control panel, create a new app.
2.  Choose GitHub as the deployment option.
3.  Bring the GitHub project into Koyeb by importing it.
4

On Koyeb's deploy page, fill in the environment variables.

(Figure: Koyeb deploy page - environment variables)5

Deploy

Use the **Deploy** button at the bottom to deploy the web service.

## Prerequisites

(Figure: Koyeb banner)

### Prerequisites

 Before starting, confirm that you:
- Have the [Turso CLI](https://docs.turso.tech/cli/installation) installed
- Have [signed up or logged in to Turso Cloud](https://docs.turso.tech/cli/authentication#signup)
- Have the [Railway CLI](https://docs.railway.app/guides/cli#installing-the-cli) installed
 1

Retrieve database credentials

An existing database is required to continue. If one is not available, [create one](https://docs.turso.tech/quickstart). Then get the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the credentials into the environment variables in `.env`.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

Store these values as environment variables. 2

Get application code

Fork and locally clone this embedded replica project from GitHub:

### My Expenses Tracker - (Elysia + Bun)

See the full source code

### Web Traffic API - (Rust)

See the full source code3

Create a new Railway project

Create a new Railway project by running the following command, and enter the project's name when prompted.

```
railway init

```

4

Add a service to the Railway project.

[Create a new empty service](https://docs.railway.app/guides/services#creating-a-service) in the Railway project so it can serve as the deployment target for the app. 5

Link application to service

List the services and choose the one to link with your application by running this command:

```
railway service

```

6

Add database credentials

In the Railway dashboard, open the service and add the Turso Cloud database credentials.

```
TURSO_DATABASE_URL=libsql://[db-name]-[github-username].turso.io
TURSO_AUTH_TOKEN=...
LOCAL_DB=file:local-db-name.db

```

7

Deploy

Deploy the application with this command:

```
railway up

```

Ensure the application is [exposed to the internet](https://docs.railway.app/guides/public-networking) so the public network can reach it. On a free plan, code deployments require connecting the Railway account to GitHub.

## Prerequisites

(Figure: Render banner)

### Prerequisites

 Before starting, confirm that you:
- Have the [Turso CLI](https://docs.turso.tech/cli/installation) installed
- Have [signed up or logged in to Turso Cloud](https://docs.turso.tech/cli/authentication#signup)
- Have a Render account; [create one](https://dashboard.render.com/) if needed
 1

Retrieve database credentials

An existing database is required to continue. If one is not available, [create one](https://docs.turso.tech/quickstart). Then get the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the credentials into the environment variables in `.env`.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

Store these values as environment variables. 2

Get application code

Fork this embedded replica project from GitHub locally:

### My Expenses Tracker - (Elysia + Bun)

Review the complete source codeOr, choose the deploy path:

### Deploy to Render with a single-click

3

Create a web service

In the Render dashboard, create a new Render **Web Service** by selecting the "New Web Service" button on the Web Services card. 4

Connect to Git repository

1.  Choose "build and deploy from a Git repository", then continue to the next page.
2.  Select "Connect" for the target project repository.
5

Set project's environment variables

On the web service configuration page, open "Advanced", add **a secret file**, and enter the database secret credentials. (Figure: Render secret credentials) 6

Deploy project

Go to the bottom of the web service configuration page and select "Create Web Service".

## How it works

Multi-DB Schemas is deprecated for all new users, while existing paid users may continue using it; see the [read the announcement](https://turso.tech/blog/upcoming-changes-to-the-turso-platform-and-roadmap) page. Turso lets one schema be shared by multiple databases, which supports multi-tenant applications where every tenant has its own database. **Using TypeScript?** For programmatic database provisioning, use the [`@tursodatabase/api`](https://www.npmjs.com/package/@tursodatabase/api) client instead of direct REST API calls. See the [SDK quickstart](https://docs.turso.tech/api-reference/introduction#quickstart-with-the-typescript-sdk). To start a multi-tenant application with Next.js, Drizzle, Clerk, libSQL, and Turso Multi-DB Schemas, try the [Turso Per User Starter](https://github.com/notrab/turso-per-user-starter).

### How it works

1. Create the database that will act as the parent schema database.
2. Create at least one database to serve as a child database.
3. Apply schema changes on the parent database; the child databases receive the new schema automatically.

### Usage

 Parent and child databases can be created and managed through the [Turso CLI](https://docs.turso.tech/cli/db/create) and the [Platform API](https://docs.turso.tech/api-reference/databases/create).

### Turso CLI

 Ensure the [Turso CLI](https://docs.turso.tech/cli/installation) is installed and that you are [logged in](https://docs.turso.tech/cli/auth/login). 1

Create Parent Database

Create one database with the `--type` flag set to `schema`:

```
turso db create parent-db --type schema

```

2

Create Schema

Open a shell connection to the database and use `CREATE TABLE`, `CREATE TRIGGER`, `CREATE VIEW`, or other statements needed to setup the schema:

```
turso db shell parent-db

```

3

Create Child Database(s)

Create one or more child databases, passing `--schema` with the parent database name:

```
turso db create child-db --schema parent-db

```

4

Apply Additional Schema Changes

After that, additional schema changes can be made on the parent database, and the child databases update automatically:

```
turso db shell parent-db "CREATE TABLE users (id INT PRIMARY KEY, name TEXT);"

```

### Platform API

 Confirm that you have an API Token and know the Organization name. 1

Create Parent Database

Create a database with `is_schema` set to `true`:

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases' \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
      "name": "parent-db",
      "group": "default",
      "is_schema": true
  }'

```

2

Create Schema

Use the shell, [SDK](https://docs.turso.tech/sdk), or a migration script to connect to the database and run `CREATE TABLE`, `CREATE TRIGGER`, `CREATE VIEW`, plus anything else needed to setup the schema: Turso CLI

```
turso db shell parent-db

```

3

Create Child Database(s)

Create one or more child databases, passing the parent database name in the `schema` field:

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases' \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
      "name": "child-db",
      "group": "default",
      "schema": "parent-db"
  }'

```

4

Apply Additional Schema Changes

Additional schema changes can now be applied to the parent database, after which the child databases update automatically:

```
turso db shell parent-db "CREATE TABLE users (id INT PRIMARY KEY, name TEXT);"

```

### Things to know

- Schema databases cannot be shared across groups or globally through an organization/account.
- `INSERT` rows may be added to the parent database and queried by the child database(s), but this is not recommended.
  - Watch for constraints that could conflict with the child database(s).

- A parent database with one or more child databases cannot be deleted.
- Applying a migration to the schema database follows this sequence:
  - First, it runs as a dry-run on the schema database and all associated databases.
  - If the dry-run succeeds, a migration job is created.
  - Each database that references the schema gets its own task.
  - The migration is then executed on every referencing database.
  - When every task succeeds, the schema database itself receives the migration last.
  - While any migrations are running, you cannot create or delete a database.

- **During a migration, write operations lock on all databases.**
- Ensure applications querying child databases can handle databases that have not yet received the schema update.
- Schema changes cannot be applied directly to a child database; use the parent (schema) database instead.
- Migration status can be checked through the `/jobs` endpoint; [learn more](https://docs.turso.tech/sdk/http/reference#schema-migration-jobs).

## How it works

Turso provides point-in-time recovery (PITR) for databases, so a database can be restored to a chosen point in time. It is useful after user mistakes such as accidentally dropping a table. Backups are generated automatically at `COMMIT`. Free plan users can restore anywhere within the last 24 hours. Developer, Scaler, and Pro plan restore windows cover the last 10, 30, or 90 days, respectively.

### How it works

1. Create a new database from the existing database through the CLI or API.
2. Update the application so it uses the new database connection string.
3. Delete the old database once it is no longer needed.
 The newly created database contains only data from before the requested timestamp. There can also be as much as a 15 seconds gap in the data immediately before that timestamp, depending on when Turso last completed its periodic batch checkpoint.

### Usage

```
turso db create my-new-database --from-db my-existing-database --timestamp 2024-01-01T00:00:00Z

```

 For details about every argument, use these references:

### CLI Reference

### API Reference

### Things to know

- A PITR restore creates a new database, so the application must be updated to use the new database connection string.
- A PITR restore cannot target an existing database.
- To connect to the new database, [create a new token](https://docs.turso.tech/cli/db/tokens/create) or use a group token.
- Delete the old database manually after it is no longer needed.
- Restores are counted against the database quota for your plan.
