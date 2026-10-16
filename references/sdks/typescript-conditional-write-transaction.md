# TypeScript conditional write transaction

Use an existing `@libsql/client` connection with `intMode: "number"` and a table `accounts(userId TEXT PRIMARY KEY, balance INTEGER)`. Store amounts in integral smallest currency units that fit JavaScript's safe-integer range. The caller owns the client; this function owns only its transaction.

```typescript
import type { Client } from "@libsql/client";

export async function withdraw(client: Client, userId: string, amount: number) {
  if (!Number.isSafeInteger(amount) || amount <= 0) {
    throw new RangeError("Amount must be a positive safe integer");
  }
  const transaction = await client.transaction("write");
  try {
    const result = await transaction.execute({
      sql: "SELECT balance FROM accounts WHERE userId = ?",
      args: [userId],
    });
    if (result.rows.length !== 1) throw new Error("Account not found");
    const balance = result.rows[0].balance;
    if (typeof balance !== "number" || !Number.isSafeInteger(balance)) {
      throw new TypeError("Balance must be a safe integer");
    }
    if (balance < amount) {
      await transaction.rollback();
      return false;
    }
    await transaction.execute({
      sql: "UPDATE accounts SET balance = balance - ? WHERE userId = ?",
      args: [amount, userId],
    });
    await transaction.commit();
    return true;
  } catch (error) {
    if (!transaction.closed) await transaction.rollback();
    throw error;
  } finally {
    transaction.close();
  }
}
```

`false` means insufficient funds; missing accounts and storage failures reject the promise. The function commits only after the conditional write succeeds.
