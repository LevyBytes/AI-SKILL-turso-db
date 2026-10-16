# Database token scopes and permissions

[Context index](activerecord-and-authentication-workflows.md) · [Continue](database-role-permission-examples.md)

## Scoping Levels

Turso controls database access with scoped JWT-based tokens. A token may limit database access, permission level, and expiration, so each client receives only the access you define.

### Scoping Levels

 Token scope can be set at several levels, ordered from widest to narrowest:

| Level | Scope | How to create |
| --- | --- | --- |
| **Group** | Permit access to every database in a group | `turso group tokens create <group>` |
| **Database** | Access a single database | `turso db tokens create <database>` |
| **Read-only** | Queries only, no writes | Add `--read-only` flag |
| **Table + Action** | Specific tables and operations | Add `-p <table>:<actions>` flag |
| **Time-limited** | Auto-expires after a duration | Add `--expiration 7d` flag |

 Scopes can be layered. For example, this creates a read-only token for one database with a 7-day lifetime:

```
turso db tokens create mydb --read-only --expiration 7d

```

 Another option is a token that permits reads on every table and inserts only into `comments`:

```
turso db tokens create mydb \
  -p all:data_read \
  -p comments:data_add

```

### Issuing Tokens

 Tokens can be issued in either of these ways:
- **[Platform Tokens](https://docs.turso.tech/sdk/authorization/tokens)** - Generate tokens directly with the Turso CLI or Platform API.
- **[External Auth Providers](https://docs.turso.tech/sdk/authorization/jwks)** - Have an authentication provider, such as Clerk or Auth0, create tokens through JWKS.
 Both options can use [fine-grained permissions](https://docs.turso.tech/sdk/authorization/fine-grained-permissions) for table-level and action-level access control.

### Using Tokens

 When constructing a database client, provide any token through `authToken`:

```
import { connect } from "@tursodatabase/serverless";

const db = connect({
  url: "<your-database-url>",
  authToken: "<your-token>",
});

```

 Run `turso db show <database-name> --url` to retrieve the database URL.

## Permission Format

Fine-grained permissions define table-level and action-level access. They are supported for both [CLI-generated tokens](https://docs.turso.tech/sdk/authorization/tokens) and [JWKS-issued tokens](https://docs.turso.tech/sdk/authorization/jwks).

### Permission Format

 Permissions use the pattern `<table-name|all>:<action1>,<action2>`:

```
# Read-only access to all tables
turso db tokens create mydb -p all:data_read

# Specific actions on a specific table
turso db tokens create mydb -p users:data_read,data_update

# Multiple permission rules
turso db tokens create mydb \
  -p all:data_read \
  -p comments:data_add,data_update \
  -p posts:data_add,data_update,data_delete

```

 Set the table name to `all` when the permission should cover every table.

### Available Actions

| Action | Description |
| --- | --- |
| `data_read` | Read data from tables |
| `data_add` | Insert new rows |
| `data_update` | Update existing rows |
| `data_delete` | Delete rows |
| `schema_add` | Create new tables |
| `schema_update` | Modify table schemas |
| `schema_delete` | Drop tables |

 By default, `data_read` is permitted for SQLite system tables, such as `sqlite_master` and `sqlite_schema`, so users can inspect database metadata.
