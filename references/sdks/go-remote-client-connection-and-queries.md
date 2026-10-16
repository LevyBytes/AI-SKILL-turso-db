# Go remote client connection and queries

[Context index](go-sdk-workflows.md)

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
