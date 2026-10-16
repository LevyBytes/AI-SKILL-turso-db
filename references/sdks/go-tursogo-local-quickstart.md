# Go tursogo local quickstart

[Context index](go-sdk-workflows.md) · [Continue](go-tursogo-cloud-sync-quickstart.md)

## Recommended: tursogo (Local + Cloud Sync)

This Go quickstart covers how to:
- Install the Turso package
- Open a connection to either a local database or a remote database
- Execute a query using SQL
- Sync changes to the cloud

### Recommended: tursogo (Local + Cloud Sync)

 `tursogo` is the preferred package when you want a local database that can also synchronize with Turso Cloud. It runs on the Turso Database engine, a new SQLite-compatible engine designed with concurrent writes (MVCC) and async I/O. Because it provides the standard `database/sql` driver interface, it can replace other Go SQLite drivers with minimal changes. Native libraries are included, and FFI goes through [purego](https://github.com/ebitengine/purego), so CGO is not required.

### Step 1: Install

```
go get turso.tech/database/tursogo

```

### Step 2: Connect

```go
package main

import (
    "database/sql"
    "fmt"
    "log"
    _ "turso.tech/database/tursogo"
)

func main() {
    if err := run(); err != nil { log.Fatal(err) }
}

func run() error {
    db, err := sql.Open("turso", "app.db")
    if err != nil { return err }
    defer db.Close()
    _, err = db.Exec(`CREATE TABLE IF NOT EXISTS users (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        name TEXT NOT NULL
    )`)
    if err != nil { return err }
    if _, err = db.Exec("INSERT INTO users (name) VALUES (?)", "Alice"); err != nil {
        return err
    }
    rows, err := db.Query("SELECT id, name FROM users")
    if err != nil { return err }
    defer rows.Close()
    for rows.Next() {
        var id int
        var name string
        if err := rows.Scan(&id, &name); err != nil { return err }
        fmt.Printf("User: %d %s\n", id, name)
    }
    return rows.Err()
}
```
