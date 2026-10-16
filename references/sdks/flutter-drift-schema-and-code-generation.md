# Flutter drift schema and code generation

[Context index](c-and-flutter-sdk-workflows.md) · [Continue](flutter-drift-connection-and-row-inserts.md)

## Prerequisites

 Before beginning, confirm that you have completed these steps:
- [Install Turso CLI](https://docs.turso.tech/cli/installation)
- [Sign up for Turso or log in](https://docs.turso.tech/cli/authentication#signup)
### Step 1: Add packages to your project

```
flutter pub add drift_libsql drift drift_flutter dev:build_runner dev:drift_dev

```

### Step 2: Retrieve database credentials

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

### Step 3: Create a table schema

```
class TaskTable extends Table {
  IntColumn get id => integer().autoIncrement()();
  TextColumn get title => text()();
  TextColumn get description => text()();
  BoolColumn get completed => boolean()();
}

```

### Step 4: Create db class

```
@DriftDatabase(tables: [TaskTable])
class AppDatabase extends _$AppDatabase {
  AppDatabase(super.e);

  @override
  int get schemaVersion => 1;
}

```

### Step 5: Run build runner

```
dart run build_runner build

```
