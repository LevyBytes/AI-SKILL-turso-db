# Db, Dev

> Cli reference. Original prose; identifiers preserved verbatim.

## Overview

Topics covered: Flags; Show; Using a dump locally.

## Flags

Remove a database's access allow rules so connections may originate from anywhere.

```
turso db config allow-rules clear <database-name> [flags]

```

 With no flags, the command removes both the IP list and the AWS VPC endpoint list. Pass `--ips` or `--aws-vpcs` when only one list should be cleared.

### Flags

| Flag | Description |
| --- | --- |
| `--ips` | Remove entries only from the allowed IP list. |
| `--aws-vpcs` | Remove entries only from the allowed AWS VPC endpoint ID list. |

### Examples

### Clear all rules

```
turso db config allow-rules clear my-db

```

### Clear only the IP list

```
turso db config allow-rules clear my-db --ips

```

### Clear only the VPC list

```
turso db config allow-rules clear my-db --aws-vpcs

```

## Flags

Configure database access allow rules. A supplied flag overwrites its matching list, while omitted flags leave their lists as they are.

```
turso db config allow-rules set <database-name> [--ip <address-or-cidr>]... [--aws-vpc <vpce-id>]...

```

 Provide `--ip`, `--aws-vpc`, or both. To make access unrestricted, run [`turso db config allow-rules clear`](https://docs.turso.tech/cli/db/config/allow-rules/clear).

### Flags

| Flag | Description |
| --- | --- |
| `--ip` | Allowed IP address or CIDR block. Repeatable. Overwrites the existing IP list. |
| `--aws-vpc` | Allowed AWS VPC endpoint ID (`vpce-...`). Repeatable. Overwrites the existing VPC list. |

### Examples

### Allow a single IP

```
turso db config allow-rules set my-db --ip 203.0.113.7

```

### Allow a CIDR range

```
turso db config allow-rules set my-db --ip 10.0.0.0/8

```

### Allow multiple IPs

```
turso db config allow-rules set my-db --ip 203.0.113.7 --ip 10.0.0.0/8

```

### Restrict to an AWS VPC endpoint

```
turso db config allow-rules set my-db --aws-vpc vpce-0fe6c8807461bba49

```

### Combine IP and VPC rules

 If both lists exist, a connection has to meet both requirements.

```
turso db config allow-rules set my-db \
  --ip 10.0.0.0/8 \
  --aws-vpc vpce-0fe6c8807461bba49

```

## Show

Display a database's access allow rules.

```
turso db config allow-rules show <database-name>

```

 If no rules exist, the database permits every connection and the command outputs:

```
Access allow rules are empty: connections from any source are accepted

```

 If rules exist, the command outputs each list:

```
Allowed IPs:
  203.0.113.7
  10.0.0.0/8
Allowed AWS VPC endpoint IDs:
  vpce-0fe6c8807461bba49

```

## Flags

Use this command to create a database inside a chosen group:

```
turso db create [database-name]

```

 `[database-name]` is optional. When it is omitted, Turso uses your project name. If multiple groups exist, include the `--group` flag.

### Flags

| Flag | Description |
| --- | --- |
| `--enable-extensions` | Turns on experimental SQLite extensions support. |
| `--from-csv` | Build the database from a csv file. |
| `--csv-table-name` | Table name inside the csv file. |
| `--from-db` | Choose a different database as the data source. |
| `--timestamp` | Choose an earlier point in time for copying data from the selected database. Use only with the â€˜from-dbâ€™ flag. The value must use RFC3339 format, for example â€˜2023-09-29T10:16:13-03:00â€™ |
| `--from-dump` | Build the database from a local SQLite dump. |
| `--from-dump-url` | Build the database from a remote SQLite dump. |
| `--from-file` | Build the database from a local SQLite3-compatible file. |
| `--group` | Place the database in the chosen group. |
| `--size-limit` | Maximum database size in bytes. Unit-suffixed values are accepted too, such as 1mb, 256mb, 1gb. |
| `--canary` | Use database canary build. |
| `-w`, `--wait` | Wait until the database can accept requests. |
| `-h`, `--help` | Get help for create. |

### Examples

 The following examples show common ways to use the `db create` command.

### Create database in a specific group

 Create a database in a chosen group by passing `--group`:

```
turso db create [database-name] --group <group-name>

```

### Create database from SQLite file

 Create a Turso database from an existing SQLite file with `--from-file`:

```
turso db create [database-name] --from-file ./path/to/file.db

```

 Files can be no larger than 2GB.

### Create database from SQLite dump

 Create a database from `.dump` output with `--from-dump`:

```
turso db create [database-name] --from-dump ./path/to/dump.sql

```

### Create database from another existing database

 Create a Turso database by copying an existing database with `--from-db`:

```
turso db create [database-name] --from-db <existing-database-name>

```

### Create database from a point in time

 Create a Turso database from an existing database at a chosen time by using `--from-db` with `--timestamp`:

```
turso db create [database-name] --from-db <existing-database-name> --timestamp 2024-01-01T10:10:10-10:00

```

### Create database from a CSV file

 Create a Turso database from a CSV file with `--from-csv`:

```
turso db create [database-name] --from-csv ./path/to/file.csv --csv-table-name <desired-table-name>

```

### Create a database with extensions enabled

 Create a database with experimental SQLite [extensions](https://docs.turso.tech/libsql#extensions) support by passing `--enable-extensions`:

```
turso db create [database-name] --enable-extensions

```

 Passing `--enable-extensions` turns on extensions for every database in the group.

## Flags

Use this command to destroy a database:

```
turso db destroy <database-name> [flags]

```

### Flags

| Flag | Description |
| --- | --- |
| `-y`, `--yes` | Confirms the destruction the database. |

## Flags

Export a Turso database snapshot into a SQLite file. The command writes the current generation of a Turso database to a local SQLite file, but that file might not include the newest changes. After export, use SDK sync to bring the database up to date.

```
turso db export <database> [flags]

```

### Flags

| Flag | Description |
| --- | --- |
| `--output-file` | Set the output filename (default: `<database>.db`). |
| `--overwrite` | Replace the output file when it already exists. |
| `--with-metadata` | Include metadata in the export |
| `-h`, `--help` | Help for export |

## Flags

Use this command to import an existing SQLite file into Turso Cloud:

```
turso db import ~/path/to/database.db

```

 Confirm that the SQLite file uses WAL journal mode. If your account has multiple groups, include `--group`.

### Flags

| Flag | Description |
| --- | --- |
| `--group` | Import the database into the selected group. |
| `-h`, `--help` | Get help for import. |

## Flags

Use this command to inspect database usage, including storage consumed plus rows read and written:

```
turso db inspect <database-name>

```

### Flags

| Flag | Description |
| --- | --- |
| `--queries` | Show database query statistics. |
| `--verbose` | Include extra details such as location and instance type. |

### Examples

### Top Queries

 Use the Turso CLI to retrieve the SQL statements run most often or consuming the most resources against your database:

```
turso db inspect <database-name> --queries

```

## Flags

Run this command to list every database available to the current user or organization:

```
turso db list

```

### Flags

| Flag | Description |
| --- | --- |
| `--group <group-name>` | Filter databases by group. |

## Flags

Run this command to list the supported database locations:

```
turso db locations

```

### Flags

| Flag | Description |
| --- | --- |
| `-l`, `--show-latencies` | Show latency from your current location to each Turso location. |

## Flags

Use this command to connect directly to a Turso database:

```
turso db shell <database-name> [sql] [flags]

```

### Flags

| Flag | Description |
| --- | --- |
| `--instance <instance>` | Connect to the chosen database instance. |
| `--location <location-code>` | Connect to the database in the chosen location. |
| `--proxy <url>` | Proxy used for the database connection. |

### Examples

### Execute SQL

 Run SQL against a database directly from the shell:

```
turso db shell <database-name> "SELECT * FROM users"

```

### Database dump

 Use this command to dump the contents of a Turso database:

```
turso db shell <database-name> .dump > dump.sql

```

 A `.dump` output can recreate a database and excludes libSQL or SQLite internal tables.

### Load from dump

 Use this command to load a dump file into a new database:

```
turso db shell <database-name> < dump.sql

```

### Shell with libSQL server

 When running `turso dev` locally, provide your database URL to open the shell:

```
turso db shell http://127.0.0.1:8080

```

## Flags

Retrieve database details such as name, ID, libSQL server version, group, size, and location.

```
turso db show <database-name> [flags]

```

 You can also retrieve the database URLs with this command:

### Flags

| Flag | Description |
| --- | --- |
| `--url` | Display the URL for the database HTTP API. |
| `--http-url` | Display the HTTP URL for the database HTTP API. |

## Flags

Use this command to create a new token for connecting to a single database:

```
turso db tokens create <database-name> [flags]

```

### Flags

| Flag | Description |
| --- | --- |
| `-e`, `--expiration` | Token expiration time; use `never` or a day value such as `7d` . |
| `-r`, `--read-only` | Limit the token to read only access. |

### Examples

 The examples below show common uses of the `db tokens create` command.

### Create a token with read only access

 Create a database token with read only access by running this command:

```
turso db tokens create <database-name> --read-only

```

### Create a token with a specific expiration time

 Create a token that expires at a chosen time with this command:

```
turso db tokens create <database-name> --expiration 7d3h2m1s

```

## Flags

Run this command to invalidate every token for a database:

```
turso db tokens invalidate <database-name> [flags]

```

### Flags

| Flag | Description |
| --- | --- |
| `-y`, `--yes` | Confirms invalidation for all current db tokens. |

 Tokens for the group containing the database are invalidated too. Existing tokens stop working and must be generated again.

## Using a dump locally

For local Turso development, choose one of these approaches:
- SQLite — local SQLite database file
- Turso CLI — managed libSQL server
- Turso Database — remote Turso database

### Using a dump locally

 You can dump your production database whenever needed and use that dump for local development: 1

Generate a dump with the Turso CLI.

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

### Turso CLI

 For [libSQL](https://docs.turso.tech/libsql) features such as [extensions](https://docs.turso.tech/libsql#extensions), use the Turso CLI:

```
turso dev

```

 This starts a local libSQL server and creates a database. Then connect with the `url` option in your SDK:

```
import { createClient } from "@libsql/client";

const client = createClient({
  url: "http://127.0.0.1:8080",
});

```

 Stopping the server discards changes. To keep changes or work from a production dump, pass `--db-file` with the SQLite filename:

```
turso dev --db-file local.db

```

### Turso Database

 If you already have a Turso database, use it for development by passing the `url` option to your SDK. A hosted Turso database can add platform costs and count against quota, so SQLite or Turso CLI is often better for local work.

### Connecting a GUI

 During development, connect a GUI to a SQLite, libSQL, or Turso database with one of these tools:
- [Beekeeper Studio](https://www.beekeeperstudio.io/db/libsql-client/) - Windows, Linux, and macOS.
- [Outerbase](https://www.outerbase.com) - Browser-based.
- [TablePlus](https://tableplus.com) - Linux, macOS, and Windows.
- [Dataflare](https://dataflare.app) - Paid, with a limited free version, for macOS, Windows, and Linux.
- [Outerbase Studio](https://libsqlstudio.com) - Browser-based.
- [DBeaver](https://dbeaver.io) - Linux, macOS, and Windows.
