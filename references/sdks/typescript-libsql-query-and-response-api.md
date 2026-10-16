# TypeScript libSQL query and response API

[Context index](typescript-package-and-api-selection.md) · [Setup and preceding context](typescript-libsql-connections-and-replica-sync.md) · [Continue](typescript-libsql-transactions-and-attach.md)

### Encryption

 For new builds that need local encryption, prefer `@tursodatabase/database`; it runs on the Turso Database engine and adds stronger performance plus concurrent write support. TypeScript

```
import { createClient } from "@libsql/client";

const db = createClient({
  url: "file:encrypted.db",
  encryptionKey: process.env.ENCRYPTION_KEY,
});

```

 An encrypted database looks like raw bytes and is not readable as a normal SQLite database. All access must go through the libSQL client; [learn more](https://docs.turso.tech/libsql#encryption-at-rest).

### Concurrency

 Unless configured differently, the client allows as many as `20` requests to run concurrently:

```
import { createClient } from "@libsql/client";

const client = createClient({
  url: process.env.TURSO_DATABASE_URL!,
  authToken: process.env.TURSO_AUTH_TOKEN,
  concurrency: 10,
});

```

### Response

 The methods that follow each resolve to a `Promise<ResultSet>`:

| Property | Type | Description |
| --- | --- | --- |
| `rows` | `Array<Row>` | Array of Row objects with row values; empty when the operation is a write |
| `columns` | `Array<string>` | Array of strings naming the columns in the same order as each Row; empty when the operation is a write |
| `rowsAffected` | `number` | The count of rows changed by a write statement, or `0` for non-write results |
| `lastInsertRowid` | `bigint \| undefined` | New row ID when a row was inserted; otherwise `undefined` |

### Simple query

 Pass either a string or an object to `execute()` when running a SQL statement:

```
const result = await client.execute("SELECT * FROM users");

```

### Placeholders

 libSQL SQL statements may use both positional placeholders and named placeholders:

```
const result = await client.execute({
  sql: "SELECT * FROM users WHERE id = ?",
  args: [1],
});

const batchResult = await client.batch(
  [
    {
      sql: "INSERT INTO users (name) VALUES (?)",
      args: ["Iku"],
    },
  ],
  "write",
);

```

 For named placeholders, libSQL accepts the same prefix characters SQLite does: `:`, `@` and `$`.

### Transaction Modes

| Mode | SQLite command | Description |
| --- | --- | --- |
| `write` | `BEGIN IMMEDIATE` | This mode permits statements that read and write data. If the transaction writes on a replica, it is sent to the primary instance and cannot run concurrently. |
| `read` | `BEGIN TRANSACTION READONLY` | This mode permits only read statements (select). Read transactions may run on replicas and can proceed in parallel with other read transactions. |
| `deferred` | `BEGIN DEFERRED` | The transaction begins as read-only and switches to write mode when the first write statement runs. That transition can fail when the primary already has an active write transaction. |
