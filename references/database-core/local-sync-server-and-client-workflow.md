# Local sync server and client workflow

[Context index](database-sync-workflows.md)

## Starting the server

The `tursodb` CLI provides an integrated sync server for local use. You can build and test sync flows on your own machine without a Turso Cloud account. Because the local server speaks the same sync protocol as Turso Cloud, application code can target either environment the same way.

### Starting the server

 Start the sync server by passing `--sync-server` and an address. The database argument tells the server where to keep its data:

```
tursodb ./server.db --sync-server 0.0.0.0:8080

```

 This launches a sync server on port `8080` using `./server.db` as its backing database.

### Connecting clients

 Configure the sync client to use `http://localhost:8080`. The local server does not require an auth token.

```
import { connect } from '@tursodatabase/sync';

const db = await connect({
  path: './local-replica.db',
  url: 'http://localhost:8080',
});

```

 After connecting, the sync operations (`push`, `pull`, `checkpoint`, `stats`) behave as covered in the [Usage](https://docs.turso.tech/sync/usage) guide.

### Example: full local sync workflow

 This sample shows an end-to-end local workflow: launch the server, write data with one client, then sync it to another.

### Step 1: Start the sync server

In a terminal, start the server:

```
tursodb ./server.db --sync-server 0.0.0.0:8080

```

### Step 2: Write from Client A, then push

```
import { connect } from '@tursodatabase/sync';

const clientA = await connect({
  path: './client-a.db',
  url: 'http://localhost:8080',
});

await clientA.exec("CREATE TABLE IF NOT EXISTS notes (id TEXT PRIMARY KEY, body TEXT)");
await clientA.exec("INSERT INTO notes VALUES ('n1', 'hello from client A')");

await clientA.push();

```

### Step 3: Pull from Client B

```
import { connect } from '@tursodatabase/sync';

const clientB = await connect({
  path: './client-b.db',
  url: 'http://localhost:8080',
});

const changed = await clientB.pull();
console.log('changes pulled:', changed);

const rows = await clientB.query("SELECT * FROM notes");
console.log(rows);
// [{ id: 'n1', body: 'hello from client A' }]

```
