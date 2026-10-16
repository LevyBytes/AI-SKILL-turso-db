# Dart installation connection and queries

[Context index](client-connection-and-vercel-workflows.md) · [Continue](dart-prepared-statements.md)

## Dart

### Step 1: Install

Add the turso_dart dependency entry to your `pubspec.yaml` file:

```
dependencies:
  turso_dart:

```

Then run:

```
dart pub get

```

### Step 2: Connect

Create a client connection. The target can be an in-memory database or a local database file:

```
import 'package:turso_dart/turso_dart.dart';

// In memory
final client = TursoClient.memory();

// For a local file, replace the preceding declaration with:
// final client = TursoClient.local('/path/to/local.db');

await client.connect();

```

### Step 3: Create table

Create a table for customers:

```
await client.execute(
  "CREATE TABLE IF NOT EXISTS customers (id INTEGER PRIMARY KEY, name TEXT)"
);

```

### Step 4: Insert data

Write a few rows into the customers table:

```
await client.query("INSERT INTO customers(name) VALUES ('John Doe')");
await client.query("INSERT INTO customers(name) VALUES ('Jane Smith')");

```

### Step 5: Query data

Read all rows from the customers table:

```
final result = await client.query("SELECT * FROM customers");
print(result);

```
