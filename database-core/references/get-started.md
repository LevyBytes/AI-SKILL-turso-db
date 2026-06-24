# Introduction, Libsql, Local Development, Quickstart

> Database Core reference. Original prose; identifiers preserved verbatim.

## Overview

Scope: Get Started; tursodatabase/libsql; Using a dump locally; TypeScript.

## Get Started

(Figure: Turso Quickstart) Turso provides a compact database foundation for ambitious AI-era software. It is designed for efficient development across traditional apps, smart devices, embodied AI, agents, and related workloads.

### Get Started

 Select the route you want for starting with Turso:

### Turso Database (Embedded)

A portable embedded database engine for offline use, browser environments, and on-device deployments.

### AgentFS

An SDK for AI agents that handles filesystem access and state while keeping auditability built in.

### Turso Cloud

A managed database platform that includes branching, analytics, backups,and additional features.

### How Well You Know Turso?

Check how much you know about Turso, then share your score. **Need sync?** For local reads and writes that push and pull cloud changes, begin with [Turso Database](https://docs.turso.tech/tursodb/quickstart) plus [Turso Sync](https://docs.turso.tech/sync/usage).

### Turso Database Features

 Turso's next-generation SQLite direction includes these capabilities for modern apps:

### Vector Search

Built-in similarity search for AI apps and RAG workflows, without requiring extensions

### Async Design

Async-first primitives, including Linux io_uring, help applications stay responsive

### SQLite Compatible

Backwards compatible with SQLite while targeting the next generation of applications

### Concurrent Writes

Multiple writers, zero conflicts, without locking

### Richer Type System

A stricter, broader type model, with User-Defined Types (experimental) included

### Open Contribution

Help shape the next evolution of SQLite by contributing directly

### Join Our Communities

 Reach developers and the Turso team through these channels:

### Turso Database Core Developers

Participate in Turso Database core development discussions with the community

### Turso Community

Talk with Turso Cloud users and find support in the community

### Follow Us

### GitHub

### X (Twitter)

### LinkedIn

## tursodatabase/libsql

libSQL began as a SQLite fork, motivated by SQLite being [open-source but not open-contribution](https://www.sqlite.org/copyright.html). It is ready for production use, remains fully backwards compatible with SQLite, and includes additions such as native vector search. libSQL was Turso's starting point. The current priority is [Turso Database](https://docs.turso.tech/tursodb/quickstart), a from-scratch SQLite rewrite built for very high database density without depending on servers or connectivity. For new work, Turso recommends [Turso Database](https://docs.turso.tech/tursodb/quickstart). Choose libSQL when a mission-critical system needs a proven base today.

### tursodatabase/libsql

Use GitHub to view the libSQL source, open issues or feature requests, and submit pull requests.

### Discord

Use Discord for conversations about libSQL development.

### libSQL vs. Turso Database

 libSQL forks SQLite while preserving the file format, API, and complete backwards compatibility. It adds capabilities the SQLite ecosystem wanted but could not upstream. [Turso Database](https://docs.turso.tech/tursodb/quickstart) takes another path: it rewrites SQLite from the ground up, preserving SQLite semantics on a modern architecture for concurrent writes, async I/O, and extremely high database density.
|  | libSQL | Turso Database |
| --- | --- | --- |
| Approach | Fork of SQLite | Full rewrite of SQLite |
| Maturity | Production-ready | Evolving (beta) |
| SQLite compatibility | Full (identical file format and API) | Backwards compatible |
| Best for | Mission-critical workloads today | New builds, agents, smart devices, high-density scenarios |

 Turso maintains both projects, and both accept open contribution. [Turso Cloud](https://docs.turso.tech/turso-cloud) runs on libSQL today, with plans to incorporate the Turso Database engine later.

### Extensions

 For vector capabilities, prefer the native [libSQL vector datatype](https://docs.turso.tech/features/ai-and-embeddings) instead of enabling vector extensions. The complete supported-extension list is available [here](https://docs.turso.tech/features/sqlite-extensions).

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

2

Create SQLite file from dump

```
cat dump.sql | sqlite3 local.db

```

3

Connect to SQLite file

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

### Connecting a GUI

 For development, these tools can connect to SQLite, libSQL, or Turso databases:
- [Beekeeper Studio](https://www.beekeeperstudio.io/db/libsql-client/) - macOS, Linux, and Windows
- [Outerbase](https://www.outerbase.com) - Browser-based
- [TablePlus](https://tableplus.com) - macOS, Windows, and Linux
- [Dataflare](https://dataflare.app) - Paid, with a limited free version, for macOS, Windows, and Linux
- [Outerbase Studio](https://libsqlstudio.com) - Browser-based
- [DBeaver](https://dbeaver.io) - macOS, Windows, and Linux

## TypeScript

**This quickstart walks through how to:**
1. Install the Turso CLI
2. Signup to Turso Cloud
3. Set up your first Turso Cloud database
4. Connect to Database Shell
 1

Install the Turso CLI

This quickstart uses the Turso CLI to manage databases and open the database shell.

```
brew install tursodatabase/tap/turso

```

2

Signup to Turso Cloud

Run the next command to open sign-up in your browser:

```
turso auth signup

```

3

Create a Database

Create the first database and name it `my-db`:

```
turso db create my-db

```

Inspect the database you just created with this command:

```
turso db show my-db

```

4

Connect to Database Shell

After the database exists, connect with the `shell` command:

```
turso db shell my-db

```

If you used another database name, substitute it for `my-db`.

Create table

Create a SQL table named `users`:

```
CREATE TABLE users (
  ID INTEGER PRIMARY KEY AUTOINCREMENT,
  name TEXT
);

```

Insert into new table

Next, add one row to the `users` table:

```
INSERT INTO users (name) VALUES ('Iku');

```

Select all rows from table

Finally, query for all `users`:

```
SELECT * FROM users;

```

When you are ready for the next step, exit the shell:

```
.quit

```

5

Connect the application to the database

Your application is ready to connect to the database. Choose one SDK below to continue:

### TypeScript

### Rust

### Go

### Python

### Android

### Swift

### PHP

### Ruby

### Ruby on Rails

### C

### SQL over HTTP
