# TypeScript attach read transaction

Enable ATTACH on both databases and use a token authorized for the target database. They must share a group. ATTACH is unavailable to new users under the documented platform restriction and does not support embedded replicas; check [its availability](https://docs.turso.tech/features/attach-database).

```typescript
import { createClient, type Transaction } from "@libsql/client";

const client = createClient({
  url: process.env.TURSO_DATABASE_URL!,
  authToken: process.env.TURSO_AUTH_TOKEN,
});
let transaction: Transaction | undefined;
try {
  transaction = await client.transaction("read");
  await transaction.execute('ATTACH "<database-id>" AS attached');
  const result = await transaction.execute("SELECT * FROM attached.users");
  console.log(result.rows);
  await transaction.commit();
} catch (error) {
  if (transaction && !transaction.closed) await transaction.rollback();
  throw error;
} finally {
  try {
    transaction?.close();
  } finally {
    client.close();
  }
}
```

Replace `<database-id>` with the target's documented database ID. This example owns the client and closes it even when opening the transaction fails.
