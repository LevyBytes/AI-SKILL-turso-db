# C statements transactions and resource cleanup

[Context index](c-and-flutter-sdk-workflows.md) · [Setup and preceding context](c-database-connections-and-sync-options.md)

### Read Your Own Writes

 The `not_read_your_writes` field controls whether a connection sees its own writes immediately on following reads. This behavior is **enabled by default**; set `not_read_your_writes` to `true` to turn it off:

```
libsql_database_t db = libsql_database_init((libsql_database_desc_t){
    .path = "local.db",
    .url = "TURSO_DATABASE_URL",
    .auth_token = "TURSO_AUTH_TOKEN",
    .not_read_your_writes = true
});

```

### Simple query

 For simple parameter-free queries, call `libsql_connection_batch`:

```
libsql_batch_t batch = libsql_connection_batch(conn, "CREATE TABLE users (id INTEGER PRIMARY KEY, name TEXT)");

```

 When a query returns rows, call `libsql_connection_prepare` followed by `libsql_statement_query`:

```
libsql_statement_t stmt = libsql_connection_prepare(conn, "SELECT * FROM users");
libsql_rows_t rows = libsql_statement_query(stmt);

```

### Prepared Statements

 Prepare statements with `libsql_connection_prepare`, then run them through `libsql_statement_execute` or `libsql_statement_query`:

```
libsql_statement_t stmt = libsql_connection_prepare(conn, "INSERT INTO users (name) VALUES (?)");
libsql_statement_bind_value(stmt, libsql_text("John Doe", 8));
libsql_execute_t result = libsql_statement_execute(stmt);

```

### Placeholders

 SQL statements can use positional placeholders as well as named placeholders in libSQL:

```
libsql_statement_t stmt = libsql_connection_prepare(conn, "SELECT * FROM users WHERE id = ?");
libsql_statement_bind_value(stmt, libsql_integer(1));

```

### Transactions

 libSQL supports transactions:

```
libsql_transaction_t tx = libsql_connection_transaction(conn);
// Perform operations within the transaction
libsql_transaction_commit(tx); // or libsql_transaction_rollback(tx)

```

### Cleanup

 Release allocated resources after the work is finished:

```
libsql_statement_deinit(stmt);
libsql_connection_deinit(conn);
libsql_database_deinit(db);

```
