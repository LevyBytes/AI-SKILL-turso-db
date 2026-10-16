# PHP remote queries and sync

[Context index](kotlin-and-php-sdk-workflows.md) · [Setup and preceding context](php-installation-and-replica-connections.md)

Local only

```
use Libsql\Database;

$db = new Database(path: "database.db");

```

Or just:

```
use Libsql\Database;

$db = new Database("database.db");

```

Remote only

```
use Libsql\Database;

$db = new Database(
    url: getenv('TURSO_DATABASE_URL'),
    authToken: getenv('TURSO_AUTH_TOKEN'),
);
$conn = $db->connect();

```

### Step 4: Execute

Run SQL queries against the existing database like this:

```
$createUsers = "
  CREATE TABLE IF NOT EXISTS users (
      id INTEGER PRIMARY KEY AUTOINCREMENT,
      name TEXT,
      age INTEGER
  );
  INSERT INTO users (name, age) VALUES ('Prof. Ir. Onno Widodo Purbo, M.Eng., Ph.D', 61);
  INSERT INTO users (name, age) VALUES ('Jim Geovedi', 41);
  INSERT INTO users (name, age) VALUES ('Nasirun', 59);
";

$conn->executeBatch($createUsers);
$conn->query("SELECT * FROM users")->fetchArray();

```

Use placeholders when values should be supplied separately:

```
$conn->query("SELECT * FROM users WHERE id = ?", [1])->fetchArray();

```

### Step 5: Sync (Embedded Replicas only)

With embedded replicas, call `sync()` on the database type to synchronize the local database with the primary database:

```
<?php

$db->sync();

```
