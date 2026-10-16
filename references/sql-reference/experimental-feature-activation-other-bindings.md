# Experimental feature activation other bindings

[Context index](data-types-and-experimental-features.md) · [Setup and preceding context](experimental-feature-activation-cli-rust-python.md)

### JavaScript / Node.js SDK

 Supply an array of feature strings in the `experimental` option:

```
import { connect } from "@tursodatabase/database";

const db = await connect("database.db", {
    experimental: ["views", "triggers", "custom_types", "encryption", "vacuum"],
});

```

### Go SDK

 Assign a comma-separated string to the `ExperimentalFeatures` field:

```
db, err := turso.NewDatabase(turso.TursoDatabaseConfig{
    Path: "database.db",
    ExperimentalFeatures: "views,triggers,custom_types,vacuum",
})

```

 DSN query parameters are also supported by the Go driver:

```
db, err := sql.Open("turso", "database.db?experimental=views,triggers,vacuum")

```

### Java SDK

 The Java binding currently exposes encryption with a dedicated method:

```
TursoDB db = TursoDB.openWithEncryption(
    "database.db",
    OpenFlags.CREATE,
    "aegis256",
    "your-hex-key-here"
);

```

### .NET SDK

 The .NET binding currently exposes encryption through the C FFI:

```
var db = Database.OpenWithEncryption(
    "database.db",
    "aegis256",
    "your-hex-key-here"
);

```

### See Also

- [Compatibility](https://docs.turso.tech/sql-reference/compatibility) provides the complete feature support matrix
