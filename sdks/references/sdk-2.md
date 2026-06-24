# C, Flutter

> Sdks reference. Original prose; identifiers preserved verbatim.

## Overview

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
 1

Retrieve database credentials

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

Protect database tokens carefully in your application and deployment setup.2

Install

Using libSQL from C requires building the library from source:

### libSQL C

Source build codeAfter compilation, link the library while compiling your C program:

```
gcc -o your_program your_program.c -L/path/to/libsql/lib -llibsql

```

3

Connect

Initialize libSQL first, create a `libsql_database_t`, then open a `libsql_connection_t` for it:

Embedded Replicas

```
#include "libsql.h"

libsql_setup((libsql_config_t){0});

libsql_database_t db = libsql_database_init((libsql_database_desc_t){
    .path = "local.db",
    .url = "TURSO_DATABASE_URL",
    .auth_token = "TURSO_AUTH_TOKEN",
    .sync_interval = 300
});

libsql_connection_t conn = libsql_database_connect(db);

```

Local only

```
#include "libsql.h"

libsql_setup((libsql_config_t){0});

libsql_database_t db = libsql_database_init((libsql_database_desc_t){
    .path = "local.db"
});

libsql_connection_t conn = libsql_database_connect(db);

```

Remote only

```
#include "libsql.h"

libsql_setup((libsql_config_t){0});

libsql_database_t db = libsql_database_init((libsql_database_desc_t){
    .url = "TURSO_DATABASE_URL",
    .auth_token = "TURSO_AUTH_TOKEN"
});

libsql_connection_t conn = libsql_database_connect(db);

```

4

Execute

To run a SQL query on your database, prepare the statement first and execute it afterward:

```
libsql_statement_t stmt = libsql_connection_prepare(conn, "SELECT * FROM users");
libsql_rows_t rows = libsql_statement_query(stmt);

```

Use placeholders when values need to be bound at execution time:

```
libsql_statement_t stmt = libsql_connection_prepare(conn, "SELECT * FROM users WHERE id = ?");
libsql_statement_bind_value(stmt, libsql_integer(1));
libsql_rows_t rows = libsql_statement_query(stmt);

```

5

Sync (Embedded Replicas only)

For embedded replicas, call `libsql_database_sync()` on the database to bring the local copy up to date with the primary, except when `sync_interval` handles it automatically. Calling `sync` while `sync_interval` is enabled is still allowed:

```
libsql_sync_t sync = libsql_database_sync(db);
if (sync.err) {
    fprintf(stderr, "Error syncing database: %s\n", libsql_error_message(sync.err));
} else {
    printf("Sync completed. Frame number: %llu, Frames synced: %llu\n",
            (unsigned long long)sync.frame_no,
            (unsigned long long)sync.frames_synced);
}

```

## Installing

