# Flutter libSQL installation and connections

[Context index](c-and-flutter-sdk-workflows.md) · [Continue](flutter-libsql-queries-and-sync.md)

## Quickstart

This community-maintained SDK may not have official Turso support or the newest feature coverage. For help and feedback, use the `#libsql-dart` channel [on Discord](https://tur.so/discord). This Flutter/Dart quickstart covers:
- Retrieve database credentials
- Add the Flutter/Dart libSQL client to your project
- Connect with a local Turso database or a remote one
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

Store these values as environment variables.

### Step 2: Install libsql_dart

```
flutter pub add libsql_dart

```

### Step 3: Initialize

Create a connection to your local or remote database with the `LibsqlClient` constructor:

Embedded Replicas

```
final dir = await getApplicationCacheDirectory();
final path = '${dir.path}/local.db';

final client = LibsqlClient(path)
  ..authToken = '<TOKEN>'
  ..syncUrl = '<TURSO_OR_LIBSQL_URL>'
  ..syncIntervalSeconds = 5
  ..readYourWrites = true;

```

Local only

```
final dir = await getApplicationCacheDirectory();
final path = '${dir.path}/local.db';

final client = LibsqlClient(path);

```

Remote only

```
final client = LibsqlClient('<TURSO_OR_LIBSQL_URL>')
  ..authToken = '<TOKEN>';

```

### Step 4: Connect

```
await client.connect();

```
