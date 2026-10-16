# Python remote libSQL quickstart

[Context index](python-and-ruby-sdk-workflows.md)

### Remote Access (Over-the-Wire)

 When an application must query a Turso Cloud database directly across the network, such as from a web server or serverless function, use the `libsql` package. It connects by HTTP and does not require a local database file. For most applications, Turso recommends a local database with [Turso Sync](https://docs.turso.tech/sync/usage) (`turso.sync`) because reads are faster, offline operation is available, and latency is lower. Direct remote access fits environments that cannot keep a database file locally, including stateless serverless deployments.

### Step 1: Retrieve database credentials

You need an existing database before continuing. If one is not available, [create one](https://docs.turso.tech/quickstart). Then retrieve the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the credential values into the environment variables defined in `.env`.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

Store these values in environment variables.

### Step 2: Install

```
uv add libsql

```

Or with pip:

```
pip install libsql

```

### Step 3: Connect and query

```
import os
import libsql

conn = libsql.connect(
    database=os.environ["TURSO_DATABASE_URL"],
    auth_token=os.environ["TURSO_AUTH_TOKEN"],
)

conn.execute("CREATE TABLE IF NOT EXISTS users (id INTEGER PRIMARY KEY, name TEXT)")
conn.execute("INSERT INTO users (name) VALUES (?)", ("Alice",))
conn.commit()

rows = conn.execute("SELECT * FROM users").fetchall()
print(rows)

```
