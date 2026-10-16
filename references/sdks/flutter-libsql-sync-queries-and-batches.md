# Flutter libSQL sync queries and batches

[Context index](c-and-flutter-sdk-workflows.md) · [Setup and preceding context](flutter-libsql-client-configuration.md) · [Continue](flutter-libsql-transactions-and-attach.md)

### Periodic Sync

 Configure `syncIntervalSeconds` when creating the client to run automatic synchronization on an interval:

```
final dir = await getApplicationCacheDirectory();
final path = '${dir.path}/local.db';

final client = LibsqlClient(path)
	..authToken = '<TOKEN>'
	..syncUrl = '<TURSO_OR_LIBSQL_URL>'
	..syncIntervalSeconds = 5
	..readYourWrites = true;

```

### Encryption at rest

 Pass `encryptionKey` to enable encryption for a SQLite file:

```
final dir = await getApplicationCacheDirectory();
final path = '${dir.path}/local.db';

final client = LibsqlClient(path)..encryptionKey = '<KEY>';

```

 Encrypted databases look like raw data and cannot be opened as standard SQLite databases. Use the libSQL client for operations. Details: [learn more](https://docs.turso.tech/libsql#encryption-at-rest).

### Execute

 Returns number of rows affected:

```
await client.execute("create table if not exists customers (id integer primary key, name text);");

```

### Query

 The result is `List<Map<String, dynamic>>`; when the operation is not a select query, the list is empty:

```
await client.query("insert into customers(name) values ('John Doe')");

print(await client.query("select * from customers"));

```

### Placeholders

 SQL statements in libSQL may use both positional and named placeholders:

```
final statement = await client
	.prepare("select * from customers where id = ?");

await statement.query(positional: [1]);

```

 For named placeholders, libSQL accepts the same prefix characters as SQLite: `:`, `@` and `$`.

### Batch Transactions

 A batch runs multiple SQL statements in order inside one implicit transaction. The backend commits every change on success; if any statement fails, it rolls everything back and leaves no updates.

```
await client.batch("""insert into customers (name) values ('Jane Doe'); insert into customers (name) values ('Jake Doe');""");

```

### Transaction Modes

| Mode | SQLite command | Description |
| --- | --- | --- |
| `LibsqlTransactionBehavior.immediate` | `BEGIN IMMEDIATE` | The transaction can run statements that read and write data. On a replica, write transactions are sent to the primary instance and cannot run concurrently. |
| `LibsqlTransactionBehavior.readOnly` | `BEGIN TRANSACTION READONLY` | The transaction is limited to statements that read data (select). Replica read transactions are allowed and may run alongside other reads. |
| `LibsqlTransactionBehavior.deferred_` | `BEGIN DEFERRED` | The transaction begins in read mode and switches to write mode when the first write statement runs. That switch can fail while another write transaction is active on the primary. |
