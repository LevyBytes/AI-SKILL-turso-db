# Python libSQL replicas sync and encryption

[Context index](python-and-ruby-sdk-workflows.md)

### libsql (libSQL)

 The `libsql` package is based on [libSQL](https://github.com/tursodatabase/libsql), the open-source SQLite fork that currently underpins Turso Cloud. It is production-ready and well tested, and it fits existing codebases that already depend on `libsql`. With `libsql` Embedded Replicas, reads use the local replica while writes go to the cloud primary and are then reflected back. Embedded Replicas are fully supported. For new sync-oriented projects, Turso recommends `turso.sync`; see the [quickstart](https://docs.turso.tech/sdk/python/quickstart).

### Embedded Replicas

 For workloads requiring **offline writes**, **bidirectional sync**, or **multi-writer convergence**, Turso recommends `turso.sync`: reads and writes both happen locally, and you synchronize deliberately with `push()` / `pull()`. See the [quickstart](https://docs.turso.tech/sdk/python/quickstart). You can also use [embedded replicas](https://docs.turso.tech/features/embedded-replicas), which sync a remote database into a local SQLite file and send writes to the remote primary database:

```
import os

import libsql

conn = libsql.connect("local.db", sync_url=os.getenv("LIBSQL_URL"),
                      auth_token=os.getenv("LIBSQL_AUTH_TOKEN"))
conn.execute("CREATE TABLE IF NOT EXISTS users (id INTEGER);")
conn.execute("INSERT INTO users(id) VALUES (1);")
conn.commit()

print(conn.execute("select * from users").fetchall())

```

 Embedded Replicas require file-system access.

### Periodic Sync

 Pass seconds to `sync_interval` to synchronize automatically on a schedule. For example, this code syncs once per minute:

```
conn = libsql.connect("local.db", sync_interval=60, sync_url=os.getenv("LIBSQL_URL"),
                      auth_token=os.getenv("LIBSQL_AUTH_TOKEN"))

```

### Manual Sync

 Use the `Sync` function to manually synchronize the local database with its remote counterpart:

```
conn.execute("INSERT INTO users(id) VALUES (2);")
conn.commit()
conn.sync()

```

### Encryption

 For new projects, Turso recommends `pyturso` for local encryption because it uses the Turso Database engine and offers stronger performance plus concurrent-write support. To encrypt a SQLite file, provide the secret through the `encryption_key` option:

```
conn = libsql.connect("encrypted.db", sync_url=os.getenv("LIBSQL_URL"),
                      auth_token=os.getenv("LIBSQL_AUTH_TOKEN"),
                      encryption_key=os.getenv("ENCRYPTION_KEY"))

```

 Encrypted databases look like raw bytes and cannot be opened as normal SQLite databases. Use the libSQL client for operations - [learn more](https://docs.turso.tech/libsql#encryption-at-rest).
