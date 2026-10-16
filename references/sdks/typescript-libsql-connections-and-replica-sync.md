# TypeScript libSQL connections and replica sync

[Context index](typescript-package-and-api-selection.md) · [Continue](typescript-libsql-query-and-response-api.md)

### @libsql/client

 The `@libsql/client` package uses [libSQL](https://github.com/tursodatabase/libsql), the open-source SQLite fork behind Turso Cloud today. It is production-ready and well exercised in real deployments, and it fits cases that require ORM support outside Drizzle, such as Prisma, or projects that already depend on a `@libsql/client` codebase. In `@libsql/client` Embedded Replicas, reads happen locally; writes go to the cloud primary and later appear on the replica. Embedded Replicas are fully supported. For new sync-focused builds, use `@tursodatabase/sync` together with [Turso Sync](https://docs.turso.tech/sync/usage).

### Installing

 Start by adding the `@libsql/client` dependency to your project:

```
npm install @libsql/client

```

### Initializing

```
import { createClient } from "@libsql/client";

export const turso = createClient({
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

```

 When working with local libsql or an sqlite file, `authToken` does not need to be provided.

### In-Memory Databases

```
import { createClient } from "@libsql/client";

const client = createClient({
  url: "file::memory:",
});

```

### Local Development

 For local development, pass an SQLite file path into `createClient`:

```
import { createClient } from "@libsql/client";

const client = createClient({
  url: "file:path/to/db-file.db",
  authToken: "...",
});

```

 `@libsql/client/web` cannot use local file URLs.

### Embedded Replicas

 If your workload requires **offline writes**, **bidirectional sync**, or **multi-writer convergence**, prefer `@tursodatabase/sync`: reads and writes both happen locally, and synchronization is explicit through `push()` / `pull()`. To use embedded replicas, pass the Turso Database URL as `syncUrl`:

```
import { createClient } from "@libsql/client";

const client = createClient({
  url: "file:path/to/db-file.db",
  syncUrl: "libsql://[databaseName]-[organizationSlug].turso.io",
  authToken: "...",
});

```

 Embedded Replicas require an environment where the file system is accessible.

#### Manual Sync

```
await client.sync();

```

#### Periodic Sync

```
import { createClient } from "@libsql/client";

const client = createClient({
  url: "file:path/to/db-file.db",
  syncUrl: "libsql://[databaseName]-[organizationSlug].turso.io",
  syncInterval: 60,
  authToken: "...",
});

```
