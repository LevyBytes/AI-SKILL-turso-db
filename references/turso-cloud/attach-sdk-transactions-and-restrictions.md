# Attach SDK transactions and restrictions

[Context index](vectors-attach-branches-and-edge-replicas.md) · [Setup and preceding context](attach-permissions-and-cli-workflow.md)

### libSQL SDKs

 Use a libSQL client SDK through [TypeScript](https://docs.turso.tech/sdk/ts), [Rust](https://docs.turso.tech/sdk/rust), [Go](https://docs.turso.tech/sdk/go), [Python](https://docs.turso.tech/sdk/python), or [HTTP](https://docs.turso.tech/sdk/http).

### Step 1: Enable attach on required databases

First enable `ATTACH` on the database(s) you intend to attach:

```
turso db config attach allow <database-name>

```

### Step 2: Retrieve Database ID

Next retrieve the **Database ID** for the database you intend to `ATTACH`:

```
turso db show <database-name>

```

### Step 3: Create token with ATTACH permissions

Then create a libSQL client token with `attach` permission for the database you want to attach:

```
turso db tokens create <database-name> --attach <another-database-name>

```

### Step 4: Connect to any database with ATTACH

Attach the desired database inside a read transaction using a [Client SDK](https://docs.turso.tech/sdk):

```
import { createClient } from "@libsql/client";

const client = createClient({
  url: "libsql://your-database.turso.io",
  authToken: "...",
});

let txn;
try {
  txn = await client.transaction("read");
  await txn.execute('ATTACH "<database-id>" AS my_db');
  const rs = await txn.execute("SELECT * FROM my_db.my_table");
  console.log(rs.rows);
  await txn.commit();
} catch (error) {
  if (txn && !txn.closed) {
    try {
      await txn.rollback();
    } catch (rollbackError) {
      throw new AggregateError([error, rollbackError], "Read and rollback failed");
    }
  }
  throw error;
} finally {
  try {
    txn?.close();
  } finally {
    client.close();
  }
}

```

### Things to know

- Only databases with the `attach` feature enabled can be attached.
- The attached databases must belong to a group and share that same group.
- A single transaction can attach at most 10 databases.
- Attached databases are read only.
- Use `ATTACH` statements only inside transactions.
- `ATTACH` doesn’t support [Embedded Replicas](https://docs.turso.tech/features/embedded-replicas)
