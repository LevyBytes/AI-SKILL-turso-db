# JavaScript local query results

[Context index](client-connection-and-vercel-workflows.md) · [Setup and preceding context](java-local-build-and-dependency.md)

Use the `db` connection and users table from [JavaScript setup](javascript-local-database-setup.md).

Read all users from the table:

```
const stmt = await db.prepare("SELECT * FROM users");
const users = await stmt.all();
console.log(users);

```

### Examples

 Review these JavaScript examples for additional Turso Database usage patterns:
- [Node](https://github.com/tursodatabase/turso/blob/main/examples/javascript/database-node) - Node.js with a local file database and no sync
- [Wasm + Vite](https://github.com/tursodatabase/turso/blob/main/examples/javascript/database-wasm-vite) - Browser (WASM) using a local database inside the browser
