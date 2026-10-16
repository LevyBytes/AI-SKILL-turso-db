# C installation credentials and example routes

[Context index](c-and-flutter-sdk-workflows.md) · [Continue](c-connections-queries-and-sync.md)

Scope: examples, libSQL C, installation, example setup, prerequisites, quickstart, and package addition.

## Examples

| Example | Description |
| --- | --- |
| local | Runs libsql against a local SQLite file, then creates a database, writes records, and reads them back. |
| remote | Connects to a remote database with URL and auth token supplied through environment variables. |
| sync | Illustrates how a local database and its remote counterpart synchronize. |
| batch | Runs several SQL statements together as one batch. |
| transactions | Walks through transactions: begin work, run operations, then commit or roll back. |
| memory | Uses an in-memory SQLite database when storage should be temporary or especially quick. |
| vector | Stores vector embeddings and queries them for similarity search. |
| encryption | Sets up an encrypted SQLite database and demonstrates basic data operations. |

## libSQL C

The C bindings are in technical preview. Report problems through [Discord](https://discord.gg/turso). This quickstart for C covers:
- Retrieve database credentials
- Install the libSQL package
- Connect with either a local Turso database or a remote one
- Execute a query using SQL
- Optionally sync changes into the local database
### Step 1: Retrieve database credentials

Continue with a database that already exists. If needed, [create one](https://docs.turso.tech/quickstart). Then fetch the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the credentials into the `.env` environment variables.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

Protect database tokens carefully in your application and deployment setup.

### Step 2: Install

Using libSQL from C requires building the library from source:

### libSQL C

Source build codeAfter compilation, link the library while compiling your C program:

```
gcc -o your_program your_program.c -L/path/to/libsql/lib -llibsql

```
