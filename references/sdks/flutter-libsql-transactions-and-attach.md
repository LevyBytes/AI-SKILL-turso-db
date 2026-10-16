# Flutter libSQL transactions and attach

[Context index](c-and-flutter-sdk-workflows.md) · [Setup and preceding context](flutter-libsql-client-configuration.md) · [Previous phase](flutter-libsql-sync-queries-and-batches.md)

### Interactive Transactions

 SQLite interactive transactions keep a group of reads and writes consistent within the transaction's scope. They let you decide when to commit or roll back changes while isolating the work from other clients.

| Method | Description |
| --- | --- |
| `execute()` | Like `execute()`, but scoped to the transaction |
| `query()` | Like `query()`, but scoped to the transaction |
| `commit()` | Commits every write statement in the transaction |
| `rollback()` | Rolls back the entire transaction |

```
final tx = await client.transaction();

await tx
	.execute("update customers set name = 'John Noe' where id = 1");
await tx
	.execute("update customers set name = 'Jane Noe' where id = 2");
print(await tx
	.query("select * from customers where id = ?", positional: [1]));

await tx.commit();

```

 libSQL interactive transactions hold the database write lock until commit or rollback, with a 5-second timeout. On busy or high-latency databases, they may reduce performance.

### ATTACH

 Use the `ATTACH` attachment to attach multiple databases to the current connection:

```
final tx = await client.transaction(behavior: LibsqlTransactionBehavior.readOnly);

await tx.execute('ATTACH "<database-id>" AS attached');

print(await tx.query("SELECT * FROM attached.customers"));

await tx.commit();

```

 First enable [allow `ATTACH`](https://docs.turso.tech/cli/db/config/attach/allow) and create a token permitted to attach a database. Details: [learn more](https://docs.turso.tech/features/attach-database)
