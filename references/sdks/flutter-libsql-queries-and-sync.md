# Flutter libSQL queries and sync

[Context index](c-and-flutter-sdk-workflows.md) · [Setup and preceding context](flutter-libsql-installation-and-connections.md)

### Step 5: Execute

```
await client.execute("create table if not exists customers (id integer primary key, name text);");

```

Placeholders can be used when values need binding:

```
final statement = await client
  .prepare("select * from customers where id = ?");

await statement.query(positional: [1]);

```

### Step 6: Sync (Embedded Replicas only)

For embedded replicas, call `sync()` on the connector so the local database syncs with the primary database.

```
await client.sync();

```
