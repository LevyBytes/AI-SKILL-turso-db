# Experimental feature activation CLI rust python

[Context index](data-types-and-experimental-features.md) · [Continue](experimental-feature-activation-other-bindings.md)

## Experimental Features

 Certain Turso capabilities remain experimental and have to be enabled explicitly before they can be used. They are suitable for testing, but their API can change in later releases.

### Experimental Features List

| Feature | Flag | Description |
| --- | --- | --- |
| Views | `views` | CREATE VIEW plus CREATE MATERIALIZED VIEW |
| Custom Types | `custom_types` | CREATE TYPE, DROP TYPE, CREATE DOMAIN, and DROP DOMAIN support for `STRICT` tables |
| Triggers | `triggers` | CREATE TRIGGER and DROP TRIGGER |
| Encryption | `encryption` | At-rest encryption configured with PRAGMA cipher / hexkey |
| Index Methods | `index_method` | CREATE INDEX ... USING support for FTS and custom index types |
| Autovacuum | `autovacuum` | Automatic database file compaction |
| Vacuum | `vacuum` | In-place VACUUM compaction |
| Attach | `attach` | ATTACH DATABASE and DETACH DATABASE |
| Multi-Process WAL | `multiprocess_wal` | Multi-Process Access - lets OS processes share one database file through a shared WAL coordinator |

### CLI

 Start `tursodb` with `--experimental-<feature>` flags:

```
tursodb --experimental-views --experimental-triggers database.db

```

 Multiple flags can be combined:

```
tursodb \
  --experimental-views \
  --experimental-custom-types \
  --experimental-triggers \
  --experimental-encryption \
  --experimental-index-method \
  --experimental-vacuum \
  database.db

```

### Rust SDK

 The Rust binding exposes configuration through a fluent `Builder` API:

```
// Inside an async function returning a compatible Result.
use turso::Builder;

let db = Builder::new_local("database.db")
    .experimental_encryption(true)
    .experimental_triggers(true)
    .experimental_materialized_views(true)
    .experimental_custom_types(true)
    .experimental_index_method(true)
    .experimental_vacuum(true)
    .experimental_attach(true)
    .experimental_multiprocess_wal(true)
    .build().await?;

```

### Python SDK

 Provide a comma-separated string through the `experimental_features` parameter:

```
import turso

conn = turso.connect(
    "database.db",
    experimental_features="views,triggers,custom_types,vacuum",
)

```
