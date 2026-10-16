# Go query rows and cleanup

[Context index](client-connection-and-vercel-workflows.md) · [Setup and preceding context](go-local-connection-and-inserts.md)

Save this as `users.go` beside [the setup module](go-local-connection-and-inserts.md). `printUsers` uses the caller-owned connection, closes query rows, checks scan/iteration errors and returns failures to the caller.

```go
package main

import (
    "database/sql"
    "fmt"
)

func printUsers(db *sql.DB) error {
    rows, err := db.Query("SELECT id, username FROM users")
    if err != nil {
        return err
    }
    defer rows.Close()
    for rows.Next() {
        var id int
        var username string
        if err := rows.Scan(&id, &username); err != nil {
            return err
        }
        fmt.Printf("User: ID: %d, Username: %s\n", id, username)
    }
    return rows.Err()
}
```
