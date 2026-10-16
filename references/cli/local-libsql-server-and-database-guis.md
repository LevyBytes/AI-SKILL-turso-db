# Local libSQL server and database guis

[Context index](database-and-group-commands.md) · [Setup and preceding context](api-token-list-and-scopes.md) · [Previous phase](local-dumps-and-sqlite-connections.md) · [Continue](group-migration-creation-and-destruction.md)

### Turso CLI

 For [libSQL](https://docs.turso.tech/libsql) features such as [extensions](https://docs.turso.tech/libsql#extensions), use the Turso CLI:

```
turso dev

```

 This starts a local libSQL server and creates a database. Then connect with the `url` option in your SDK:

```
import { createClient } from "@libsql/client";

const client = createClient({
  url: "http://127.0.0.1:8080",
});

```

 Stopping the server discards changes. To keep changes or work from a production dump, pass `--db-file` with the SQLite filename:

```
turso dev --db-file local.db

```

### Turso Database

 If you already have a Turso database, use it for development by passing the `url` option to your SDK. A hosted Turso database can add platform costs and count against quota, so SQLite or Turso CLI is often better for local work.

### Connecting a GUI

 During development, connect a GUI to a SQLite, libSQL, or Turso database with one of these tools:
- [Beekeeper Studio](https://www.beekeeperstudio.io/db/libsql-client/) - Windows, Linux, and macOS.
- [Outerbase](https://www.outerbase.com) - Browser-based.
- [TablePlus](https://tableplus.com) - Linux, macOS, and Windows.
- [Dataflare](https://dataflare.app) - Paid, with a limited free version, for macOS, Windows, and Linux.
- [Outerbase Studio](https://libsqlstudio.com) - Browser-based.
- [DBeaver](https://dbeaver.io) - Linux, macOS, and Windows.
