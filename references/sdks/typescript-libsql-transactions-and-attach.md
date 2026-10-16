# TypeScript libSQL transactions and attach

[Context index](typescript-package-and-api-selection.md) · [Setup and preceding context](typescript-libsql-connections-and-replica-sync.md) · [Previous phase](typescript-libsql-query-and-response-api.md)

### Batch Transactions

 A batch is a sequence of SQL statements run in order inside an implicit transaction. The backend manages the transaction boundary: when everything succeeds, all changes commit; if anything fails, the whole batch rolls back and leaves no changes.

```
const result = await client.batch(
  [
    {
      sql: "INSERT INTO users (name) VALUES (?)",
      args: ["Iku"],
    },
    {
      sql: "INSERT INTO users (name) VALUES (?)",
      args: ["Iku 2"],
    },
  ],
  "write",
);

```

### Interactive Transactions

Interactive transactions expose `execute`, `commit`, `rollback` and `close`. A write transaction holds the write lock until it finishes and has a five-second timeout; long network round trips can reduce throughput.

- Open when conditionally reading and updating a balance: [transaction example](typescript-conditional-write-transaction.md).
- Open when attaching an enabled database inside a read transaction: [ATTACH example](typescript-attach-read-transaction.md).

The batch above assumes a `users(name TEXT)` column. Every example requires its connection and schema prerequisites; the caller owns a shared client's lifetime.
