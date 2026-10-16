# Go remote query program

Use the credentials and package installation in [remote setup](go-remote-libsql-quickstart.md). This complete program preserves any existing URL query parameters while encoding the auth token.

```go
package main

import (
    "database/sql"
    "fmt"
    "log"
    "net/url"
    "os"
    _ "github.com/tursodatabase/libsql-client-go/libsql"
)

func main() {
    if err := run(); err != nil { log.Fatal(err) }
}

func run() error {
    address, token := os.Getenv("TURSO_DATABASE_URL"), os.Getenv("TURSO_AUTH_TOKEN")
    if address == "" || token == "" { return fmt.Errorf("missing database credentials") }
    parsed, err := url.Parse(address)
    if err != nil { return err }
    parameters := parsed.Query()
    parameters.Set("authToken", token)
    parsed.RawQuery = parameters.Encode()
    db, err := sql.Open("libsql", parsed.String())
    if err != nil { return err }
    defer db.Close()
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
