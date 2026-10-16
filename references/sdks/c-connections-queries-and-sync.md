# C connections queries and sync

[Context index](c-and-flutter-sdk-workflows.md) · [Setup and preceding context](c-installation-credentials-and-example-routes.md)

### Step 3: Connect

Initialize libSQL first, create a `libsql_database_t`, then open a `libsql_connection_t` for it:

Embedded Replicas

```
#include "libsql.h"

libsql_setup((libsql_config_t){0});

libsql_database_t db = libsql_database_init((libsql_database_desc_t){
    .path = "local.db",
    .url = "TURSO_DATABASE_URL",
    .auth_token = "TURSO_AUTH_TOKEN",
    .sync_interval = 300
});

libsql_connection_t conn = libsql_database_connect(db);

```

Local only

```
#include "libsql.h"

libsql_setup((libsql_config_t){0});

libsql_database_t db = libsql_database_init((libsql_database_desc_t){
    .path = "local.db"
});

libsql_connection_t conn = libsql_database_connect(db);

```

Remote only

```
#include "libsql.h"

libsql_setup((libsql_config_t){0});

libsql_database_t db = libsql_database_init((libsql_database_desc_t){
    .url = "TURSO_DATABASE_URL",
    .auth_token = "TURSO_AUTH_TOKEN"
});

libsql_connection_t conn = libsql_database_connect(db);

```

### Step 4: Execute

To run a SQL query on your database, prepare the statement first and execute it afterward:

```
libsql_statement_t stmt = libsql_connection_prepare(conn, "SELECT * FROM users");
libsql_rows_t rows = libsql_statement_query(stmt);

```

Use placeholders when values need to be bound at execution time:

```
libsql_statement_t stmt = libsql_connection_prepare(conn, "SELECT * FROM users WHERE id = ?");
libsql_statement_bind_value(stmt, libsql_integer(1));
libsql_rows_t rows = libsql_statement_query(stmt);

```

### Step 5: Sync (Embedded Replicas only)

For embedded replicas, call `libsql_database_sync()` on the database to bring the local copy up to date with the primary, except when `sync_interval` handles it automatically. Calling `sync` while `sync_interval` is enabled is still allowed:

```
libsql_sync_t sync = libsql_database_sync(db);
if (sync.err) {
    fprintf(stderr, "Error syncing database: %s\n", libsql_error_message(sync.err));
} else {
    printf("Sync completed. Frame number: %llu, Frames synced: %llu\n",
            (unsigned long long)sync.frame_no,
            (unsigned long long)sync.frames_synced);
}

```
