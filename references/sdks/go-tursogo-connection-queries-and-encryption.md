# Go tursogo connection queries and encryption

[Context index](go-sdk-workflows.md) · [Continue](go-tursogo-sync-and-checkpoint-api.md)

## tursogo

Turso offers three Go packages:

|  | `tursogo` | `libsql-client-go` | `go-libsql` |
| --- | --- | --- | --- |
| **Use case** | Local / embedded database, sync | Remote access (over-the-wire) | Existing libSQL codebases |
| **Engine** | Turso Database (rewrite) | libSQL wire protocol | libSQL (SQLite fork) |
| **Concurrent writes** | Yes (MVCC) | N/A (remote) | Not supported |
| **Sync** | push/pull (local-first) | — | Embedded Replicas: writes are forwarded to the cloud primary |
| **CGO** | Not required | Not required | Required |
| **API** | `database/sql` | `database/sql` | `database/sql` |

 **Starting a new project?** Choose `tursogo` for local, embedded, or sync-oriented use. Choose `libsql-client-go` for remote access. Both avoid CGO.

### tursogo

 Use this for local and embedded scenarios. It runs on the Turso Database engine, which supports concurrent writes (MVCC) and async I/O.

### Installing

```
go get turso.tech/database/tursogo

```

### Connecting

These are alternatives inside a function returning `error`. Check `err` immediately after `sql.Open`; defer `db.Close()` only after success. The query reference below provides the complete lifecycle.

```
import (
	"database/sql"
	_ "turso.tech/database/tursogo"
)

db, err := sql.Open("turso", "app.db")

```

 In-memory databases can be used too:

```
db, err := sql.Open("turso", ":memory:")

```

### Querying

Open the [complete query function](go-tursogo-user-query-lifecycle.md) for schema creation, insertion, scanning and error handling.

### Encryption

 Use DSN options to encrypt local databases at rest:

```
import (
	"database/sql"
	"fmt"
	_ "turso.tech/database/tursogo"
)

hexkey := "b1bbfda4f589dc9daaf004fe21111e00dc00c98237102f5c7002a5669fc76327"
dsn := fmt.Sprintf("encrypted.db?experimental=encryption&encryption_cipher=aegis256&encryption_hexkey=%s", hexkey)

db, err := sql.Open("turso", dsn)

```

 Available ciphers are `aegis256`, `aegis256x2`, `aegis128l`, `aegis128x2`, `aegis128x4`, `aes256gcm`, and `aes128gcm`. Standard SQLite tools cannot open encrypted databases; they must be opened with the Turso Database engine. Turso Cloud also supports bring-your-own-key encryption; see [learn more](https://docs.turso.tech/cloud/encryption).
