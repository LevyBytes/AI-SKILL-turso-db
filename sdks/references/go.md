# Examples, Quickstart, Reference

> Sdks reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: example; Recommended: tursogo (Local + Cloud Sync); tursogo.

## example

[tursodatabase](https://docs.turso.tech/tursodatabase)  / ** [go-libsql](https://docs.turso.tech/tursodatabase/go-libsql) ** Public
- [Notifications](https://docs.turso.tech/login?return_to=%2Ftursodatabase%2Fgo-libsql) Sign in before adjusting notification preferences.
- [34 forks](https://docs.turso.tech/login?return_to=%2Ftursodatabase%2Fgo-libsql)
- [240 stars](https://docs.turso.tech/login?return_to=%2Ftursodatabase%2Fgo-libsql)

### Expand file tree

/

### example

/Copy path

### Directory actions

### More options

More options

### Directory actions

### More options

More options

### Latest commit

### History

[History](https://docs.turso.tech/tursodatabase/go-libsql/commits/main/example)History/

### example

/Copy path

### Folders and files

| Name | Name | Last commit message | Last commit date |
| --- | --- | --- | --- |
| ### parent directory.. |  |  |  |
| local | local |  |  |
| remote | remote |  |  |
| sync | sync |  |  |
| go.mod | go.mod |  |  |
| go.sum | go.sum |  |  |

## Recommended: tursogo (Local + Cloud Sync)

This Go quickstart covers how to:
- Install the Turso package
- Open a connection to either a local database or a remote database
- Execute a query using SQL
- Sync changes to the cloud

### Recommended: tursogo (Local + Cloud Sync)

 `tursogo` is the preferred package when you want a local database that can also synchronize with Turso Cloud. It runs on the Turso Database engine, a new SQLite-compatible engine designed with concurrent writes (MVCC) and async I/O. Because it provides the standard `database/sql` driver interface, it can replace other Go SQLite drivers with minimal changes. Native libraries are included, and FFI goes through [purego](https://github.com/ebitengine/purego), so CGO is not required. 1

Install

```
go get turso.tech/database/tursogo

```

2

Connect

```
package main

import (
	"database/sql"
	"fmt"

	_ "turso.tech/database/tursogo"
)

func main() {
	db, _ := sql.Open("turso", "app.db")
	defer db.Close()

	db.Exec(`CREATE TABLE IF NOT EXISTS users (
		id INTEGER PRIMARY KEY AUTOINCREMENT,
		name TEXT NOT NULL
	)`)

	db.Exec("INSERT INTO users (name) VALUES (?)", "Alice")

	rows, _ := db.Query("SELECT * FROM users")
	defer rows.Close()
	for rows.Next() {
		var id int
		var name string
		rows.Scan(&id, &name)
		fmt.Printf("User: %d %s\n", id, name)
	}
}

```

3

Sync (push and pull)

When local data must synchronize with Turso Cloud, choose the sync driver:

```
package main

import (
	"context"
	"os"

	turso "turso.tech/database/tursogo"
)

func main() {
	ctx := context.Background()

	syncDb, _ := turso.NewTursoSyncDb(ctx, turso.TursoSyncDbConfig{
		Path:      "app.db",
		RemoteUrl: os.Getenv("TURSO_DATABASE_URL"),
		AuthToken: os.Getenv("TURSO_AUTH_TOKEN"),
	})

	db, _ := syncDb.Connect(ctx)
	defer db.Close()

	db.ExecContext(ctx, "INSERT INTO users (name) VALUES (?)", "Bob")

	// Push local writes to Turso Cloud
	syncDb.Push(ctx)

	// Pull remote changes to local database
	syncDb.Pull(ctx)
}

```

Reads and writes use the local database file, which keeps access fast and usable offline. Call `Push()` to upload local updates, and call `Pull()` to retrieve changes from the remote side. The [reference](https://docs.turso.tech/sdk/go/reference) covers checkpoint behavior, stats, and encryption, while [Turso Sync](https://docs.turso.tech/sync/usage) explains conflict handling and related topics. To try sync locally without a Turso Cloud account, run a local sync server:

```
tursodb :memory: --sync-server 127.0.0.1:8080

```

After that, use `http://127.0.0.1:8080` as `RemoteUrl` and omit `AuthToken`. Installation steps for `tursodb` are in the [Turso Database quickstart](https://docs.turso.tech/tursodb/quickstart).

### Remote Access (Over-the-Wire)

 For applications that should talk to a Turso Cloud database directly over the network, such as web servers or serverless functions, use `libsql-client-go`. It reaches the database through the libSQL wire protocol without needing a local file, and it is pure Go. Most applications should instead use a synchronized local database through `NewTursoSyncDb`, because that gives faster reads, offline use, and reduced latency. Direct remote access fits cases where keeping a local database file is not possible, such as stateless serverless runtimes. 1

Retrieve database credentials

An existing database is required before continuing. If you do not have one yet, [create one](https://docs.turso.tech/quickstart). Then retrieve the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Place the credential values in `.env` environment variables.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

These values should be kept in environment variables. 2

Install

```
go get github.com/tursodatabase/libsql-client-go/libsql

```

3

Connect and query

```
package main

import (
	"database/sql"
	"fmt"
	"os"

	_ "github.com/tursodatabase/libsql-client-go/libsql"
)

func main() {
	url := os.Getenv("TURSO_DATABASE_URL") + "?authToken=" + os.Getenv("TURSO_AUTH_TOKEN")

	db, _ := sql.Open("libsql", url)
	defer db.Close()

	rows, _ := db.Query("SELECT * FROM users")
	defer rows.Close()
	for rows.Next() {
		var id int
		var name string
		rows.Scan(&id, &name)
		fmt.Printf("User: %d %s\n", id, name)
	}
}

```

### Embedded Replicas (go-libsql)

 Embedded Replicas provide a local read copy of a Turso Cloud database for a Go application. Reads come from the local replica, while writes are routed to the cloud primary and then mirrored back. Embedded Replicas are supported for production use. For new sync-focused projects, prefer `tursogo` with `NewTursoSyncDb`: reads and writes both occur locally, synchronization is triggered with `Push()` / `Pull()`, and the wire format uses logical change-data-capture instead of page frames ([benchmark](https://turso.tech/blog/sync-benchmark)). The [reference](https://docs.turso.tech/sdk/go/reference) has the complete Embedded Replicas documentation for `go-libsql`.

## tursogo

Turso offers two Go packages:
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

```
_, err = db.Exec(`CREATE TABLE IF NOT EXISTS users (
	id INTEGER PRIMARY KEY AUTOINCREMENT,
	name TEXT NOT NULL
)`)

_, err = db.Exec("INSERT INTO users (name) VALUES (?)", "Alice")

rows, err := db.Query("SELECT * FROM users")
defer rows.Close()

for rows.Next() {
	var id int
	var name string
	rows.Scan(&id, &name)
	fmt.Printf("User: %d %s\n", id, name)
}

```

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

### Sync (Push and Pull)

 Use this mode for a local database that synchronizes with the cloud. Reads and writes stay local; call `Push()` to upload local changes and `Pull()` to retrieve remote updates.

```
import (
	"context"
	"os"
	turso "turso.tech/database/tursogo"
)

ctx := context.Background()

syncDb, err := turso.NewTursoSyncDb(ctx, turso.TursoSyncDbConfig{
	Path:      "app.db",
	RemoteUrl: os.Getenv("TURSO_DATABASE_URL"),
	AuthToken: os.Getenv("TURSO_AUTH_TOKEN"),
})

db, err := syncDb.Connect(ctx)

```

 The initial run automatically seeds the local database from the remote database. See [Turso Sync](https://docs.turso.tech/sync/usage) for the complete behavior.

#### Push and Pull

```
// Push local writes to Turso Cloud
err := syncDb.Push(ctx)

// Pull remote changes to local database
changed, err := syncDb.Pull(ctx)

```

#### Checkpoint

 Reduce local WAL disk growth while keeping sync metadata intact:

```
err := syncDb.Checkpoint(ctx)

```

#### Stats

```
stats, err := syncDb.Stats(ctx)
fmt.Printf("CDC operations: %d\n", stats.CdcOperations)
fmt.Printf("Main WAL size: %d\n", stats.MainWalSize)
fmt.Printf("Network sent: %d bytes\n", stats.NetworkSentBytes)
fmt.Printf("Network received: %d bytes\n", stats.NetworkReceivedBytes)
fmt.Printf("Last pull: %d\n", stats.LastPullUnixTime)
fmt.Printf("Last push: %d\n", stats.LastPushUnixTime)
fmt.Printf("Revision: %s\n", stats.Revision)

```

### libsql-client-go (Remote)

 Use this package for **applications that reach a remote Turso Cloud database** across the network, including web servers, Docker containers, and serverless functions. It is pure Go and has no native dependency requirement.

### Installing

```
go get github.com/tursodatabase/libsql-client-go/libsql

```

### Connecting

```
import (
	"database/sql"
	"os"
	_ "github.com/tursodatabase/libsql-client-go/libsql"
)

url := os.Getenv("TURSO_DATABASE_URL") + "?authToken=" + os.Getenv("TURSO_AUTH_TOKEN")
db, err := sql.Open("libsql", url)

```

### Querying

 It works through the standard `database/sql` interface, matching `tursogo`:

```
rows, err := db.Query("SELECT * FROM users WHERE id = ?", 1)

```

### go-libsql (libSQL)

 `go-libsql` is based on [libSQL](https://github.com/tursodatabase/libsql), the open-source SQLite fork that currently underpins Turso Cloud. It is production-ready, proven in real use, and best suited to existing codebases already using `go-libsql`. With `go-libsql` Embedded Replicas, reads happen locally. Writes are forwarded to the cloud primary and then copied back to the replica. Embedded Replicas are supported for production use. For new projects that need sync, prefer `tursogo` with `NewTursoSyncDb`; see the [quickstart](https://docs.turso.tech/sdk/go/quickstart).

### Embedded Replicas

 For workloads requiring **offline writes**, **bidirectional sync**, or **multi-writer convergence**, use `tursogo` with `NewTursoSyncDb`. Reads and writes both stay local, and synchronization is controlled directly with `Push()` / `Pull()`. [embedded replicas](https://docs.turso.tech/features/embedded-replicas) can synchronize data from the remote database into a local SQLite file and send writes through the remote primary database:

```
package main

import (
  "database/sql"
  "fmt"
  "os"
  "path/filepath"

  "github.com/tursodatabase/go-libsql"
)

func main() {
    dbName := "local.db"
    primaryUrl := "libsql://[DATABASE].turso.io"
    authToken := "..."

    dir, err := os.MkdirTemp("", "libsql-*")
    if err != nil {
        fmt.Println("Error creating temporary directory:", err)
        os.Exit(1)
    }
    defer os.RemoveAll(dir)

    dbPath := filepath.Join(dir, dbName)

    connector, err := libsql.NewEmbeddedReplicaConnector(dbPath, primaryUrl,
      libsql.WithAuthToken(authToken)
    )
    if err != nil {
        fmt.Println("Error creating connector:", err)
        os.Exit(1)
    }
    defer connector.Close()

    db := sql.OpenDB(connector)
    defer db.Close()
}

```

 Embedded Replicas require file system access.

#### Manual Sync

```
if err := connector.Sync(); err != nil {
    fmt.Println("Error syncing database:", err)
}

```

#### Periodic Sync

```
syncInterval := time.Minute

connector, err := libsql.NewEmbeddedReplicaConnector(dbPath, primaryUrl,
    libsql.WithAuthToken(authToken),
    libsql.WithSyncInterval(syncInterval),
)

```

#### Read Your Writes

 After synchronization, the default behavior waits for the server to apply all of your changes before it returns, so later reads observe your own writes. That guarantee is more conservative but **much slower**, because every pending change must be processed by the server. If eventual consistency is acceptable, turn it off to make sync much faster:

```
connector, err := libsql.NewEmbeddedReplicaConnector(dbPath, primaryUrl,
    libsql.WithAuthToken(authToken),
    libsql.WithReadYourWrites(false),
)

```

### Encryption

 For new projects, choose `tursogo` for local encryption; it uses the Turso Database engine and provides stronger performance plus concurrent write support. To turn on encryption for a SQLite file, provide the encryption key value to the constructor:

```
encryptionKey := "SuperSecretKey"

connector, err := libsql.NewEmbeddedReplicaConnector(dbPath, primaryUrl,
    libsql.WithAuthToken(authToken),
    libsql.WithEncryption(encryptionKey),
)

```

 Encrypted databases look like raw bytes and are not readable as standard SQLite databases. Operations must go through the libSQL client; see [learn more](https://docs.turso.tech/libsql#encryption-at-rest).
