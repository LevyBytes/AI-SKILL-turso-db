# Python pyturso queries and encryption

[Context index](python-and-ruby-sdk-workflows.md)

## pyturso

Turso offers two Python packages:

|  | `pyturso` | `libsql` |
| --- | --- | --- |
| **Use case** | Local / embedded database, sync | Existing libSQL codebases |
| **Engine** | Turso Database (rewrite) | libSQL (SQLite fork) |
| **Concurrent writes** | Yes (MVCC) | Not supported |
| **Sync** | push/pull (local-first) | Embedded Replicas, with writes forwarded to the cloud primary |
| **API** | Python `sqlite3`-compatible | Python `sqlite3`-compatible |

 **Starting a new project?** Choose `pyturso`; it runs on the Turso Database engine and provides concurrent writes plus local-first sync.

### pyturso

 Use this for local and embedded applications. It runs on the Turso Database engine and supports concurrent writes (MVCC) plus async I/O.

### Installing

```
pip install pyturso

```

### Connecting

```
import turso

conn = turso.connect("app.db")

```

 In-memory databases are supported as well:

```
conn = turso.connect(":memory:")

```

### Querying

```
conn.execute("CREATE TABLE IF NOT EXISTS users (id INTEGER PRIMARY KEY, name TEXT)")
conn.execute("INSERT INTO users (name) VALUES (?)", ("Alice",))
conn.commit()

for row in conn.execute("SELECT * FROM users"):
    print(row)

```

### Encryption

 Use the `encryption` option to encrypt local databases at rest:

```
from turso import connect, EncryptionOpts

conn = connect("encrypted.db",
               experimental_features="encryption",
               encryption=EncryptionOpts(cipher="aegis256",
                                         hexkey="b1bbfda4f589dc9daaf004fe21111e00dc00c98237102f5c7002a5669fc76327"))

```

 Supported ciphers are `aegis256`, `aegis256x2`, `aegis128l`, `aegis128x2`, `aegis128x4`, `aes256gcm`, and `aes128gcm`. A standard SQLite reader cannot open encrypted databases; open them with the Turso Database engine instead. Turso Cloud databases also support bring-your-own-key encryption - [learn more](https://docs.turso.tech/cloud/encryption).
