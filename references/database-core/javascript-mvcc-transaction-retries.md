# JavaScript MVCC transaction retries

[Context index](embedded-database-mvcc-and-encryption.md) · [Setup and preceding context](mvcc-setup-and-conflict-handling.md) · [Previous phase](rust-mvcc-transaction-retries.md)

Install `@tursodatabase/database` and run this as an ES module with Node.js. Each writer has its own connection. Conflicts and busy errors retry up to 100 times; other failures propagate after all writers finish.

```javascript
import { connect } from "@tursodatabase/database";
import { mkdtempSync, rmSync } from "node:fs";
import { tmpdir } from "node:os";
import { join } from "node:path";

const directory = mkdtempSync(join(tmpdir(), "turso-mvcc-"));
const DB_PATH = join(directory, "hits.db");

function isRetryable(error) {
  const message = (error?.message ?? "").toLowerCase();
  return message.includes("conflict") || message.includes("busy");
}

async function writeWorker() {
  const value = Math.floor(Math.random() * 100) + 1;
  const db = await connect(DB_PATH);
  try {
    await db.exec("PRAGMA journal_mode = 'mvcc'");
    for (let attempt = 0; attempt < 100; attempt++) {
      try {
        await db.exec("BEGIN CONCURRENT");
        await db.exec(`INSERT INTO hits VALUES (${value})`);
        await db.exec("COMMIT");
        return value;
      } catch (error) {
        try { await db.exec("ROLLBACK"); } catch (_) {}
        if (!isRetryable(error) || attempt === 99) throw error;
        await new Promise(resolve => setImmediate(resolve));
      }
    }
  } finally {
    await db.close();
  }
}

try {
  const setup = await connect(DB_PATH);
  try {
    await setup.exec("PRAGMA journal_mode = 'mvcc'");
    await setup.exec("CREATE TABLE hits (val INTEGER)");
  } finally {
    await setup.close();
  }
  const results = await Promise.allSettled(
    Array.from({ length: 16 }, () => writeWorker())
  );
  const failure = results.find(result => result.status === "rejected");
  if (failure) throw failure.reason;
  const final = await connect(DB_PATH);
  try {
    await final.exec("PRAGMA journal_mode = 'mvcc'");
    const statement = await final.prepare("SELECT COUNT(*) FROM hits");
    const row = await statement.get();
    console.log(`total rows: ${Object.values(row)[0]}`);
  } finally {
    await final.close();
  }
} finally {
  rmSync(directory, { recursive: true, force: true });
}
```

Error-message matching follows the upstream example; prefer typed error codes where the installed binding exposes them. The generated temporary directory belongs solely to this example.
