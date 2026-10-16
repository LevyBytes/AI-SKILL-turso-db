# Application sync connection and bootstrap

[Context index](database-sync-workflows.md) · [Continue](application-sync-push-pull-and-checkpoint.md)

## Offline-first writes

This guide explains how to create a Turso database and use sync from an application. This setup relies on Turso Cloud for syncing local Turso databases, so it expects an existing account.

### Step 1: Setup Turso Cloud database

- Use the [Quickstart](https://docs.turso.tech/quickstart) to install the CLI and create a database
-  Get the database URL (`libsql://...`):

```
turso db show <db>

```

- Generate a token for the app:

```
turso db tokens create <db>

```

### Step 2: Set up a basic sync connection

Sync setup requires three values:
- Local path: the storage location for the local synced tursodb file
- Remote URL: the Turso Cloud URL (`libsql://...`)
- Auth token: the Turso Cloud token used to authenticate requests

```
import { connect } from '@tursodatabase/sync';

const db = await connect({
  path: './app.db',                               // local path
  url: 'libsql://...',                            // remote URL (generated with turso db show <db-name> --url)
  authToken: process.env.TURSO_AUTH_TOKEN,        // authentication token (generated with turso db tokens create <db-name>)
  // longPollTimeoutMs: 10_000,                   // optional: server waits before replying to pull
  // bootstrapIfEmpty: false,                     // set to false to avoid bootstrapping on first run
});

```

During the first run, the local database bootstraps itself from the remote, so the initial connection must be able to reach the remote. If `bootstrap_if_empty` is set to `false`, the local database begins empty instead. You can bootstrap it or refresh it later by calling `pull()` explicitly. 3
