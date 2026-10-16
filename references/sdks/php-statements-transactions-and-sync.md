# PHP statements transactions and sync

[Context index](kotlin-and-php-sdk-workflows.md) · [Setup and preceding context](php-client-configuration-and-sync-options.md)

### Manual Sync

 The `sync` function lets you manually synchronize the local database with the remote counterpart:

```
$db->sync();

```

### Read Your Own Writes

 The `readYourWrites` parameter makes writes immediately visible to later read operations from the same connection. It is **enabled by default** and is especially useful in distributed systems for preserving consistency from the writer's perspective. Pass `false` to disable it:

```
$db = new Database(
    path: 'test.db',
    url: getenv('TURSO_DATABASE_URL'),
    authToken: getenv('TURSO_AUTH_TOKEN'),
    readYourWrites: false,
);

```

### Simple Query

 Get a connection from a database, then call `query()` to run a SQL statement with optional arguments:

```
$conn = $db->connect();
$rows = $conn->query("SELECT * FROM users");

```

### Prepared Statements

 Create a cached statement with `prepare()`, bind parameters, and query it afterward:

```
$stmt = $conn->prepare("SELECT * FROM users where id = ?");
$rows = $stmt->bind([1])->query();

```

### Placeholders

 libSQL allows both positional and named placeholders in SQL statements:

```
$conn->query("SELECT * FROM users WHERE id = ?", [1]);

```

### Batch Transactions

 `executeBatch` runs a SQL batch. When all-or-nothing behavior is required, use an explicit transaction and execute the batch through that transaction; do not infer atomicity merely from multiple statements in one string.

```
$conn->executeBatch("
  CREATE TABLE IF NOT EXISTS users (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL
  );

  INSERT INTO users (name) VALUES ('Alice');
  INSERT INTO users (name) VALUES ('Bob');
");

```

### Interactive Transactions

 Interactive transactions in SQLite preserve consistency across a sequence of read and write operations within the transaction's scope. They let you choose when to commit or roll back changes while isolating that work from other client activity.

```
$tx = $conn->transaction();
try {
    $tx->execute("INSERT INTO users (name) VALUES (?1)", ["Iku"]);
    $tx->execute("INSERT INTO users (name) VALUES (?1)", ["Iku 2"]);
    $tx->commit();
} catch (\Throwable $error) {
    $tx->rollback();
    throw $error;
}

```
