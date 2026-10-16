# Flutter drift connection and row inserts

[Context index](c-and-flutter-sdk-workflows.md) · [Setup and preceding context](flutter-drift-schema-and-code-generation.md)

### Step 6: Create the db

```
final db = AppDatabase(DriftLibsqlDatabase(
    "${dir.path}/replica.db",
    syncUrl: url,
    authToken: token,
    readYourWrites: true,
    syncIntervalSeconds: 3,
  ));

```

### Step 7: Perform SQL operations

```
await db.into(db.taskTable).insert(TaskTableCompanion.insert(
	title: task.title,
	description: task.description,
	completed: task.completed));

```
