# C database connections and sync options

[Context index](c-and-flutter-sdk-workflows.md) · [Continue](c-statements-transactions-and-resource-cleanup.md)

## Installing

The C bindings remain in technical preview. Report issues in [Discord](https://discord.gg/turso).

### Installing

 Projects using the libSQL C bindings must include `libsql.h` and link the `liblibsql.so` shared library.

### libSQL C

Build from source code. Before working with libSQL, call the setup function:

```
libsql_setup((libsql_config_t){0});

```

### In-Memory Databases

 For non-persistent use cases, libSQL can connect to [in-memory databases](https://www.sqlite.org/inmemorydb.html):

```
libsql_database_t db = libsql_database_init((libsql_database_desc_t){.path = ":memory:"});
libsql_connection_t conn = libsql_database_connect(db);

```

### Local Development

 Local development can use an SQLite file:

```
libsql_database_t db = libsql_database_init((libsql_database_desc_t){.path = "local.db"});
libsql_connection_t conn = libsql_database_connect(db);

```

### Remote Databases

 A remote database connection uses a URL together with an auth token:

```
libsql_database_t db = libsql_database_init((libsql_database_desc_t){
    .url = "TURSO_DATABASE_URL",
    .auth_token = "TURSO_AUTH_TOKEN"
});
libsql_connection_t conn = libsql_database_connect(db);

```

### Embedded Replicas

 Embedded replicas can sync from the remote URL while sending writes to the remote primary database:

```
libsql_database_t db = libsql_database_init((libsql_database_desc_t){
    .path = "local.db",
    .url = "TURSO_DATABASE_URL",
    .auth_token = "TURSO_AUTH_TOKEN"
});
libsql_connection_t conn = libsql_database_connect(db);

```

### Manual Sync

 Use `libsql_database_sync` to manually synchronize the local database with its remote counterpart:

```
libsql_sync_t sync = libsql_database_sync(db);

```

### Sync Interval

 Set `sync_interval` in the database description to run background synchronization on a schedule:

```
libsql_database_t db = libsql_database_init((libsql_database_desc_t){
    .path = "local.db",
    .url = "TURSO_DATABASE_URL",
    .auth_token = "TURSO_AUTH_TOKEN",
    .sync_interval = 60000
});

```
