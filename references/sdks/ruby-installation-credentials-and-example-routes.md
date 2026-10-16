# Ruby installation credentials and example routes

[Context index](python-and-ruby-sdk-workflows.md) · [Continue](ruby-connections-queries-and-replica-sync.md)

## Examples

| Example | Description |
| --- | --- |
| local | Uses libsql with a local SQLite file, then creates a database, inserts rows, and runs queries. |
| remote | Connects to a remote database and expects environment variables for the URL and auth token. |
| sync | Shows synchronization between a local database and a remote database. |
| transactions | Covers transaction flow, including begin, operations, commit, and rollback. |
| memory | Uses an in-memory SQLite database for temporary data or fast access. |
| vector | Stores vector embeddings and queries them for similarity search. |
| encryption | Creates an encrypted SQLite database and demonstrates setup plus data operations. |

## Quickstart

This Ruby quickstart is currently in technical preview. [Join us in Discord](https://discord.gg/turso) to report issues. It covers these tasks:
- Retrieve database credentials
- Install the libSQL package
- Connect to either a local Turso database or a remote Turso database
- Execute a query using SQL
- Optionally sync changes into a local database
### Step 1: Retrieve database credentials

You need an existing database before continuing. If one is not available, [create one](https://docs.turso.tech/quickstart). Then retrieve the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the credential values into the environment variables defined in `.env`.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

Make sure database tokens are handled securely.

### Step 2: Install

Install this Rubygem inside your Ruby project:

```
bundle add turso_libsql

```
