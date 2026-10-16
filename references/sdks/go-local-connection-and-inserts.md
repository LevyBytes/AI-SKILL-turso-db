# Go local connection and inserts

[Context index](client-connection-and-vercel-workflows.md) · [Continue](go-query-rows-and-cleanup.md)

Run `go get turso.tech/database/tursogo` in a Go module. Save this as `main.go` and place the complete [user query function](go-query-rows-and-cleanup.md) in `users.go` in the same directory, then run the package with `go run .`.

```go
package main

import (
    "database/sql"
    "log"
    _ "turso.tech/database/tursogo"
)

func main() {
    if err := run(); err != nil {
        log.Fatal(err)
    }
}

func run() error {
    db, err := sql.Open("turso", "sqlite.db")
    if err != nil {
        return err
    }
    defer db.Close()
    _, err = db.Exec(`CREATE TABLE IF NOT EXISTS users (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        username TEXT NOT NULL
    )`)
    if err != nil {
        return err
    }
    for _, username := range []string{"alice", "bob"} {
        if _, err = db.Exec("INSERT INTO users (username) VALUES (?)", username); err != nil {
            return err
        }
    }
    return printUsers(db)
}
```
