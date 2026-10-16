# Vercel database lifecycle and sync API

[Context index](client-connection-and-vercel-workflows.md) · [Setup and preceding context](vercel-in-function-sqlite-setup.md)

### API Reference

 **`createDb(name, options?)`** Creates or returns a database instance. If that database does not exist, it is created automatically inside the configured group (`TURSO_GROUP`). Calling `createDb()` again with the same name during one function invocation returns the existing instance.

```
const db = await createDb(process.env.TURSO_DATABASE!, {
  remoteWrites: false,     // Batch writes locally and push on close() (optional, default: true)
});

```

 When `remoteWrites` is `true` (the default), write statements (`INSERT`, `UPDATE`, `DELETE`) go directly to the remote Turso database for immediate durability. When it is `false`, writes are applied to the local SQLite copy and then pushed as one batch when `db.push()` or `db.close()` runs. That mode can help with bulk ingestion or background work where eventual visibility is acceptable. **`db.query(sql, params?)`** Execute a SELECT query and return results.

```
const result = await db.query("SELECT * FROM users WHERE id = ?", [1]);

console.log(result.columns); // ["id", "name", "email"]
console.log(result.rows);    // [[1, "Alice", "alice@example.com"]]

```

 **`db.execute(sql, params?)`** Execute an INSERT, UPDATE, DELETE, or DDL statement. By default, writes go straight to the remote Turso database. If `remoteWrites: false` is configured, writes are made locally and pushed on `close()`.

```
await db.execute("UPDATE users SET name = ? WHERE id = ?", ["Bob", 1]);

```

 **`db.close()`** Close the connection. If `remoteWrites: false` is configured, this also sends any pending local writes to the remote Turso database. Always call it from a `finally` block. As a fallback, the package registers with Vercel's [`waitUntil()`](https://vercel.com/docs/functions/functions-api-reference#waituntil) API so pending changes are pushed if `close()` is not called within 5 seconds after the function completes.

```
const db = await createDb(process.env.TURSO_DATABASE!);
try {
  await db.execute("INSERT INTO users (name) VALUES (?)", ["Charlie"]);
} finally {
  await db.close();
}

```

 **`db.push()`** Push local changes to the remote Turso database manually without closing the connection. This applies only when `remoteWrites: false` is in use.

```
await db.execute("INSERT INTO users (name) VALUES (?)", ["Charlie"]);
await db.push(); // Sync without closing

```

 **`db.pull()`** Fetch the latest changes from the remote Turso database.

```
await db.pull();

```
