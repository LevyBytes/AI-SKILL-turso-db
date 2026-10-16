# SQLite migration wal preparation

[Context index](access-durability-encryption-and-migration.md) · [Continue](sqlite-import-and-platform-authentication.md)

## Preparing to Migrate

This guide explains how to move an existing SQLite database to Turso Cloud. You can use either the Turso CLI or the Platform API. (Figure: Migrate to Turso)

### Preparing to Migrate

 Before importing a SQLite database into Turso, verify that it meets Turso requirements. In particular, the SQLite database should use WAL (Write-Ahead Logging) mode.

### Step 1: Open your SQLite database

Open the database with the SQLite command-line tool or another SQLite client:

```
sqlite3 path/to/your/database.db

```

### Step 2: Set WAL journal mode

Use this command to change the database into WAL mode:

```
PRAGMA journal_mode='wal';

```

A `wal` result confirms that the change succeeded.

### Step 3: Checkpoint and truncate the WAL file

Run a checkpoint so pending changes reach the main database file, then truncate the WAL file:

```
PRAGMA wal_checkpoint(truncate);

```

### Step 4: Verify the journal mode

Verify that the database is currently in WAL mode:

```
PRAGMA journal_mode;

```

This should return `wal`.

### Step 5: Close the database

Exit the SQLite shell:

```
.exit

```

The database is ready to migrate to Turso.
