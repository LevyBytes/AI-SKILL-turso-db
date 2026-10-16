# Python pyturso local and sync quickstart

[Context index](python-and-ruby-sdk-workflows.md)

## Recommended: pyturso (Local / Embedded)

This Python quickstart covers these tasks:
- Install the Turso package
- Open a connection to a local or remote database
- Execute a query using SQL
- Sync changes to local database

### Recommended: pyturso (Local / Embedded)

 `pyturso` is the preferred package for local and embedded scenarios. It uses the Turso Database engine, a fresh SQLite-compatible implementation with concurrent writes (MVCC) and async I/O. Its interface mirrors Python's standard `sqlite3` API closely enough to act as a drop-in substitute.

### Step 1: Install

```
uv add pyturso

```

Or with pip:

```
pip install pyturso

```

### Step 2: Connect

```
import turso

db = turso.connect("app.db")

```

You can also open in-memory databases:

```
db = turso.connect(":memory:")

```

### Step 3: Execute

```
db.execute("CREATE TABLE IF NOT EXISTS users (id INTEGER PRIMARY KEY, name TEXT)")
db.execute("INSERT INTO users (name) VALUES (?)", ("Alice",))
db.commit()

for row in db.execute("SELECT * FROM users"):
    print(row)

```

### Step 4: Sync (push and pull)

To synchronize a local database with Turso Cloud, use `turso.sync`:

```
import os
import turso.sync

db = turso.sync.connect(
    "app.db",
    remote_url=os.environ["TURSO_DATABASE_URL"],
    auth_token=os.environ["TURSO_AUTH_TOKEN"],
)

db.execute("INSERT INTO users (name) VALUES (?)", ("Bob",))
db.commit()

# Push local writes to Turso Cloud
db.push()

# Pull remote changes to local database
db.pull()

```

Reads and writes use the local database file, so operations stay fast and can work offline. `push()` uploads local changes, while `pull()` retrieves remote changes. See [Turso Sync](https://docs.turso.tech/sync/usage) for conflict resolution, checkpointing, and related details.
