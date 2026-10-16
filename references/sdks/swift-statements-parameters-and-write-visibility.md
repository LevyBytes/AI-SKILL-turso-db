# Swift statements parameters and write visibility

[Context index](swift-sdk-workflows.md) · [Setup and preceding context](swift-client-configuration-and-sync-options.md)

### Read Your Own Writes

 The `readYourWrites` parameter makes writes immediately visible to later reads from the same connection. This behavior is **enabled by default**, which helps preserve writer-side consistency in distributed systems. Pass `false` to disable it:

```
import Libsql

let db = try Database(
    path: "./local.db",
    url: "libsql://your-database.turso.io",
    authToken: "replace-with-your-token",
    readYourWrites: false
)

let conn = try db.connect()

```

### Simple query

 Pass a string to `query()` to run a SQL statement, with optional arguments when needed:

```
try conn.execute("INSERT INTO users VALUES (?)", [1])

```

### Prepared Statements

 Use `prepare()` to create a cached statement, then execute it with `query()`:

```
let stmt = try conn.prepare("SELECT * FROM users WHERE id = ?")
try stmt.bind([1])
let rows = try stmt.query()

```

### Placeholders

 libSQL supports positional and named placeholders inside SQL statements:

```
try conn.query("SELECT * FROM users WHERE id = ?", [1])

```
