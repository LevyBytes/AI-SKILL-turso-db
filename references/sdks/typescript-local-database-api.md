# TypeScript local database API

[Context index](typescript-package-and-api-selection.md)

### @tursodatabase/database

 Use this package for local or embedded databases. It runs on the Turso Database engine and provides concurrent writes (MVCC) plus async I/O.

### Installing

```
npm install @tursodatabase/database

```

### Initializing

```
import { connect } from "@tursodatabase/database";

const db = await connect("app.db");

```

 Databases that live entirely in memory are supported too:

```
const db = await connect(":memory:");

```

### Querying

```
const stmt = await db.prepare("SELECT * FROM users");
const users = await stmt.all();

const insert = await db.prepare("INSERT INTO users (username) VALUES (?)");
await insert.run("alice");

```

### Encryption

 To protect local databases at rest, pass the `encryption` option:

```
import { connect } from "@tursodatabase/database";

const db = await connect("encrypted.db", {
  encryption: {
    cipher: "aegis256",
    hexkey: "b1bbfda4f589dc9daaf004fe21111e00dc00c98237102f5c7002a5669fc76327",
  },
});

```

 Available ciphers are `aegis256`, `aegis256x2`, `aegis128l`, `aegis128x2`, `aegis128x4`, `aes256gcm`, `aes128gcm`. When a database is encrypted, it is not readable as a normal SQLite database; open it through the Turso Database engine. Turso Cloud also supports bring-your-own-key encryption for cloud databases; [learn more](https://docs.turso.tech/cloud/encryption).
