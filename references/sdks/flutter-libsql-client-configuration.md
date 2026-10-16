# Flutter libSQL client configuration

[Context index](c-and-flutter-sdk-workflows.md) · [Continue](flutter-libsql-sync-queries-and-batches.md)

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
