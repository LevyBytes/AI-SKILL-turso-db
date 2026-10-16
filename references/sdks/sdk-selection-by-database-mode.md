# SDK selection by database mode

[Context index](http-api-and-package-selection.md)

## Which package should I use?

| Use case | TypeScript | Python | Go | Rust |
| --- | --- | --- | --- | --- |
| **Local database** (embedded, device-local, offline) | `@tursodatabase/database` | `pyturso` | `tursogo` | `turso` |
| **Local database + cloud sync** (push/pull) | `@tursodatabase/sync` | `pyturso` (with sync) | `tursogo` (with sync) | `turso` (with `sync` feature) |
| **Remote access** (servers, Docker, serverless, edge - any networked connection) | `@tursodatabase/serverless` | `libsql` | `libsql-client-go` | `libsql` (with `remote` feature) |
| **libSQL** — ORM support (Drizzle, Prisma), production-ready, battle-tested | `@libsql/client` | `libsql` | `go-libsql` | `libsql` |

 **Starting a new project?** For TypeScript, Python, Go, and Rust, begin with `@tursodatabase/database`, `pyturso`, `tursogo`, or `turso`. Those packages run on the Turso Database engine, a SQLite-compatible rewrite that provides concurrent writes, async I/O, and local-first sync. **Need sync?** Choose [Turso Sync](https://docs.turso.tech/sync/usage) when reads and writes should happen locally and cloud exchange should be explicit through `push()` / `pull()`. **Using an ORM?**
- **TypeScript** - Drizzle and Prisma use `@libsql/client`, and the major ORMs in the ecosystem provide production-ready support.
- **Rust** - [Toasty](https://docs.turso.tech/sdk/rust/orm/toasty), Tokio project's async ORM, includes a native Turso driver based on the `turso` crate.
- **Python** - refer to the [SQLAlchemy guide](https://docs.turso.tech/sdk/python/orm/sqlalchemy).
 **Migrating from SQLite?** Turso Database can replace SQLite without changing existing SQL, schema, or queries. **Already using `@libsql/client`, `libsql`, or `go-libsql`?** These packages use [libSQL](https://github.com/tursodatabase/libsql), the open-source SQLite fork behind Turso Cloud today, and are mature production options. If the workload requires concurrent writes, push/pull sync, or local-first writes for offline, multi-writer, or bidirectional scenarios, the Turso Database packages may fit better; choose based on workload needs.

### Why multiple packages in TypeScript?

 Keeping `@tursodatabase/database` separate from `@tursodatabase/serverless` keeps bundles small. Because `@tursodatabase/serverless` depends only on `fetch` and has no native dependency, it can target Node.js, Docker containers, edge runtimes, browsers, and serverless functions. **Need sync?** New projects should use [Turso Sync](https://docs.turso.tech/sync/usage) when they want local reads and writes with deliberate `push()` / `pull()` cloud exchange. The libSQL-based SDKs below can also use Embedded Replicas, where reads happen locally and writes go to the cloud primary.

### Official SDKs

### TypeScript

### Rust

### Go

### Python

### Android

### Swift

### PHP

### Ruby

### Ruby on Rails

### C
