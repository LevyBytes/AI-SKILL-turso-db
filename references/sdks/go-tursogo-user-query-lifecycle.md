# Go tursogo user query lifecycle

Open when creating and querying a local `tursogo` database. Install the package and choose the DSN using the [connection reference](go-tursogo-connection-queries-and-encryption.md). Call `printUsers("app.db")` from your application and handle its returned error.

```go
package example

import (
    "database/sql"
    "fmt"
    _ "turso.tech/database/tursogo"
)

func printUsers(dsn string) error {
    db, err := sql.Open("turso", dsn)
    if err != nil {
        return err
    }
    defer db.Close()
    _, err = db.Exec(`CREATE TABLE IF NOT EXISTS users (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        name TEXT NOT NULL
    )`)
    if err != nil {
        return err
    }
    if _, err = db.Exec("INSERT INTO users (name) VALUES (?)", "Alice"); err != nil {
        return err
    }
    rows, err := db.Query("SELECT * FROM users")
    if err != nil {
        return err
    }
    defer rows.Close()
    for rows.Next() {
        var id int
        var name string
        if err := rows.Scan(&id, &name); err != nil {
            return err
        }
        fmt.Printf("User: %d %s\n", id, name)
    }
    return rows.Err()
}
```