The C bindings remain in technical preview. Report issues in [Discord](https://discord.gg/turso).

### Installing

 Projects using the libSQL C bindings must include `libsql.h` and link the `liblibsql.so` shared library.

### libSQL C

Build from source code. Before working with libSQL, call the setup function:

```
libsql_setup((libsql_config_t){0});

```

### In-Memory Databases

 For non-persistent use cases, libSQL can connect to [in-memory databases]([https://www.sqlite.org/inmemorydb.htm](https://www.sqlite.org/inmemorydb.htm):

```
libsql_database_t db = libsql_database_init((libsql_database_desc_t){.path = ":memory:"});
libsql_connection_t conn = libsql_database_connect(db);

```

### Local Development

 Local development can use an SQLite file:

```
libsql_database_t db = libsql_database_init((libsql_database_desc_t){.path = "local.db"});
libsql_connection_t conn = libsql_database_connect(db);

```

### Remote Databases

 A remote database connection uses a URL together with an auth token:

```
libsql_database_t db = libsql_database_init((libsql_database_desc_t){
    .url = "TURSO_DATABASE_URL",
    .auth_token = "TURSO_AUTH_TOKEN"
});
libsql_connection_t conn = libsql_database_connect(db);

```

### Embedded Replicas

 Embedded replicas can sync from the remote URL while sending writes to the remote primary database:

```
libsql_database_t db = libsql_database_init((libsql_database_desc_t){
    .path = "local.db",
    .url = "TURSO_DATABASE_URL",
    .auth_token = "TURSO_AUTH_TOKEN"
});
libsql_connection_t conn = libsql_database_connect(db);

```

### Manual Sync

 Use `libsql_database_sync` to manually synchronize the local database with its remote counterpart:

```
libsql_sync_t sync = libsql_database_sync(db);

```

### Sync Interval

 Set `sync_interval` in the database description to run background synchronization on a schedule:

```
libsql_database_t db = libsql_database_init((libsql_database_desc_t){
    .path = "local.db",
    .url = "TURSO_DATABASE_URL",
    .auth_token = "TURSO_AUTH_TOKEN",
    .sync_interval = 60000
});

```

### Read Your Own Writes

 The `not_read_your_writes` field controls whether a connection sees its own writes immediately on following reads. This behavior is **enabled by default**; set `not_read_your_writes` to `true` to turn it off:

```
libsql_database_t db = libsql_database_init((libsql_database_desc_t){
    .path = "local.db",
    .url = "TURSO_DATABASE_URL",
    .auth_token = "TURSO_AUTH_TOKEN",
    .not_read_your_writes = true
});

```

### Simple query

 For simple parameter-free queries, call `libsql_connection_batch`:

```
libsql_batch_t batch = libsql_connection_batch(conn, "CREATE TABLE users (id INTEGER PRIMARY KEY, name TEXT)");

```

 When a query returns rows, call `libsql_connection_prepare` followed by `libsql_statement_query`:

```
libsql_statement_t stmt = libsql_connection_prepare(conn, "SELECT * FROM users");
libsql_rows_t rows = libsql_statement_query(stmt);

```

### Prepared Statements

 Prepare statements with `libsql_connection_prepare`, then run them through `libsql_statement_execute` or `libsql_statement_query`:

```
libsql_statement_t stmt = libsql_connection_prepare(conn, "INSERT INTO users (name) VALUES (?)");
libsql_statement_bind_value(stmt, libsql_text("John Doe", 8));
libsql_execute_t result = libsql_statement_execute(stmt);

```

### Placeholders

 SQL statements can use positional placeholders as well as named placeholders in libSQL:

```
libsql_statement_t stmt = libsql_connection_prepare(conn, "SELECT * FROM users WHERE id = ?");
libsql_statement_bind_value(stmt, libsql_integer(1));

```

### Transactions

 libSQL supports transactions:

```
libsql_transaction_t tx = libsql_connection_transaction(conn);
// Perform operations within the transaction
libsql_transaction_commit(tx); // or libsql_transaction_rollback(tx)

```

### Cleanup

 Release allocated resources after the work is finished:

```
libsql_statement_deinit(stmt);
libsql_connection_deinit(conn);
libsql_database_deinit(db);

```

## example

[dikatok](https://docs.turso.tech/dikatok)  / ** [libsql_dart](https://docs.turso.tech/dikatok/libsql_dart) ** Public
-

###  Uh oh!

Loading failed. Reload the page and try again.

-  [Notifications](https://docs.turso.tech/login?return_to=%2Fdikatok%2Flibsql_dart) Sign in before changing notification settings
-  [Fork 8](https://docs.turso.tech/login?return_to=%2Fdikatok%2Flibsql_dart)
-   [Star  29](https://docs.turso.tech/login?return_to=%2Fdikatok%2Flibsql_dart)

### Expand file tree

/

### example

/Copy path

### Directory actions

### More options

More options

### Directory actions

### More options

More options

### Latest commit

### History

[History](https://docs.turso.tech/dikatok/libsql_dart/commits/main/example)History/

### example

/Copy path

### Folders and files

| Name | Name | Last commit message | Last commit date |
| --- | --- | --- | --- |
| ### parent directory.. |  |  |  |
| android | android |  |  |
| ios | ios |  |  |
| lib | lib |  |  |
| linux | linux |  |  |
| macos | macos |  |  |
| web | web |  |  |
| windows | windows |  |  |
| .env | .env |  |  |
| .gitignore | .gitignore |  |  |
| .metadata | .metadata |  |  |
| README.md | README.md |  |  |
| analysis_options.yaml | analysis_options.yaml |  |  |
| devtools_options.yaml | devtools_options.yaml |  |  |
| docker-compose.yaml | docker-compose.yaml |  |  |
| pubspec.lock | pubspec.lock |  |  |
| pubspec.yaml | pubspec.yaml |  |  |

### README.md

Outline

### libsql_dart_example

### Getting Started

Do either of the following:

- Create a remote Turso database
- Start a local Turso database with `https://docs.turso.tech/local-development#turso-cli`
- Launch a local libsql instance with the supplied `docker-compose.yaml`

Fill the credentials in `.env`

Start the example with `flutter run --debug --dart-define-from-file=.env`

## Prerequisites

### Prerequisites

 Before beginning, confirm that you have completed these steps:
- [Install Turso CLI](https://docs.turso.tech/cli/installation)
- [Sign up for Turso or log in](https://docs.turso.tech/cli/authentication#signup)
 1

Add packages to your project

```
flutter pub add drift_libsql drift drift_flutter dev:build_runner dev:drift_dev

```

2

Retrieve database credentials

Get the database URL:

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

3

Create a table schema

```
class TaskTable extends Table {
  IntColumn get id => integer().autoIncrement()();
  TextColumn get title => text()();
  TextColumn get description => text()();
  BoolColumn get completed => boolean()();
}

```

4

Create db class

```
@DriftDatabase(tables: [TaskTable])
class AppDatabase extends _$AppDatabase {
  AppDatabase(super.e);

  @override
  int get schemaVersion => 1;
}

```

5

Run build runner

```
dart run build_runner build

```

6

Create the db

```
final db = AppDatabase(DriftLibsqlDatabase(
    "${dir.path}/replica.db",
    syncUrl: url,
    authToken: token,
    readYourWrites: true,
    syncIntervalSeconds: 3,
  ));

```

7

Perform SQL operations

```
await db.into(db.taskTable).insert(TaskTableCompanion.insert(
	title: task.title,
	description: task.description,
	completed: task.completed));

```

## Quickstart

This community-maintained SDK may not have official Turso support or the newest feature coverage. For help and feedback, use the `#libsql-dart` channel [on Discord](https://tur.so/discord). This Flutter/Dart quickstart covers:
- Retrieve database credentials
- Add the Flutter/Dart libSQL client to your project
- Connect with a local Turso database or a remote one
- Execute a query using SQL
- Optionally sync changes into the local database
 1

Retrieve database credentials

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

Store these values as environment variables.2

Install libsql_dart

```
flutter pub add libsql_dart

```

3

Initialize

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

4

Connect

```
await client.connect();

```

5

Execute

```
await client.execute("create table if not exists customers (id integer primary key, name text);");

```

Placeholders can be used when values need binding:

```
final statement = await client
  .prepare("select * from customers where id = ?");

await statement.query(positional: [1])

```

6

Sync (Embedded Replicas only)

For embedded replicas, call `sync()` on the connector so the local database syncs with the primary database.

```
await client.sync();

```

## Add the package to your project

The libSQL package for Flutter / Dart provides the pieces needed for Turso / libSQL work. It is built on the libSQL Rust crate and `flutter_rust_bridge`, which bridges Rust and Dart.

### Add the package to your project

```
flutter pub add libsql_dart

```

 You can also add the dependency manually in your project's `pubspec.yaml`.

```
libsql_dart:

```

### Initializing

 Create the database client by calling the `LibsqlClient` constructor. Supported configurations include in-memory databases, local sqlite files, remote Turso / libSQL databases, and embedded replicas.

### In-Memory Databases

 When persistence is not required, libSQL can connect to [in-memory databases](https://www.sqlite.org/inmemorydb.html):

```
final client = LibsqlClient(":memory:");

```

### Local Development

 For local development, pass an SQLite file path to `LibsqlClient`:

```
final dir = await getApplicationCacheDirectory();
final path = '${dir.path}/local.db';

final client = LibsqlClient(path);

```

### Remote

 Use a Turso Database URL to connect to a remote database:

```
final client = LibsqlClient('<TURSO_OR_LIBSQL_URL>')
	..authToken = '<TOKEN>';

```

### Embedded Replicas

 Create an embedded replica by passing your Turso Database URL to `syncUrl`:

```
final dir = await getApplicationCacheDirectory();
final path = '${dir.path}/local.db';

final client = LibsqlClient(path)
	..authToken = '<TOKEN>'
	..syncUrl = '<TURSO_OR_LIBSQL_URL>'
	..readYourWrites = true;

```

 Embedded Replicas require filesystem access to work.

### Connect

```
await client.connect();

```

### Manual Sync

 `sync()` lets you manually synchronize the local database with the remote counterpart:

```
await client.sync();

```

### Periodic Sync

 Configure `syncIntervalSeconds` when creating the client to run automatic synchronization on an interval:

```
final dir = await getApplicationCacheDirectory();
final path = '${dir.path}/local.db';

final client = LibsqlClient(path)
	..authToken = '<TOKEN>'
	..syncUrl = '<TURSO_OR_LIBSQL_URL>'
	..syncIntervalSeconds = 5
	..readYourWrites = true;

```

### Encryption at rest

 Pass `encryptionKey` to enable encryption for a SQLite file:

```
final dir = await getApplicationCacheDirectory();
final path = '${dir.path}/local.db';

final client = LibsqlClient(path)..encryptionKey = '<KEY>';

```

 Encrypted databases look like raw data and cannot be opened as standard SQLite databases. Use the libSQL client for operations. Details: [learn more](https://docs.turso.tech/libsql#encryption-at-rest).

### Execute

 Returns number of rows affected:

```
await client.execute("create table if not exists customers (id integer primary key, name text);");

```

### Query

 The result is `List<Map<String, dynamic>>`; when the operation is not a select query, the list is empty:

```
await client.query("insert into customers(name) values ('John Doe')");

print(await client.query("select * from customers"));

```

### Placeholders

 SQL statements in libSQL may use both positional and named placeholders:

```
final statement = await client
	.prepare("select * from customers where id = ?");

await statement.query(positional: [1])

```

 For named placeholders, libSQL accepts the same prefix characters as SQLite: `:`, `@` and `$`.

### Batch Transactions

 A batch runs multiple SQL statements in order inside one implicit transaction. The backend commits every change on success; if any statement fails, it rolls everything back and leaves no updates.

```
await client.batch("""insert into customers (name) values ('Jane Doe'); insert into customers (name) values ('Jake Doe');""");

```

### Transaction Modes

| Mode | SQLite command | Description |
| --- | --- | --- |
| `LibsqlTransactionBehavior.immediate` | `BEGIN IMMEDIATE` | The transaction can run statements that read and write data. On a replica, write transactions are sent to the primary instance and cannot run concurrently. |
| `LibsqlTransactionBehavior.readOnly` | `BEGIN TRANSACTION READONLY` | The transaction is limited to statements that read data (select). Replica read transactions are allowed and may run alongside other reads. |
| `LibsqlTransactionBehavior.deferred_` | `BEGIN DEFERRED` | The transaction begins in read mode and switches to write mode when the first write statement runs. That switch can fail while another write transaction is active on the primary. |

### Interactive Transactions

 SQLite interactive transactions keep a group of reads and writes consistent within the transaction's scope. They let you decide when to commit or roll back changes while isolating the work from other clients.
| Method | Description |
| --- | --- |
| `execute()` | Like `execute()`, but scoped to the transaction |
| `query()` | Like `query()`, but scoped to the transaction |
| `commit()` | Commits every write statement in the transaction |
| `rollback()` | Rolls back the entire transaction |

```
final tx = await client.transaction();

await tx
	.execute("update customers set name = 'John Noe' where id = 1");
await tx
	.execute("update customers set name = 'Jane Noe' where id = 2");
print(await tx
	.query("select * from customers where id = ?", positional: [1]));

await tx.commit();

```

 libSQL interactive transactions hold the database write lock until commit or rollback, with a 5-second timeout. On busy or high-latency databases, they may reduce performance.

### ATTACH

 Use the `ATTACH` attachment to attach multiple databases to the current connection:

```
final tx = await client.transaction(behavior: LibsqlTransactionBehavior.readOnly);

await tx.execute("ATTACH "<database-id>" AS attached");

print(await tx.execute("SELECT * FROM attached.customers"));

await tx.commit();

```

 First enable [allow `ATTACH`](https://docs.turso.tech/cli/db/config/attach/allow) and create a token permitted to attach a database. Details: [learn more](https://docs.turso.tech/features/attach-database)
